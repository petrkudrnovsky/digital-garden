
> [!tldr] First 5 minutes of hell
> 3 MPI features:
> - `MPI_Cart_create` - create a virtual cartesian topology
> 	- MPI can map the standard `MPI_COMM_WORLD` ranks to coordinates of a 2D torus and provide the communication primitives, that respect this structure
> 	- result is a new communicator with processes that are aware of their new 2D coordinates
> 	- helper functions:
> 		- `MPI_Cart_coords` - coordinations of the process
> 		- `MPI_Cart_rank` - new rank of the process
> - `MPI_Cart_shift` - shift along a cartesian dimension
> 	- this function computes the source and the destination ranks for the shift
> - `MPI_Sendrecv_replace` - performs the actual exchange in-place (sends one submatrix and receive another at the same time)
> 	- this actually performs the shift operation (using the coordinates from the previous function)
> - `MPI_Comm_free` must be called in the end to free up the virtual communicators (otherwise, MPI will leak resources)
> - the local matrix multiply could be done by a plain sequential `SeqMatrixMultiply`, optionally it could also be OpenMP multithreaded


## Problem definition

Implement Cannon's algorithm for dense MMM $C = A \times B$ in MPI, given:

- Square dense matrices $A$, $B$, $C$ of size $(\sqrt{N} \times \sqrt{N})$.
- $p$ MPI processes arranged in a virtual 2-D torus $K(\sqrt{p}, \sqrt{p})$.
- Block checkerboard mapping: each process initially holds one $(\sqrt{N/p} \times \sqrt{N/p})$-submatrix of each of $A$, $B$, $C$. For a refresher on the algorithm itself (correctness, properties, time complexity) see Exam Question 49. The MPI implementation rests on three MPI features:
- Virtual Cartesian topology, created with `MPI_Cart_create`, which lets MPI map the standard `MPI_COMM_WORLD` ranks to coordinates of a 2-D torus and provides convenient communication primitives that respect this structure.
- Cyclic shift along a Cartesian dimension via `MPI_Cart_shift` (which computes the source and destination ranks for a shift) together with `MPI_Sendrecv_replace` (which performs the actual data exchange in-place).
- A local sequential matrix-matrix multiplication routine for the per-step submatrix product.

## Why a torus and why MPI Cartesian topology

Cannon's algorithm performs cyclic shifts of $A$ submatrices along rows (leftward, wrapping around) and cyclic shifts of $B$ submatrices along columns (upward, wrapping around). These are exactly the primitives a 2-D torus supports natively. MPI provides direct support for declaring such a virtual topology: `MPI_Cart_create` takes a base communicator, the number of dimensions, the size in each dimension, a periodicity flag per dimension (whether the dimension wraps around), and a reorder flag. The result is a new communicator in which the processes are aware of their 2-D coordinates. The benefit of this is twofold. First, the rank-to-coordinate and coordinate-to-rank conversions are handled by MPI helper functions (`MPI_Cart_coords`, `MPI_Cart_rank`). Second, communication primitives like `MPI_Cart_shift` can compute the source and destination of a cyclic shift along a specified dimension without the programmer manually doing modular arithmetic over $\sqrt{p}$.

## Key MPI functions used

- `MPI_Cart_create(comm, ndims, dims, periods, reorder, &new_comm)` - creates a new communicator with a Cartesian (toroidal) structure. For Cannon: `ndims = 2`, `dims = {sqrt(p), sqrt(p)}`, `periods = {1, 1}` (both dimensions periodic, i.e., a torus), `reorder = 1` (MPI may renumber ranks for efficiency).
- `MPI_Cart_coords(cart_comm, rank, ndims, coords)` - returns the 2-D coordinates of a given rank in the Cartesian communicator.
- `MPI_Cart_shift(cart_comm, dim, displacement, &source, &dest)` - computes source and destination ranks for a cyclic shift of `displacement` positions along dimension `dim`. Negative displacement means leftward/upward.
- `MPI_Sendrecv_replace(buf, count, datatype, dest, sendtag, source, recvtag, comm, &status)` - sends data from `buf` to `dest` and replaces it with data received from `source`, in a single combined operation. Used to perform the actual shift after `MPI_Cart_shift` has computed the source and destination.
- `MPI_Comm_free(&comm)` - releases the Cartesian communicator at the end. The combination `MPI_Cart_shift` + `MPI_Sendrecv_replace` is the canonical MPI idiom for cyclic shifts on a Cartesian communicator.

## Auxiliary variables

A typical implementation declares the following per process:

- `nlocal` - size of one side of the local submatrix, i.e., $\sqrt{N/p}$. Computed as `n / dims[0]` where `n = sqrt(N)`.
- `p` - total number of processes, obtained via `MPI_Comm_size`.
- `dims[2]` - dimensions of the 2-D Cartesian topology, set to `{sqrt(p), sqrt(p)}`.
- `periods[2]` - periodicity flags, set to `{1, 1}` for a torus.
- `myrank` - standard rank in the original `comm`.
- `my2Drank` - rank in the new 2-D toroidal communicator.
- `mycoords[2]` - this process's $(i, j)$ coordinates in the 2-D torus.
- `shiftsource`, `shiftdest` - source and destination ranks for the prologue shifts (variable displacement) and for restoration.
- `uprank`, `downrank`, `leftrank`, `rightrank` - the ranks of the four immediate neighbors in the torus, used in the main loop for unit shifts.
- `comm_2d` - the new Cartesian communicator.
- `status` - an `MPI_Status` object for the receive part of `MPI_Sendrecv_replace`.

## Part I: communicator setup

```c
CannonMMM(int n, double *A, double *B, double *C, MPI_Comm comm)
{
    int i;
    int nlocal;
    int p;
    int dims[2];
    int periods[2];
    int myrank;
    int my2Drank;
    int mycoords[2];
    int shiftsource, shiftdest;
    int uprank, rightrank, leftrank, downrank;
    int coords[2];
    MPI_Status status;
    MPI_Comm comm_2d;

    MPI_Comm_size(comm, &p);
    MPI_Comm_rank(comm, &myrank);

    dims[0] = dims[1] = sqrt(p);
    periods[0] = periods[1] = 1;   // toroidal: both dimensions wrap around
    MPI_Cart_create(comm, 2, dims, periods, 1, &comm_2d);  // 1 = reorder

    MPI_Comm_rank(comm_2d, &my2Drank);
    MPI_Cart_coords(comm_2d, my2Drank, 2, mycoords);

    nlocal = n / dims[0];
    ...
```

After `MPI_Cart_create`, each process has a new 2-D-aware rank (`my2Drank`) and a pair of coordinates `mycoords = (i, j)`. The local submatrix size `nlocal` is computed from `n` and the row count of the topology.

## Part II: prologue (initial skew of A and B)

The prologue performs the asymmetric row/column rotations:

- Row $i$ of $A$ submatrices is rotated by $i$ positions leftward.
- Column $j$ of $B$ submatrices is rotated by $j$ positions upward. `MPI_Cart_shift` computes which ranks to send to and receive from, given the dimension and the displacement. The displacement is negated because the rotation direction is "leftward" (decreasing column index) for $A$ and "upward" (decreasing row index) for $B$.

```c
    // Rotate A submatrix in row i by i positions leftward.
    // Dimension 0 = horizontal (row dimension), displacement = -mycoords[0].
    MPI_Cart_shift(comm_2d, 0, -mycoords[0], &shiftsource, &shiftdest);
    MPI_Sendrecv_replace(A, nlocal*nlocal, MPI_DOUBLE,
                         shiftdest, 1, shiftsource, 1, comm_2d, &status);

    // Rotate B submatrix in column j by j positions upward.
    // Dimension 1 = vertical (column dimension), displacement = -mycoords[1].
    MPI_Cart_shift(comm_2d, 1, -mycoords[1], &shiftsource, &shiftdest);
    MPI_Sendrecv_replace(B, nlocal*nlocal, MPI_DOUBLE,
                         shiftdest, 1, shiftsource, 1, comm_2d, &status);
```

Note that for each process the displacement is `-mycoords[0]` or `-mycoords[1]`, so different processes shift by different amounts - this is the prologue's asymmetric skew. After the prologue, the four neighbor ranks for the unit shifts of the main loop are precomputed once:

```c
    // Neighbor ranks for unit shifts in the main loop.
    MPI_Cart_shift(comm_2d, 0, -1, &rightrank, &leftrank);
    MPI_Cart_shift(comm_2d, 1, -1, &downrank, &uprank);
```

These are constant for all main-loop iterations. Note the naming: with displacement $-1$, the source of incoming data is `rightrank` (in dimension 0) or `downrank` (in dimension 1), and the destination is `leftrank` (in dimension 0) or `uprank` (in dimension 1).

## Part III: main loop

The main loop runs $\sqrt{p} = $ `dims[0]` iterations. Each iteration performs a local multiply-accumulate and then a unit shift of $A$ leftward and $B$ upward:

```c
    for (i = 0; i < dims[0]; i++) {
        SeqMatrixMultiply(nlocal, A, B, C);    // C = C + A * B

        // Rotate A horizontally by 1 position (leftward).
        MPI_Sendrecv_replace(A, nlocal*nlocal, MPI_DOUBLE,
                             leftrank, 1, rightrank, 1, comm_2d, &status);

        // Rotate B vertically by 1 position (upward).
        MPI_Sendrecv_replace(B, nlocal*nlocal, MPI_DOUBLE,
                             uprank, 1, downrank, 1, comm_2d, &status);
    }
```

The local `SeqMatrixMultiply` is just a standard sequential submatrix product (a cubic three-loop multiply on local memory). It can be further OpenMP-parallelized within each process if desired - this is one place where MPI+OpenMP cooperation pays off. At the end of $\sqrt{p}$ iterations, each process holds the final $C_{i,j}$ in its local $C$ buffer.

## Part IV: restoring the original mapping and cleanup

After the main loop, the $A$ and $B$ submatrices are no longer at their original locations (they have been shifted through the torus). To return the system to its initial checkerboard layout, the inverse rotations are applied: row $i$ of $A$ is rotated rightward by $i$, and column $j$ of $B$ is rotated downward by $j$. This is achieved by the same `MPI_Cart_shift` + `MPI_Sendrecv_replace` pattern, but with positive displacements:

```c
    // Restore original checkerboard mapping of A: rotate row i by i rightward.
    MPI_Cart_shift(comm_2d, 0, +mycoords[0], &shiftsource, &shiftdest);
    MPI_Sendrecv_replace(A, nlocal*nlocal, MPI_DOUBLE,
                         shiftdest, 1, shiftsource, 1, comm_2d, &status);

    // Restore original checkerboard mapping of B: rotate column j by j downward.
    MPI_Cart_shift(comm_2d, 1, +mycoords[1], &shiftsource, &shiftdest);
    MPI_Sendrecv_replace(B, nlocal*nlocal, MPI_DOUBLE,
                         shiftdest, 1, shiftsource, 1, comm_2d, &status);

    MPI_Comm_free(&comm_2d);    // release the Cartesian communicator
}
```

The restoration shifts differ from the prologue shifts only in sign: $+\texttt{mycoords[0]}$ instead of $-\texttt{mycoords[0]}$, and likewise for the column dimension. The Cartesian communicator is then explicitly freed.

> Cleanup matters: virtual communicators created with `MPI_Cart_create` are not automatically released. `MPI_Comm_free` must be called explicitly, otherwise MPI may leak resources.

## How the MPI idioms map to the algorithm

- The torus topology of Cannon's algorithm $\leftrightarrow$ `MPI_Cart_create` with `periods = {1, 1}`.
- Cyclic shift of $A$ submatrices leftward in row $i$ $\leftrightarrow$ `MPI_Cart_shift(comm_2d, 0, displacement, ...)` + `MPI_Sendrecv_replace`.
- Cyclic shift of $B$ submatrices upward in column $j$ $\leftrightarrow$ `MPI_Cart_shift(comm_2d, 1, displacement, ...)` + `MPI_Sendrecv_replace`.
- The asymmetric prologue (different shift amount per process) $\leftrightarrow$ displacement varies per process, computed from `mycoords[0]` or `mycoords[1]`.
- The regular main loop (unit shift everywhere) $\leftrightarrow$ neighbor ranks computed once, displacement $\pm 1$, then reused in every iteration.
- Local submatrix multiply $\leftrightarrow$ a plain sequential `SeqMatrixMultiply`, optionally OpenMP-multithreaded.

## Potential exam questions

- What MPI feature does Cannon's algorithm map naturally onto, and which MPI function creates this virtual topology? List its arguments and explain what each controls.
- Why is `periods[0] = periods[1] = 1` essential for the Cartesian communicator in the Cannon implementation? What would change if `periods` were set to `{0, 0}`?
- Explain the role of `MPI_Cart_shift`. It does not actually move data; what does it produce, and which MPI function then performs the data movement?
- Describe the function `MPI_Sendrecv_replace` and explain why it is preferred over a pair of `MPI_Send` and `MPI_Recv` calls for the shift operations in Cannon's algorithm.
- Write the MPI code that, given `mycoords` and `comm_2d`, performs the prologue rotation of $A$ (row $i$ leftward by $i$ positions). Why is the displacement passed to `MPI_Cart_shift` negative?
- After the prologue, the neighbor ranks for unit shifts are computed once and reused throughout the main loop. Write the two `MPI_Cart_shift` calls that compute `leftrank`, `rightrank`, `uprank`, `downrank`. Why is this done outside the loop?
- Write the body of the main loop of the Cannon MPI implementation: local multiply, shift $A$ leftward by 1, shift $B$ upward by 1. How many iterations does the loop run, and how is that count expressed in the code?
- Why are the displacements $-\texttt{mycoords[0]}$ and $-\texttt{mycoords[1]}$ used in the prologue, but $+\texttt{mycoords[0]}$ and $+\texttt{mycoords[1]}$ in the restoration phase? What does this say about the symmetry of the shifts?
- What is the purpose of the final restoration step (rotating $A$ rightward by $i$ and $B$ downward by $j$)? When would you skip it, and when is it necessary?
- Why is `MPI_Comm_free(&comm_2d)` important at the end of the function?
- The `SeqMatrixMultiply` call is the only place where significant local computation happens. How can it be further parallelized within an MPI process, and what would this look like in a hybrid MPI+OpenMP implementation?
- Trace through the values of `shiftsource` and `shiftdest` produced by `MPI_Cart_shift(comm_2d, 0, -2, ...)` for the process at `mycoords = (1, 0)` on a $4 \times 4$ torus. (Apply the formula for cyclic shifts modulo $\sqrt{p}$.)
- Sketch the entire MPI implementation of Cannon's algorithm from `MPI_Cart_create` to `MPI_Comm_free`, identifying each phase (setup, prologue, main loop, restoration, cleanup).