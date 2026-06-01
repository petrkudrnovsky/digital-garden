
> [!tldr] First 5 minutes in hell
> Problem of the arbitrary and row-wise mapping is that that both approaches still require the whole $x$ vector to be loaded in each process. This is solved by cutting the row-wise mapping (which already contributes to $n/p$ parts of $y$ vector) into columns => producing a 2D mesh of processes, each owning $n/\sqrt{p}$ elements of $x$ and $n/\sqrt{p}$ elements of $y$.
> - each process touches only a chunk of columns (therefore it only needs a (corresponding) chunk of $x$)
> - so the memory shrinks as I add more processes
> 
> One iteration:
> - diagonal processes broadcast their column chunk along the whole column ("here is the chunk of $x$, what you need")
> - convergence test
> - all processes do a local multiplication of their tile with the chunk they got from their same-column-on-diagonal process
> - each row reduces (sums) it's results back to its diagonal process
> - diagonal processes compute the norm $\alpha$ (since all together, they have all pieces of $y$ vector), then they normalize the vector $x$ and the cycle restarts
> 	- `MPI_Allreduce` - each diagonal process gets the $\alpha$ value
> 	- and they will divide their $y$ chunk with this $\alpha$ to normalize it
> - the the normalized block of $x$ is again broadcasted along the columns (this is actually the first step)
> 
> Three subcommunicators (to perform multiple and disjoint collective operations (like broadcasts) on different sets of processes without interfering) 
> - vcomm = columns, for the broadcasting
> - hcomm = rows, for the sum
> - dcomm = the diagonal only, for computing the norm
> 
> They are created by the `MPI_Comm_split`
> 
> Convergence speed is data-dependent, the number of iterations varies per matrix. We want to understand the structure of nonzeros to ensure the uniform load.
> 
> Checkerboard mapping wins on every metric: lowest per-process memory, lowest communication latency per iteration. Its only cost is implementation complexity: three sub-communicators must be created and managed, and the diagonal-vs-non-diagonal asymmetry in the inner loop must be handled carefully.
> 
> Complexity:
> Both arrays `x` and `y` are of size $m = n/\sqrt{p}$. Compared to the other mappings:
> - Arbitrary: $x$ size $n$, $y$ size $n$
> - Row-wise: $x$ size $n$, $y$ size $n/p$
> - Checkerboard: $x$ size $n/\sqrt{p}$, $y$ size $n/\sqrt{p}$. For large $p$, checkerboard has by far the smallest memory footprint - the only one whose per-process memory shrinks as $p$ grows.
     

![[Pasted image 20260525123815.png]]
## Recap: the Power Method and its distributed structure

The Power Method finds the largest eigenvalue $\lambda$ (in absolute value) and the corresponding eigenvector $\vec{v}$ of a matrix $A$, satisfying $A\vec{v} = \lambda\vec{v}$. The eigenvector is typically normalized. The algorithm has six phases:

- Ph.1: take any nonzero initial vector $\vec{x}$.
- Ph.2: compute $\vec{y} \leftarrow A\vec{x}$ (the MVM).
- Ph.3: compute the norm $\alpha \leftarrow |\vec{y}|_2$.
- Ph.4: replace $\vec{x}$ with the normalized $\vec{y}$, i.e., $\vec{x} \leftarrow \vec{y}/\alpha$.
- Ph.5: evaluate the convergence criterion.
- Ph.6: if satisfied, terminate; otherwise go to Ph.2. In a distributed implementation, $A$ is partitioned among $p$ MPI processes as $$ A = A^{(0)} + A^{(1)} + \cdots + A^{(p-1)}, $$ so Ph.2 becomes $p$ local MVMs followed by a parallel reduction. The specific structure depends on the matrix mapping. The arbitrary and block row-wise mappings are covered in Exam Question 51. This note covers the third option: checkerboard mapping. The motivation for checkerboard mapping is to reduce both the per-process memory and the per-iteration communication volume. Where arbitrary mapping needs $O(n)$ memory for both $\vec{x}$ and $\vec{y}$, and row-wise needs $O(n)$ for $\vec{x}$ and $O(n/p)$ for $\vec{y}$, checkerboard mapping needs only $O(n/\sqrt{p})$ for both.

## Checkerboard mapping: the setup

Processes form a virtual 2-D mesh $M(\sqrt{p}, \sqrt{p})$. Process $P_i$ (with linear rank $i$ in `MPI_COMM_WORLD`) has:

- row index $I = \lfloor i / \sqrt{p} \rfloor$,
- column index $J = i \bmod \sqrt{p}$. Process $P_{I,J}$ holds a $(n/\sqrt{p} \times n/\sqrt{p})$-submatrix of $A$. For the input/output vectors, the natural convention used here is: $\vec{x}$ and $\vec{y}$ are mapped to the diagonal processes (those with $I = J$). Each diagonal process holds a block of $n/\sqrt{p}$ consecutive elements of the vector. Why this is more efficient:
- Each process in column $J$ needs access only to the part of $\vec{x}$ indexed by $x_{J \cdot n/\sqrt{p}}, \ldots, x_{(J+1) \cdot n/\sqrt{p} - 1}$. The local nonzeros of $A_{I,J}$ only reference column indices in this range. Memory complexity: $O(n/\sqrt{p})$ per process.
- After local MVMs, processes in the same row $I$ have local contributions to the same block of $\vec{y}$ (corresponding to row indices $I \cdot n/\sqrt{p}, \ldots, (I+1) \cdot n/\sqrt{p} - 1$). These must be reduced within row $I$.
- The most natural mapping of $\vec{y}$ is back to the diagonal processes ($I = J$): each row $I$ has its reduction root at the diagonal process $P_{I,I}$.

## Visual structure of one iteration

The four conceptual stages of one Power Method iteration under checkerboard mapping:

- Initial $\vec{x}$ lives on the diagonal processes only.
- Column OABs: each diagonal $P_{J,J}$ broadcasts its block of $\vec{x}$ to all other processes in column $J$.
- Local MVMs: every $P_{I,J}$ multiplies its local submatrix by its received subvector.
- Row reductions: each row $I$ performs a parallel sum-reduction into the diagonal process $P_{I,I}$, producing the new $\vec{y}$ block there.
- Normalization: diagonal processes cooperate (via a separate reduction over their squared norms) to compute the global norm $\alpha$, then scale their local blocks of $\vec{y}$.

## Sub-communicators: hcomm, dcomm, vcomm

The key MPI technique enabling the checkerboard implementation is the use of sub-communicators - groups of processes that participate in collective operations together, disjoint from other groups. MPI provides `MPI_Comm_split` to create these. `MPI_Comm_split(comm, color, key, &newcomm)` decomposes a base communicator into disjoint sub-communicators: every process with the same `color` value ends up in the same new communicator, and within each new communicator the processes are ranked according to their `key` values. The checkerboard Power Method needs three sub-communicators per process:

- `hcomm` (horizontal communicator) - groups all processes in the same row $I$. Used for the row-wise reductions of MVM partial results into the diagonal process. Color: $I$.
- `dcomm` (diagonal communicator) - groups all diagonal processes ($I = J$) into one communicator, and all non-diagonal processes into another. Used so the diagonal processes can cooperatively compute the global norm of $\vec{y}$. Color: the boolean expression `I == J` (so all `true`-valued processes form one communicator and all `false`-valued ones form another, but only the `true` one is used).
- `vcomm` (vertical communicator) - groups all processes in the same column $J$. Used to broadcast the new normalized $\vec{x}$ block from each diagonal process $P_{J,J}$ to the rest of column $J$. Color: $J$.

> Sub-communicators let MPI perform multiple, disjoint, simultaneous collective operations - all rows reduce in parallel without interfering with each other.

## Creating the sub-communicators

```c
int p, r;
MPI_Comm_size(MPI_COMM_WORLD, &p);   // total # of processes
MPI_Comm_rank(MPI_COMM_WORLD, &r);   // current process rank

int q = 1;
while (q < p) { if (p == q * q) break; q++; }   // q = integer sqrt(p)
int I = r / q;        // process row index
int J = r % q;        // process column index

MPI_Comm hcomm, dcomm, vcomm;
MPI_Comm_split(MPI_COMM_WORLD, I,       r, &hcomm);   // row reductions
MPI_Comm_split(MPI_COMM_WORLD, I == J,  r, &dcomm);   // diagonal-only reductions
MPI_Comm_split(MPI_COMM_WORLD, J,       r, &vcomm);   // column broadcasts
```

Within each sub-communicator the processes have new ranks, numbered from $0$ to $\sqrt{p} - 1$:

- In `hcomm`, the diagonal process of row $I$ has rank $I$ (because it has column index $J = I$ in the original 2-D mesh, and within the row the ranking inherits column indices).
- In `vcomm`, the diagonal process of column $J$ has rank $J$.
- In `dcomm`, the ordering among diagonal processes is by their original rank.

## The main iteration loop: structure

For each iteration:

1. Local MVM: each process computes its local contribution $\vec{y}^{(I,J)} = A^{(I,J)} \vec{x}^{(J)}$.
2. Row reduction: in each row $I$, all $\sqrt{p}$ processes reduce their local $\vec{y}^{(I,*)}$ into the diagonal process $P_{I,I}$ via `MPI_Reduce` on `hcomm`. After this, the diagonal processes (and only they) hold the new $\vec{y}$ blocks.
3. Compute norm on diagonals: each diagonal process computes the local sum of squares of its $\vec{y}$ block. These local partial norms are then reduced (with `MPI_Allreduce`) across `dcomm` so that every diagonal process ends up with the same global $\alpha = |\vec{y}|_2$.
4. Local normalization on diagonals: each diagonal process divides its local block by $\alpha$.
5. Column broadcast: each diagonal process $P_{J,J}$ broadcasts its normalized block to the rest of column $J$ via `MPI_Bcast` on `vcomm`, with the diagonal process at rank $J$ within `vcomm`.
6. Convergence test.

## Step 2: row reduction (MPI_Reduce on hcomm)

```c
static const long m = n / q;       // local block size = n / sqrt(p)
std::vector<double> x(m, 1.0);     // Ph.1: initial nonzero vector x
std::vector<double> y(m);
double alpha;

do {
    ... // Ph.2: local MVM, leaves local contribution in y[]

    // Ph.2 (continued): row-wise reduction of local contributions into x[]
    //   on the diagonal process of row I (which has rank I in hcomm).
    MPI_Reduce(&y[0], &x[0], m, MPI_DOUBLE, MPI_SUM, I, hcomm);
    // x[] on diagonal processes now contains the new distributed y
```

Note the root parameter is `I` - the rank of the diagonal process within `hcomm`. After this call, only the diagonal processes have meaningful data in `x[]`; the off-diagonal processes participate as contributors but do not receive the result (this is `MPI_Reduce`, not `MPI_Allreduce`). The reduction directly writes into `x` rather than into a separate `y` buffer - the same trick used in the row-wise mapping (Exam 51) that eliminates the copy step at the end of the iteration.

## Step 3: computing the norm (MPI_Allreduce on dcomm)

```c
    // Ph.3: norm of vector y - only diagonal processes participate.
    alpha = 0.0;
    if (I == J) {           // only diagonal processes
        for (long i = 0; i < m; i++) alpha += x[i] * x[i];
        MPI_Allreduce(MPI_IN_PLACE, &alpha, 1, MPI_DOUBLE, MPI_SUM, dcomm);
        alpha = sqrt(alpha);
        // Ph.4: normalize the local block
        for (long i = 0; i < m; i++) x[i] /= alpha;
    }
```

The norm is computed in three substeps: local sum of squares, all-reduce of the partial sums across `dcomm`, then square root. Only diagonal processes participate. After this, every diagonal process holds:

- the same global `alpha` (the eigenvalue estimate),
- its local block of the normalized eigenvector in `x[]`. The non-diagonal processes do not participate in this block - they wait at the next collective.

## Step 5: column broadcast (MPI_Bcast on vcomm)

```c
    // Ph.4 (continued): broadcast normalized block from diagonal to column.
    MPI_Bcast(&x[0], m, MPI_DOUBLE, J, vcomm);
    // Now every process in column J has the same x[] - ready for next iter.

} while (...);   // Ph.5: convergence test
```

The root is `J` - the rank of the diagonal process within `vcomm`. After this, every process in column $J$ holds the same block of $\vec{x}$, ready for the next iteration's local MVM. The non-diagonal processes' previously-stale `x[]` has been refreshed; the diagonal processes' already-normalized `x[]` is unchanged (broadcasting from self).

## Complete code

```c
int p, r;
MPI_Comm_size(MPI_COMM_WORLD, &p);
MPI_Comm_rank(MPI_COMM_WORLD, &r);

int q = 1;
while (q < p) { if (p == q * q) break; q++; }
int I = r / q, J = r % q;

MPI_Comm hcomm, dcomm, vcomm;
MPI_Comm_split(MPI_COMM_WORLD, I,       r, &hcomm);
MPI_Comm_split(MPI_COMM_WORLD, I == J,  r, &dcomm);
MPI_Comm_split(MPI_COMM_WORLD, J,       r, &vcomm);

static const long m = n / q;
std::vector<double> x(m, 1.0), y(m);
double alpha;

do {
    ... // Ph.2: local MVM

    MPI_Reduce(&y[0], &x[0], m, MPI_DOUBLE, MPI_SUM, I, hcomm);

    alpha = 0;
    if (I == J) {
        for (long i = 0; i < m; i++) alpha += x[i] * x[i];
        MPI_Allreduce(MPI_IN_PLACE, &alpha, 1, MPI_DOUBLE, MPI_SUM, dcomm);
        alpha = sqrt(alpha);
        for (long i = 0; i < m; i++) x[i] /= alpha;
    }
    MPI_Bcast(&x[0], m, MPI_DOUBLE, J, vcomm);

} while (...);   // Ph.5: convergence test

// The normalized eigenvector is stored distributively in x[] of diagonal processes.
// Created communicators must be released.
MPI_Comm_free(&hcomm);
MPI_Comm_free(&dcomm);
MPI_Comm_free(&vcomm);
```

## Memory complexity per process

Both arrays `x` and `y` are of size $m = n/\sqrt{p}$. Compared to the other mappings:

- Arbitrary: $x$ size $n$, $y$ size $n$.
- Row-wise: $x$ size $n$, $y$ size $n/p$.
- Checkerboard: $x$ size $n/\sqrt{p}$, $y$ size $n/\sqrt{p}$. For large $p$, checkerboard has by far the smallest memory footprint - the only one whose per-process memory shrinks as $p$ grows.

## Experimental evaluation (Blue Waters)

Same experimental setup as the previous two mappings: $p = 256$, $n = 256 \cdot 10^6$, 1 MPI process per computing node, MPI+OpenMP hybrid:

- Measured communication latency per iteration: $0.72$ seconds.
- This is $6.3 \times$ faster than arbitrary mapping ($4.54$ s) and $1.8 \times$ faster than row-wise mapping ($1.33$ s). For larger $p$ (e.g., $p = 1024$ with $n = p \cdot 10^6$), the gap widens significantly: arbitrary $\approx 22$ s/iter, row-wise $\approx 5.5$ s/iter, checkerboard $\approx 1.8$ s/iter.

## Summary remarks

- The experiments were done with very large matrices ($n = p \cdot 10^6$). If these matrices were dense they would not fit anywhere; the practical relevance comes from sparse matrices, for which SpMVM is a fundamental kernel used by many HPC applications beyond the Power Method.
- Even though the per-iteration differences look modest in absolute terms (seconds), the number of iterations can be hundreds or thousands, so substantial total time is saved.
- The convergence speed of the Power Method is strongly data-dependent. The number of iterations varies with the matrix.
- For real-world sparse matrices, understanding the structure of nonzeros is key to achieving uniform load. Real matrices typically have very irregular structures, and good mapping is a trade-off between balancing local computation and minimizing communication and memory latency.

> Checkerboard mapping wins on every metric: lowest per-process memory, lowest communication latency per iteration. Its only cost is implementation complexity: three sub-communicators must be created and managed, and the diagonal-vs-non-diagonal asymmetry in the inner loop must be handled carefully.

## Potential exam questions

- Describe the checkerboard mapping of a sparse matrix $A$ on $p$ MPI processes. How are the row and column indices $I$ and $J$ computed from the linear rank $r$ in `MPI_COMM_WORLD`?
- What convention is used for mapping the input and output vectors $\vec{x}$, $\vec{y}$ in the checkerboard Power Method? Why are the diagonal processes ($I = J$) chosen, and how is the memory footprint reduced compared to arbitrary or row-wise mapping?
- Why does each process in column $J$ only need access to the subvector $x_{J \cdot n/\sqrt{p}}, \ldots, x_{(J+1) \cdot n/\sqrt{p} - 1}$? What about the matrix mapping makes this sufficient?
- Describe the four conceptual stages of one Power Method iteration under checkerboard mapping: column broadcasts, local MVMs, row reductions, normalization.
- What is `MPI_Comm_split`, and what are its four parameters? Explain the role of the `color` and `key` parameters in particular.
- The checkerboard implementation creates three sub-communicators `hcomm`, `dcomm`, `vcomm`. State the color used for each and explain what operation each enables.
- Write the three `MPI_Comm_split` calls that create `hcomm`, `dcomm`, and `vcomm`. In which sub-communicator does the diagonal process of row $I$ have rank $I$? In which does it have rank $J$?
- Write the MPI call that performs the row reduction of local MVM contributions into the diagonal process. What is the root parameter, and in which communicator is the call made? Why is the result written into `x` rather than into a separate buffer?
- Why does only the diagonal processes' `x[]` hold meaningful data after the `MPI_Reduce` in row reductions? What does the off-diagonal `x[]` contain at that point?
- Explain how the global norm $\alpha = |\vec{y}|_2$ is computed across the diagonal processes. Which sub-communicator is used and which MPI operation?
- Why is the test `if (I == J)` wrapped around the norm computation block? What would go wrong if it were omitted?
- Write the `MPI_Bcast` call that propagates the normalized $\vec{x}$ block from each diagonal process to its column. What is the root parameter, and why is it `J`?
- State the per-process memory complexity of arrays `x` and `y` in the checkerboard implementation. Compare it to the arbitrary and row-wise mappings, and explain why checkerboard is the only one whose memory scales down with $p$.
- State the measured latency per iteration of the checkerboard variant on Blue Waters at $p = 256$, $n = 256 \cdot 10^6$. Express the speedup over arbitrary and row-wise mappings.
- Why is it important to call `MPI_Comm_free` on `hcomm`, `dcomm`, and `vcomm` at the end of the program?
- Compare the three Power Method MPI implementations (arbitrary, row-wise, checkerboard) along three axes: (a) per-process memory, (b) communication volume per iteration, (c) implementation complexity. Which mapping is preferred in practice and under what conditions?