### Problem definition
- histogram expresses frequencies of appearances of data values
- we expect values in the input array that are in some range (e.g. 0-256 for 8-bit grayscale images)
	- values from the range serve as indexes
	- the final histogram assigns frequencies of given value from the range
- standard sequential algorithm has complexity `O(n) + O(range)`
### Sequential algorithm (Code HG-Seq)

```c
int *A, n,              // input array and its size
    histogram[range];   // resulting histogram

for (int i = 0; i < range; i++)  // histogram initialization
    histogram[i] = 0;

for (int j = 0; j < n; j++)     // histogram computation
    histogram[A[j]]++;
```
- increment operation `histogram[A[j]]++` involves three non-atomic sub-operations: read the current value, add one, write back
	- this needs to be an atomic operation in parallel algorithms to avoid race conditions
### Variant 1: Parallel computation of a shared histogram (HG-Shrd)

```c
int *A, n;
int histogram[range];

for (int i = 0; i < range; i++)  // histogram initialization
    histogram[i] = 0;            // master thread

#pragma omp parallel for schedule(static) shared(histogram)
{ for (int j = 0; j < n; j++) {  // histogram computation
    #pragma omp atomic update
    histogram[A[j]]++;
} }
```
- parallel time: `T(n, range, p) = O(range) + O(n/p)`
- no additional memory and no additional computation is needed (beyond the sequential algorithm)
- the atomic update is essential, otherwise the race conditions would introduce false results
- performance issues:
	1. **Serialization on atomic operations**: When multiple threads happen to update the same histogram index (because they encounter the same value in their input portions), they are serialized by the atomic operation - each must wait for the previous one to complete.
	2. **False sharing is unavoidable**: Since input data values serve as indexes (indirect addressing), two different threads can write to adjacent histogram positions at any time. Whether false sharing occurs depends entirely on the data values, not on the scheduling strategy. No clever choice of `schedule` can eliminate it
		- this is data-dependent, we could build the histogram without any false sharing and we could trigger false sharing with each access

### Variant 2: Parallel reduction of local partial histograms (HG-Local)
The idea is a time-space tradeoff: each thread gets its own private histogram copy in shared memory, computes locally without any conflicts, and then all threads cooperate to reduce the local histograms into a single global result.

```c
int *A, n,
    histogram[p][range];  // p = # of threads

#pragma omp parallel
{ int my_id = omp_get_thread_num();

  for (int i = 0; i < range; i++)      // each thread initializes
      histogram[my_id][i] = 0;         // sequentially its own histogram

  #pragma omp for schedule(static)
  for (int j = 0; j < n; j++)          // histogram computation
      histogram[my_id][A[j]]++;

  #pragma omp for schedule(static)
  for (int i = 0; i < range; i++)      // each thread reduces
      for (int j = 1; j < p; j++)      // range/p p-tuples of local
          histogram[0][i] += histogram[j][i];  // counters
}
```
- phases:
	1. **Initialization** (`O(range)` per thread, all in parallel): Each thread zeroes out its own row of the 2D histogram array.
	2. **Parallel computation** (`O(n/p)`): Threads write into their own local histograms only - no side effects to other threads, no atomic operations needed, no false sharing during this phase.
	3. **Parallel reduction** (`O(range)`): The `range` columns of the 2D histogram are split among `p` threads. Each thread sums `range/p` columns across all `p` rows into `histogram[0]`.
##### Parallel time
$T(n, range, p) = O(range) + O(n/p) + O(range)$
- The initialization and reduction phases each cost `O(range)` parallel time. The main computation phase costs `O(n/p)`.
### Comparison of variants

| Aspect            | HG-Shrd (shared)                                        | HG-Local (local + reduction)                 |
| ----------------- | ------------------------------------------------------- | -------------------------------------------- |
| Additional memory | None                                                    | `p × range` (p-fold)                         |
| Atomic operations | Required (`#pragma omp atomic update`)                  | Not needed                                   |
| False sharing     | Unavoidable (data-dependent indirect addressing)        | None during main computation                 |
| Parallel time     | `O(range) + O(n/p)`                                     | `O(range) + O(n/p) + O(range)`               |
| Data dependency   | Performance depends strongly on input data distribution | Performance independent of data distribution |
| Scheduling        | `static` preferable                                     | `static` preferable                          |
- if $p$ is large or $range$ are large, the second variant would require large space allocations (for their own copies of the histogram)
### Key design principles illustrated by histogram parallelization

The histogram example demonstrates several general principles:

- **Local computation + global reduction** is a very common trick: do the heavy computation purely locally to produce partial results, then reduce (globalize) them at the end. The lecturer likened it to each thread doing its "homework" privately, then "publishing" the results.
- **Indirect addressing defeats false sharing elimination**: When write targets are determined by input data values rather than by the loop index, no scheduling or alignment strategy can prevent false sharing. The only recourse is to eliminate shared writes entirely (as HG-Local does).
- **Time-space tradeoff**: HG-Local trades `p`-fold memory for elimination of atomics and false sharing. This tradeoff is worthwhile when `range` and `p` are moderate.
- **Static scheduling is preferable** in both variants because the per-iteration work is uniform (one increment per iteration in the main loop).
### Potential exam questions

1. Describe the sequential histogram computation algorithm and state its time complexity.
2. Write OpenMP code for the shared-histogram parallel variant (HG-Shrd). Why is `#pragma omp atomic update` necessary? What happens if it is omitted?
3. Explain why false sharing in the shared-histogram variant is unavoidable. Why can't alignment or scheduling strategy eliminate it?
4. Write OpenMP code for the local-histogram parallel variant (HG-Local). Describe the three phases and their complexities.
5. Derive the parallel time complexity of HG-Local: `T(n, range, p) = O(range) + O(n/p) + O(range)`. Explain what each term corresponds to.
6. How does the parallel reduction phase work in HG-Local? For `p = 4` and `range = 12`, describe concretely what each thread computes during reduction.
7. Why is no atomic operation needed in HG-Local during either the computation or the reduction phase?
8. Under what conditions is HG-Local infeasible? Give a concrete example with numerical values.
9. Compare HG-Shrd and HG-Local in terms of memory overhead, synchronization overhead, false sharing, and data dependence of performance.
10. Why is histogram computation classified as a memory-bound algorithm? What limits its parallel scalability?
11. The lecturer described HG-Local as a "local computation + global reduction" pattern. Give another example (from the course material) where this pattern could be applied.
12. If the histogram `range` is 256, `p = 8`, and `sizeof(int) = 4`, how much additional memory does HG-Local require compared to HG-Shrd? Is this acceptable?