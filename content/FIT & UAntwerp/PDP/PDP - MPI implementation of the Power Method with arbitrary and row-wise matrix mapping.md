
> [!tldr] First 5 minutes in hell
> Power method is an iterative method, which finds the biggest eigenvalue of a matrix and it's matching eigenvector. Find $\lambda$ satisfying: $A \vec{v} = \lambda \vec{v}$.
> 
> Algorithm:
> - pick any non-zero vector $x$
> - multiply $y=Ax$
> - measure the length of the $y$, which is denoted $\alpha$
> - normalize $x=y/\alpha$ (just divide it by it's length, resulting into an unit vector)
> - check if converged, if not, repeat
> 
> After enough iterations, $\alpha$ will converge to the $\lambda$. With Power method, we are only effectively computing $A^kx$ for a large $k$. Since it is iterative, we have to focus on minimizing the communication latencies per iteration.
> 
> Practical use:
> - Google PageRank, web link's structure is a giant sparse matrix and the dominant eigenvector tells you the page rankings
> 
> Parallel strategy 1: arbitrary mapping
> - the sparse matrix is saved in the COO format (row, column, value) triples (3 arrays)
> - the non-zeros are partitioned onto different processes arbitrarily (not in a given order)
> 	- any $P_i$ therefore holds nonzeros from any row and any column of the $A$ matrix
> - consequences:
> 	- every process needs the whole $x$ vector (there could be any column index referenced)
> 		- $O(\sqrt{N})$ memory for each process
> 	- every process produces a contribution to the $y$ array ($y^{(i)}$) of length $n$ elements (but all entries are partial sums, it contains mostly zeros, it is sparse and taking a lot of memory - fixed in the second strategy)
> 	- final $y$ is a product of the parallel reduction (`MPI_Allreduce` with the predefined `MPI_Sum`) of the $p$ vectors $y_{(i)}$ (each is of size $n$)
> - at the end, every process contains:
> 	- the greatest eigenvalue $\lambda$ in the $\alpha$ variable
> 	- the corresponding eigenvector in $y$
> 	- the corresponding normalized eigenvector $x$
> 
> Parallel strategy 2: block row-wise mapping
> - the matrix is partitioned into $p$ horizontal stripes, each having $n/p$ rows (done with the CSR format for sparse matrices)
> - every process still needs the whole vector $x$ (since the local nonzeros could by in any column)
> 	- this is performed by the AAB (all-to-all broadcast) since each process initially has $n/p$ part of the $x$ vector
> - BUT, every process produces only $n/p$ nonzero contributions to $y$ (compared to arbitrary's $n$ contributions and this chunk is already complete)
> 	- gathering of $n/p$ data is faster than reducing $n$ data
> 	- `MPI_Allgather` does not reduce, it just contatenates (each process contributes a chunk and the chunks get stacked side by side to produce the result)

- row-wise:
![[Pasted image 20260525123751.png]]
## The Power Method: mathematical background

The Power Method is an iterative algorithm that finds the largest eigenvalue (in absolute value) $\lambda$ of a matrix $A$ and its corresponding eigenvector $\vec{v}$, i.e., the pair satisfying $$ A \vec{v} = \lambda \vec{v}. $$ For a given eigenvalue $\lambda$, this equation has infinitely many eigenvectors: if $\vec{v}$ is a solution then so is $\beta \vec{v}$ for any scalar $\beta$. The eigenvectors are therefore usually normalized, e.g. to unit Euclidean norm. The problem of computing eigenvalues of very large sparse matrices is of major practical importance. Google's PageRank algorithm is a famous example - the matrix $A$ encodes the link structure of the web, and the dominant eigenvector encodes the page ranks. This is the application that pushed the Power Method to prominence in computer science.

## The Power Method algorithm

```
Ph.1: Take any nonzero initial vector x.
Ph.2: MVM: compute y <- A * x.
Ph.3: Reduction: compute the norm alpha of vector y:
         alpha <- ||y||_2 = sqrt(y_1^2 + y_2^2 + ... + y_n^2)
Ph.4: Replace x with the normalized y: x <- y / alpha.
Ph.5: Evaluate the convergence criterion.
Ph.6: If satisfied, the algorithm ends. Otherwise go to Ph.2.
```

During the iterative process, the powers of $A$ are computed (hence the name "Power Method"). The norm $\alpha$ converges to the largest eigenvalue $\lambda$, and the vector $\vec{y}$ from Ph.2 converges to the corresponding eigenvector $\vec{v}$.

## MPI implementation: general framework

Assume $A$ is a very large sparse matrix with no special structure of nonzero elements. Only nonzeros are stored, in a format such as COO or CSR (see Lecture 4). The set of nonzeros must be partitioned among $p$ MPI processes: $$ A = A^{(0)} + A^{(1)} + \cdots + A^{(p-1)} $$ where $A^{(i)}$ contains the nonzero elements assigned to process $P_i$. The MVM in Ph.2 ($\vec{y} \leftarrow A \vec{x}$) then translates into $p$ local MVMs and an all-to-all reduction: $$ \vec{y}^{(i)} \leftarrow A^{(i)} \vec{x}, \quad 0 \leq i < p $$ $$ \vec{y} \leftarrow \vec{y}^{(0)} + \vec{y}^{(1)} + \cdots + \vec{y}^{(p-1)}. $$ The specific computation and communication details strongly depend on the matrix mapping/partitioning/distribution scheme. Since the method is iterative and the number of iterations can be hundreds or thousands, we focus on minimizing the communication latency per iteration.

> In pseudocode we write $n$ instead of $\sqrt{N}$ for the side length of the matrix, to keep the code compact.

## Mapping 1: arbitrary mapping

Arbitrary mapping is the most general case. It naturally occurs when $A$ is stored in COO format: each nonzero is an independent triple `(row, col, value)`, and the partitioning is free to place any nonzero on any process. Every process $P_i$ can hold nonzeros from any row and any column of $A$. Consequences:

- Every process needs the whole vector $\vec{x}$, because its local nonzeros may reference any column index. Memory: $O(\sqrt{N})$ per process.
- Every process produces a contribution $\vec{y}^{(i)}$ of $n$ elements (any row may receive a contribution). Memory: $O(\sqrt{N})$ per process.
- The resulting $\vec{y}$ is constructed by a parallel reduction (`MPI_Allreduce` with `MPI_SUM`) of the $p$ vectors $\vec{y}^{(i)}$ of $n$ elements each.

## Arbitrary mapping: MPI code

```c
std::vector<double> x(n, 1.0);   // Ph.1: initial nonzero vector x
std::vector<double> y(n);
double alpha;
do {                              // iteration loop
    // Ph.2: local MVM
    #pragma omp parallel for
    for (long i = 0; i < n; i++) y[i] = 0;   // nullify y[]
    #pragma omp parallel for
    for all nonzero elements A[i,j] of process-local part of A:
        y[i] += A[i,j] * x[j];               // pseudocode
    // y[] now contains the local contribution to y

    // Ph.2 (continued): reduction of local contributions into the global y
    MPI_Allreduce(MPI_IN_PLACE, &y[0], n, MPI_DOUBLE, MPI_SUM,
                  MPI_COMM_WORLD);
    // y[] now contains the whole vector y

    // Ph.3: the norm of y
    alpha = 0.0;
    #pragma omp parallel for reduction(+:alpha)
    for (long i = 0; i < n; i++) alpha += y[i] * y[i];
    alpha = sqrt(alpha);

    // Ph.4: replace x with the normalized y
    #pragma omp parallel for
    for (long i = 0; i < n; i++) x[i] = y[i] / alpha;

} while (...);   // Ph.5: convergence test
```

After program completion every process contains:

- the greatest eigenvalue $\lambda$ in `alpha`,
- the corresponding eigenvector in `y`,
- the corresponding normalized eigenvector in `x`. Key points:
- The local accumulation `y[i] += A[i,j] * x[j]` is the same pattern as the COO sparse MVM from Lecture 4. In an OpenMP implementation it needs an atomic update because multiple threads may target the same `y[i]` from different nonzeros, but this detail is not the focus here.
- `MPI_Allreduce` with `MPI_IN_PLACE` means the input and output buffers are the same array; every process supplies its local contribution and receives the globally summed result in place.
- The norm is computed redundantly on every process. After `MPI_Allreduce`, every process has the same global `y`, so each computes the same `alpha` independently. No further communication is needed for the norm.
- The normalization and the convergence test are also fully replicated.

## Arbitrary mapping: experimental evaluation

Implemented in hybrid MPI+OpenMP on the Blue Waters supercomputer:

- 1 MPI process per computing node, 1 MPI process forked into 12 OpenMP threads.
- $p = 256$ computing nodes, matrix size $n = 256 \cdot 10^6$.
- Measured communication latency per iteration: $4.54$ seconds. The sparse matrix storage format is not optimized and the local computation time is not measured - only the MPI communication latency is reported, since that is what differs between the three mapping schemes.

## Mapping 2: block row-wise mapping

Processes $P_i$ form a 1-D virtual mesh $M(p)$. The matrix $A$ is partitioned into $p$ horizontal stripes of $n/p$ rows each. Process $P_i$ owns rows with indices $$
i \cdot \frac{n}{p}, \; i \cdot \frac{n}{p} + 1, \; \ldots, \; (i+1) \cdot \frac{n}{p} - 1.
$$ Consequences:

- Every process still needs the whole vector $\vec{x}$ (its local nonzeros may reference any column). Memory for $\vec{x}$: $O(n)$ per process. The communication needed to provide $\vec{x}$ everywhere is now an all-to-all broadcast (AAB), implemented as `MPI_Allgather`.
- However, every process produces only $n/p$ nonzero contributions $y^{(i)}_{i \cdot n/p}, \ldots, y^{(i)}_{(i+1) \cdot n/p - 1}$, since its local matrix rows are confined to a known horizontal stripe. Memory for $\vec{y}^{(i)}$: $O(n/p)$ per process.
- The global $\vec{y}$ is constructed by concatenation/gathering (AAG) of the local arrays $\vec{y}^{(i)}$, not by a sum-reduction. Each $\vec{y}^{(i)}$ contributes to a disjoint block of $\vec{y}$.

> The savings come from two things: (a) the AAG transfers only $n/p$ elements per process rather than $n$ in the reduction, and (b) the per-process memory for $\vec{y}$ drops from $n$ to $n/p$.

## Block row-wise mapping: MPI code

```c
static const long m = n / p;          // p is the number of MPI processes
std::vector<double> x(n, 1.0);        // Ph.1: full x is needed
std::vector<double> y(m);             // only m local elements of y
double alpha;

do {
    ... // Ph.2: local MVM
    // y[] now contains the nonzero part of this process's contribution to y

    // Ph.2 (continued): gather the local contributions into the resulting y.
    // The result is gathered directly into x; y is too small to hold it.
    MPI_Allgather(&y[0], m, MPI_DOUBLE,
                  &x[0], m, MPI_DOUBLE, MPI_COMM_WORLD);
    // x[] now contains the whole new vector y (not yet normalized)

    // Ph.3: norm of vector y, computed locally and redundantly
    alpha = 0.0;
    #pragma omp parallel for reduction(+:alpha)
    for (long i = 0; i < n; i++) alpha += x[i] * x[i];
    alpha = sqrt(alpha);

    // Ph.4: replace x with the normalized y
    #pragma omp parallel for
    for (long i = 0; i < n; i++) x[i] /= alpha;

} while (...);   // Ph.5: convergence test
```

Notice two important implementation details:

- The result of `MPI_Allgather` is written directly into `x`, not into a fresh `y` buffer. The local array `y` has size only $m = n/p$ and physically cannot hold the global vector. By gathering into `x`, the implementation simultaneously transfers the data and replaces the old `x` with the new global $\vec{y}$ - eliminating the copy step `x <- y` that would otherwise close every iteration.
- The same trick (reduction directly into `x` instead of in-place into `y`) could in principle be applied in the arbitrary-mapping case to save the copy step. The lecturer notes this explicitly. After this, the normalization is just an in-place scaling of `x`, since `x` already contains the new $\vec{y}$.

## Block row-wise mapping: experimental evaluation

Same experimental setup as before (Blue Waters, $p = 256$, $n = 256 \cdot 10^6$):

- Measured communication latency per iteration: $1.33$ seconds.
- This is $3.4$ times faster than arbitrary mapping.
- Significantly less memory is needed (only $\vec{x}$ is full-sized; $\vec{y}$ is reduced to $n/p$). The speedup comes from the AAG transferring only $n/p$ elements per process, against the reduction in the arbitrary-mapping case that transfers $n$.

## Comparison: arbitrary vs row-wise mapping

- Both require every process to hold the full $\vec{x}$ ($O(n)$ memory).
- Arbitrary mapping requires per-process $O(n)$ memory for $\vec{y}$ and a sum-reduction (`MPI_Allreduce`) on $n$ elements.
- Row-wise mapping requires per-process $O(n/p)$ memory for $\vec{y}$ and a gather (`MPI_Allgather`) of $n/p$ elements per process.
- Row-wise mapping wins on both axes: less memory and less communication. The price paid is that the row-wise partitioning is only natural if the sparse matrix storage allows row-aligned splitting (e.g., CSR), whereas arbitrary mapping is the only general option when the matrix is stored in COO without any structure-aware preprocessing.

> The reason row-wise is faster despite using "the same" all-to-all-style operation is that AAG of $n/p$ elements is fundamentally less data than reduction of $n$ elements, regardless of the underlying collective implementation. The checkerboard mapping (a third option) goes even further and is covered in a separate exam question (52).

## Potential exam questions

- State the mathematical problem solved by the Power Method. What does it compute, and what is the typical normalization convention applied to the eigenvector?
- Write out the six phases of the Power Method algorithm. Which phases involve communication in a distributed implementation, and which are purely local?
- Why is the Power Method called "the Power Method"? Explain by reference to what the iterative process effectively computes.
- Give a real-world example of a problem that requires applying the Power Method to a very large sparse matrix.
- Express the distributed MVM in Ph.2 as a sum of local MVMs and a reduction. What does $A^{(i)}$ denote, and why does this decomposition work for any sparse matrix partitioning?
- Define "arbitrary mapping" of a sparse matrix among MPI processes. When does this mapping naturally arise, and why does it impose $O(\sqrt{N})$ memory per process for both $\vec{x}$ and $\vec{y}$?
- Which MPI collective operation is used in the arbitrary-mapping implementation to combine local contributions $\vec{y}^{(i)}$ into the global $\vec{y}$? Why is `MPI_IN_PLACE` used, and what does it mean operationally?
- Why is the norm computation in Ph.3 of arbitrary mapping performed redundantly on every process, with no further MPI communication? What property of the preceding MPI call makes this possible?
- Write the inner loop of the arbitrary-mapping MPI implementation, including Ph.2, Ph.3, and Ph.4. Annotate which steps are local and which require communication.
- Describe block row-wise mapping. How are the matrix rows divided among the $p$ MPI processes, and what virtual topology do the processes form?
- Explain why, in block row-wise mapping, every process still needs the entire vector $\vec{x}$, even though it only produces $n/p$ elements of $\vec{y}$.
- Which MPI collective operation is used in row-wise mapping to combine the local contributions into the global $\vec{y}$? Why is it more efficient than the one used in arbitrary mapping?
- The row-wise mapping MPI code gathers `y` directly into `x` rather than into a temporary buffer. Explain the two reasons this is done: (a) buffer size constraint, (b) elimination of a copy step.
- Could the "gather directly into x" trick also be applied to the arbitrary-mapping implementation? If so, in what form?
- State and justify the measured speedup of row-wise mapping over arbitrary mapping in the Blue Waters experiment. From what does this $3.4 \times$ improvement come?
- Tabulate the per-process memory requirements of arbitrary vs row-wise mapping for arrays $\vec{x}$ and $\vec{y}$. Express each entry in terms of $n$ and $p$.
- Compare the asymptotic data volume sent per process by `MPI_Allreduce` in the arbitrary case vs `MPI_Allgather` in the row-wise case. Why does the row-wise version win, regardless of the specific collective implementation?