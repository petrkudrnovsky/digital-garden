### Problem definition

Consider a computation `y = Ax` where `A` is an input sparse matrix of order `n × n` with `A = (a_{i,j})`, `x` is an input vector of order `n`, and `y` is the output vector of order `n`. The number of nonzero elements in `A` is denoted by `N`. **Sparsity assumption:** `1 ≪ n ≤ N ≪ n²`. In practice, `N` is typically of order `n` rather than `n²`, meaning each row has a roughly constant number of nonzeros on average. This is why special `O(N)` storage formats are needed - storing the full `n × n` array would waste memory on zeros. For matrices with `n` in the hundreds of millions, the dense format would exhaust memory. **SpMVM** (Sparse Matrix-Vector Multiplication) is the most common operation in numeric linear algebra, needed in iterative solvers of systems of linear equations, conjugate-gradient methods, and many others.
##### SpMVM is memory-bound
- Sequential complexity is `O(N)`.
- Each element of the matrix is loaded from main memory and used only once.
- The performance is therefore limited by the CPU-memory bandwidth. This is the fundamental reason why SpMVM achieves only a few percent of peak performance on supercomputers (as shown by the HPCG benchmark).

---
### The COO (Coordinate) format

The COO format is the simplest representation of sparse matrices. It stores each nonzero element together with its row and column index using three arrays:

- `RowInd[0, ..., N-1]` contains row indexes of nonzero elements.
- `ColInd[0, ..., N-1]` contains column indexes of nonzero elements.
- `Elems[0, ..., N-1]` contains values of nonzero elements. The ordering of nonzero elements is not defined - although it is usually row-wise, this is not guaranteed.

##### Example

For a `4 × 9` matrix with 14 nonzeros (4 in row 0, 3 in row 1, 5 in row 2, 2 in row 3), stored row-wise:

```
A.RowInd: [0, 0, 0, 0, 1, 1, 1, 2, 2, 2, 2, 2, 3, 3]
A.ColInd: [0, 1, 3, 5, 1, 2, 3, 1, 2, 4, 5, 6, 2, 3]
A.Elems:  [corresponding nonzero values]
```

##### Memory cost
Three arrays of size `N` each: total `3N` entries.

---
### The CSR (Compressed Sparse Row) format

Also called Compressed Row Storage (CRS). It exploits the row-wise ordering to compress the row index information. Sparse matrix `A` is represented by three arrays:

- `Elems[0, ..., N-1]` contains values of nonzero elements, stored in lexicographic order by row and column indexes.
- `ColInd[0, ..., N-1]` contains their column indexes.
- `RowStart[0, ..., n]` contains indexes into `ColInd`/`Elems` indicating where each row's elements begin. Specifically, `RowStart[i]` is the index of the first nonzero in row `i`, and the number of nonzeros in row `i` is `RowStart[i+1] - RowStart[i]`.

##### Example (same matrix as above)

```
A.RowStart: [0, 4, 7, 12, 14]
A.ColInd:   [0, 1, 3, 5, 1, 2, 3, 1, 2, 4, 5, 6, 2, 3]
A.Elems:    [corresponding nonzero values]
```
- it begins with row 0 up to 4th non-zero number, then 7-4=3 non-zeros in the 
##### Memory cost

Two arrays of size `N` plus one array of size `n+1`: total `2N + n + 1` entries. CSR saves memory compared to COO by replacing the `N`-element `RowInd` array with the `(n+1)`-element `RowStart` array.

##### Key advantage for parallelization

The row-wise storage means all elements contributing to a given output `y[i]` are contiguous in memory. Threads can be assigned entire rows and work on disjoint parts of the output vector - no atomic updates needed.

---

### Sequential SpMVM in COO

```c
struct A;           // matrix in the COO format
float x[n], y[n];  // input and output vectors

for (int i = 0; i < n; i++)
    y[i] = 0.0;

for (int k = 0; k < N; k++)
    y[A.RowInd[k]] += A.Elems[k] * x[A.ColInd[k]];
```
- it's `y=Ax`
Each iteration reads one nonzero element value and its column index, fetches the corresponding `x` element, and accumulates into the output `y` element identified by the row index. Both reads from `x` and writes to `y` use indirect addressing - the indices depend on the matrix structure, not on `k`.

---

### Parallel SpMVM in COO

```c
struct A; float x[n]; float y[n];

#pragma omp parallel for schedule(static)
for (int i = 0; i < n; i++)
    y[i] = 0.0;

#pragma omp parallel for schedule(...)
for (int k = 0; k < N; k++) {
    float temp = A.Elems[k] * x[A.ColInd[k]];
    #pragma omp atomic update
    y[A.RowInd[k]] += temp;
}
```

##### Why this is inefficient

- **Atomic operations required**: Indirect indexing allows multiple threads to update the same element of `y` simultaneously (different nonzeros may belong to the same row - in one row, there are multiple non-zeros and different threads may get them (at the end the whole row needs to be summed)).
- **False sharing unavoidable**: Indirect indexing does not guarantee any spatial or temporal locality. Which output indices different threads write to depends entirely on the matrix structure, so no scheduling strategy can prevent false sharing.
- **`N` writes to shared memory**: Every nonzero causes a write to `y`.

> Don't use COO for parallel SpMVM, ever.

---

### Sequential SpMVM in CSR

```c
struct A;           // matrix in the CSR format
float x[n], y[n];

for (int i = 0; i < n; i++) {         // loop on rows
    float sum = 0.0;
    for (int k = A.RowStart[i]; k < A.RowStart[i+1]; k++)
        sum += A.Elems[k] * x[A.ColInd[k]];  // sparse scalar product
    y[i] = sum;
}
```

The outer loop iterates over rows, and the inner loop iterates over nonzero elements of one row. The accumulation into a local `sum` variable means each `y[i]` is written exactly once. This is the key structural advantage of CSR: only `n` writes to `y` (one per row), compared to `N` writes in COO.

---

### Parallel SpMVM in CSR - scheduling variants

The basic parallel code places `#pragma omp parallel for` on the outer row loop:

```c
struct A; float x[n]; float y[n];

#pragma omp parallel for schedule(...)
for (int i = 0; i < n; i++) {
    float sum = 0.0;
    for (int k = A.RowStart[i]; k < A.RowStart[i+1]; k++)
        sum += A.Elems[k] * x[A.ColInd[k]];
    y[i] = sum;
}
```

No atomic operations are needed regardless of scheduling, because each iteration writes to a unique `y[i]`. The choice of scheduling has significant impact on performance.

##### Variant 1: `schedule(static)` - block distribution

Rows are distributed block-wise: each thread gets `n/p` contiguous rows. Write regions are disjoint and false sharing can be eliminated through cache block alignment. However, the number of nonzeros per row can be arbitrarily irregular, leading to potentially severe load imbalance. For example, a matrix with a dense horizontal band will overload the thread that receives that band.

##### Variant 2: `schedule(static, 1)` - cyclic distribution

Rows are distributed cyclically (round-robin). This provides somewhat better load balancing by sampling rows from different parts of the matrix. However, false sharing is introduced because adjacent threads write to adjacent `y` elements sharing cache blocks. A group of `X` threads (where `X = cache_line_size / sizeof(float)`) share one cache block. This is clearly worse than plain `static` - it adds false sharing while the load imbalance problem persists.

##### Variant 3: `schedule(static, 16)` - chunk-cyclic distribution

Using 16-element chunks (for `float`, `16 × 4B = 64B` = one cache block) eliminates false sharing while maintaining reasonably good load balance. The chunks are still thin relative to `n`, so the cyclic distribution at 16-row granularity samples from different matrix regions. This dominates `schedule(static, 1)`: eliminating false sharing slightly outweighs the somewhat coarser load balance.

##### Variant 4: `schedule(dynamic, K)` - dynamic distribution

Threads are assigned blocks of `K` rows dynamically as they become available. False sharing can be eliminated by choosing `K` as a multiple of `X` (with alignment). Load balancing improves over static variants. However, dynamic scheduling has higher runtime overhead. For very sparse matrices, the dynamic overhead dominates and causes slowdown relative to `schedule(static, 16)`. For denser matrices, the overhead is amortized by more useful balanced computation.

##### Variant 5: Explicit load balancing (SpMV-CSR-Bal)
- because we cannot guarantee the "density" of the non-zeros in every row
The matrix is split explicitly into `p` disjoint row bands so that each band contains roughly `N/p` nonzero elements. The width of bands varies - sparse regions get wider bands, dense regions get narrower bands - but each thread processes approximately the same computational work. 

**Algorithm:** Each thread `τ_j` (`1 ≤ j ≤ p-1`) computes its ideal left splitting index as `j × N/p` in `A.Elems`, then uses binary search on `A.RowStart` to find the first row `k_j` such that `A.RowStart[k_j] ≥ j × N/p`. This is the real left splitting index. The thread processes all rows from its real left splitting index to just before the next thread's real left splitting index. **Concrete example:** For `n = 15`, `N = 42`, `p = 3`: ideal split is `14 + 14 + 14`. After snapping to row boundaries, the actual split becomes `16 + 16 + 10`.

```c
int band[p+1];
band[p] = n;

#pragma omp parallel
{ int my_id = omp_get_thread_num();
  int my_ILSI = my_id * N / p;  // my IDEAL left splitting index in A.Elems
  band[my_id] = binary_search(A.RowStart, my_ILSI);
  // find my 1st row s.t. RowStart[row] >= my_ILSI

  #pragma omp barrier  // all threads must finish computing boundaries

  for (int i = band[my_id]; i < band[my_id+1]; i++) {
      float sum = 0.0;
      for (int k = A.RowStart[i]; k < A.RowStart[i+1]; k++)
          sum += A.Elems[k] * x[A.ColInd[k]];
      y[i] = sum;
  }
}
```

The barrier is essential: all threads must finish computing their boundaries before any thread starts reading its neighbor's boundary. After the barrier, each thread processes its band using the standard sequential CSR SpMVM - no atomic operations needed. Binary search has `O(log n)` cost, negligible compared to the `O(N/p)` computation.

> Every thread can compute its band independently based on its rank, using binary search in the RowStart array.
> - binary search will do a almost-perfect split and then each thread can operate independently

---

### Comprehensive comparison of parallel SpMVM variants

|Variant|Atomic|False sharing|Load balance|Overhead|Best for|
|---|---|---|---|---|---|
|COO parallel|Yes|Unavoidable|Depends on schedule|Atomic serialization|Never recommended|
|CSR `static`|No|Eliminable (alignment)|Poor (irregular rows)|Minimal|Uniform-density matrices|
|CSR `static,1`|No|Significant|Slightly better than `static`|Minimal|Almost never preferable|
|CSR `static,16`|No|Eliminated|Good (cyclic sampling)|Minimal|Good general-purpose choice|
|CSR `dynamic,K`|No|Eliminable (`K` multiple of `X`)|Good to excellent|Dynamic dispatch overhead|Dense matrices with irregular rows|
|CSR balanced|No|Not guaranteed|Excellent|`O(p log n)` binary search + 1 barrier|Best overall for most matrices|

---

### Experimental results (12-core CPU, 6 real matrices)

|Matrix|`n` [M]|`N` [M]|`N/n`|`T_COO` [ms]|`T_CSR` [ms]|
|---|---|---|---|---|---|
|circuit5M|5.5|59.5|10.7|194|98|
|FullChip|3.0|26.6|8.9|82|53|
|Rajat31|4.7|20.3|4.3|56|37|
|Nlpkkt120|3.5|95.1|26.8|283|144|
|ldoor|0.95|42.5|44.6|143|71|
|TSOPF_RS_b2383|0.038|16.2|424|69|23|

CSR is sequentially approximately 2× faster than COO because it reads less data (no `RowInd` array) and performs only `n` writes to shared memory instead of `N`.

##### Speedup summary (key observations for `p = 4` and `p = 12`)

- **COO**: Slowdown for `p = 4`. For `p = 12`, at most 2× speedup. `FullChip` (very dense rows) causes serialization of atomic accesses to the same address.
- **CSR `static`**: `2 ≤ S(n,4) ≤ 3`. For `p = 12`, speedup drops to roughly `S(n,4)/3` - 12 threads are ~3× slower than 4 threads due to load imbalance and memory bus saturation.
- **CSR `static,1`**: Worse than `static` due to false sharing, except for the densest matrix (TSOPF) where computation amortizes the penalty.
- **CSR `static,16`**: Improvement over `static,1` - no false sharing, load balance remains good. With `p = 12`, denser matrices approach the saturation bound of 4 memory controllers.
- **CSR `dynamic,16`**: Equal or better load balance than `static,16`. For very sparse matrices (Rajat31), dynamic overhead dominates. For denser matrices, overhead is amortized.
- **CSR balanced**: Nearly linear speedup for `p = 4` (between 3× and 4× across all matrices). For `p = 12`, no further improvement beyond `p = 4` because 4 memory controllers are already saturated. This is the best variant in most cases.

> The most critical performance criterion is the architecture of the memory interface. It doesn't matter whether you use 12 or 4 threads - what matters is the bandwidth between the CPU and the memory.

---

### Key design lessons from SpMVM parallelization

- **Storage format determines parallelizability**: COO requires atomic operations and `N` shared-memory writes; CSR requires neither and performs only `n` writes. The format choice is more consequential than the scheduling choice.
- **Memory bus saturation is the ultimate limit**: For SpMVM, the balanced CSR variant saturates 4 memory controllers with just 4 threads. Adding more threads cannot improve performance beyond this hardware limit.
- **Load balancing for irregular data requires algorithmic effort**: Simple OpenMP scheduling directives cannot handle arbitrary nonzero distributions. The balanced method uses knowledge of the `RowStart` array (binary search) to achieve near-optimal distribution statically.
- **False sharing vs load balance trade-off**: `static,1` improves balance but adds false sharing; `static,16` eliminates false sharing with acceptable balance; `dynamic` provides best balance but at runtime cost. The optimal choice depends on matrix density.
- **Per-element performance varies by matrix structure**: Even sequentially, the time per nonzero varies across matrices due to cache miss effects caused by different sparsity patterns. Irregular column indices (indirect addressing into `x`) destroy spatial locality.

---

### Potential exam questions

1. Define the COO and CSR sparse matrix storage formats. For a given example matrix, write out all three arrays for both formats.
2. What is the memory cost of COO vs CSR? Why does CSR save memory, and by how much?
3. Write sequential SpMVM code for both COO and CSR. Why is CSR approximately 2× faster sequentially?
4. Write parallel SpMVM code in COO. Why are atomic operations required? Why is false sharing unavoidable?
5. Write parallel SpMVM code in CSR. Why are atomic operations not needed regardless of scheduling?
6. Explain why `schedule(static)` can lead to arbitrarily unbalanced load in CSR SpMVM. Give a concrete matrix structure example.
7. Compare `schedule(static,1)` and `schedule(static,16)` for CSR SpMVM. Which is better and why?
8. When is `schedule(dynamic,K)` preferable to `schedule(static,16)` for CSR SpMVM? When is it worse?
9. Describe the balanced SpMVM algorithm (SpMV-CSR-Bal). What is the role of binary search on `RowStart`? Why is the barrier essential? For `n = 15`, `N = 42`, `p = 3`, show the ideal and actual splitting.
10. Why does the balanced CSR variant achieve nearly linear speedup for `p = 4` but no further improvement for `p = 12`? What hardware limitation is responsible?
11. SpMVM in COO performs `N` writes to shared memory while CSR performs `n` writes. Explain why this difference is fundamental for parallel performance.
12. Why is SpMVM classified as a memory-bound algorithm? How does this relate to the HPCG benchmark results discussed earlier in the course?