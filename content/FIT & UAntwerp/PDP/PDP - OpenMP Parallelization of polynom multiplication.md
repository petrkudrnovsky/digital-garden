
> [!tldr] First 5 minutes of hell
> The sequential algorithm has $O(nm)$ complexity (first polynomial has $n$ coefficients, second polynomial has $m$ coefficients). The final `C[n+m+1]` array has to be initialized to zeros first, so running sums can take place.
> 
> 1) Parallelization of the outer i-loop
> 	- the outer loop is split to different threads and each thread reads the inner array sequentially
> 	- `atomic update` is needed for writing to the $C$ array (the threads may may meet each other at the same memory cell of the $C$ thread)
> 2) Parallelization of the inner j-loop
> 	- here are two ways:
> 		- sequential outer loop and team of threads is initialized within each outer-loop (fork-join overhead)
> 		- team of threads created once, each thread running outer loop in parallel and only inner-loop iterations are split
> 	- since the inner-loop iterations are split, the `i+j` combinations are unique and there is no need for atomicity
> 	- false sharing is unavoidable in both variants, because the written areas of `C[i+j]` are shifted only 1 position to the right (outer loop moves it synchronously for all threads), so they will often invalidate each other's cache blocks
> 3) Parallelization via output array decomposition
> 	- this approach uses a different view on the problem, we utilize the convolution formula `C[k] = sum over (l) of A[l] * B[k-l]`
> 		- we separate the output into disjoint areas and each thread computes different areas
> 		- this way false sharing and atomic operations are entirely eliminated
> 	- and for `A[l]` and `B[k-l]` to be valid for a fixed `k` (set by the outer loop), they need to be in those bounds:
> 		- `l >= max(0, k - n)`
> 		- `l <= min(k, m)`
> 		- it creates the diamond shape (we need to distribute the iterations (and those disjoint areas) in a way that there is a similar load on all threads)
> 	- next improvements:
> 		- align the chunk size with the cache block size (`cache_line_size / sizeof(element)`)
> 		- compute everything locally and write only the final result to the shared memory 
>  


### Problem definition

**Input:** Polynomials $A = \sum_{i=0}^{m} a_i x^i$ and $B = \sum_{i=0}^{n} b_i x^i$, where $a_m \neq 0$ and $b_n \neq 0$. **Output:** Polynomial $C = A \times B = \sum_{i=0}^{m+n} c_i x^i$, where:

$$c_k = \sum_{l=\max(0,, k-n)}^{\min(k,, m)} a_l \cdot b_{k-l}$$

Polynomials are represented as arrays of coefficients (`A[m+1]`, `B[n+1]`, `C[m+n+1]`). The classical sequential algorithm has `O(nm)` complexity.

---
### Sequential algorithm (Code PM-Seq)

```c
int A[m+1], B[n+1], C[n+m+1];

for (int k = 0; k <= m + n; k++)
    C[k] = 0;

for (int i = 0; i <= m; i++)
    for (int j = 0; j <= n; j++)
        C[i+j] += A[i] * B[j];
```

The core operation `C[i+j] += A[i]*B[j]` accumulates products into running sums - this is why `C` must be initialized to zero first. The multiplication combines all pairs of coefficients whose exponents sum to the same value, producing a diagonal-like summation pattern.

---

### Strategy 1: Parallelization of the outer i-loop (Code PM-A)

##### Access pattern (for `m = n = 5`, `p = 3`)
- Array `A` is split among threads by `schedule(static)`: τ₀ gets `A[0..1]`, τ₁ gets `A[2..3]`, τ₂ gets `A[4..5]`.
- All threads read all of `B` sequentially.
- Write regions in `C` overlap: τ₀ writes to `C[0..6]`, τ₁ to `C[2..8]`, τ₂ to `C[4..10]`.
##### Incorrect version (race condition)

```c
#pragma omp parallel for schedule(static)
for (int i = 0; i <= m; i++)
    for (int j = 0; j <= n; j++)
        C[i+j] += A[i]*B[j];  // INCORRECT - atomicity needed!
```

Different threads can simultaneously update the same element of `C` (e.g., thread with `i=1, j=3` and thread with `i=3, j=1` both write to `C[4]`). Without atomic protection, the results are completely useless.
##### Corrected version

```c
#pragma omp parallel for schedule(static)
for (int i = 0; i <= m; i++)
    for (int j = 0; j <= n; j++) {
        #pragma omp atomic update
        C[i+j] += A[i]*B[j];
    }
```
##### Properties of PM-A
- **Atomicity**: Required - threads may update overlapping parts of `C` simultaneously.
- **Synchronization**: Minimal - only 1 implicit barrier at the end of the parallel region.
- **False sharing**: Occurs because asynchronous threads write to neighboring `C` indices. Can only be eliminated by inflating `C` to size `p × (m + n + 1)` (same inflation technique as for small arrays) - see [[PDP - OpenMP Classification of parallelizable programs, sources of inefficiency of multithreaded programs, false sharing and its elimination]]
- **Load balance**: Good with `schedule(static)` - each thread gets `⌈(m+1)/p⌉` iterations of equal cost.

---
### Strategy 2: Parallelization of the inner j-loop (Codes PM-B1, PM-B2)

##### Key insight

All threads share the same `i` value (guaranteed by the barrier at the end of each parallel for). Each thread gets a different subset of `j` values. Since `i` is fixed and `j` values are disjoint across threads, the sums `i + j` are also disjoint across threads - no race conditions, no need for atomic operations.

##### Variant PM-B1: fork-join per outer iteration

```c
int A[m+1], B[n+1], C[m+n+1];

for (int i = 0; i <= m; i++)
    #pragma omp parallel for schedule(static)
    for (int j = 0; j <= n; j++)
        C[i+j] += A[i]*B[j];
```

In each iteration of the outer loop, `p` new threads are forked and joined. The overhead is `m × T_barr` (one fork-join per outer iteration).

##### Access pattern (for `m = n = 5`, `p = 3`, showing iteration `i = 2`)

- All threads read the same `A[2]`.
- `B` is split: τ₀ reads `B[0..1]`, τ₁ reads `B[2..3]`, τ₂ reads `B[4..5]`.
- `C` writes are disjoint: τ₀ writes `C[2..3]`, τ₁ writes `C[4..5]`, τ₂ writes `C[6..7]`.

##### Variant PM-B2: single thread team

```c
int A[m+1], B[n+1], C[m+n+1];

#pragma omp parallel
for (int i = 0; i <= m; i++)
    #pragma omp for schedule(static)
    for (int j = 0; j <= n; j++)
        C[i+j] += A[i]*B[j];
```

The thread team is created only once. All threads iterate the outer `i`-loop synchronously (each with its own copy of `i`), with a barrier at the end of each `#pragma omp for`. This eliminates the fork/join overhead of PM-B1 - just one fork at the beginning and one join at the end. The array `B` is divided equally among threads in each iteration and will be cached.
##### Properties of PM-B1/B2
- **Atomicity**: Not needed - write regions are disjoint within each outer iteration.
- **Synchronization**: PM-B1 has `m × T_barr` (repeated fork-join). PM-B2 has `m × T_barr` (barriers only, no fork-join overhead).
- **False sharing**: Unavoidable in both variants, for a fundamentally different reason than PM-A. As `i` increments by 1, each thread's write region `C[i+j]` shifts one position to the right. Threads are continuously moving through cache blocks - a cache block that was private to one thread in iteration `i` may become shared in iteration `i+1`. The write regions are mobile, sweeping across the array, making it impossible to fix cache blocks to specific threads.

> False sharing is unavoidable here. The threads are running through cache blocks freely, and there is no way to fix cache blocks to threads because the write regions are mobile.

- **Load balance**: Good - each thread performs `⌈(n+1)/p⌉` iterations of equal cost per outer iteration.

---

### Strategy 3: Parallelization via output decomposition (Codes PM-C1 → PM-C3)

This approach completely restructures the algorithm: instead of iterating over input arrays and accumulating into `C`, each thread is responsible for computing specific output elements `C[k]` directly using the convolution formula.
- e.g. `C[3]` will be calculated by thread 2 (so it will be the only thread writing into this element)
##### PM-C1: basic output decomposition with `schedule(static)`

```c
int A[m+1], B[n+1], C[m+n+1];

#pragma omp parallel for schedule(static)
for (int k = 0; k <= (m + n); k++)
    for (int l = max(0, k - n); l <= min(k, m); l++)
        C[k] += A[l] * B[k-l];
```
##### Properties of PM-C1

- **Atomicity**: Not needed - each thread writes to a disjoint part of `C`.
- **Synchronization**: Minimal - only 1 implicit barrier.
- **False sharing**: Possible at chunk boundaries (standard problem for any static distribution of contiguous output elements).
- **Load balance**: Poor with `schedule(static)`. The number of terms in the inner sum depends on `k`:

|`k`|Contributing `[i, j]` pairs|# terms|
|---|---|---|
|0|`[0,0]`|1|
|1|`[0,1] + [1,0]`|2|
|2|`[0,2] + [1,1] + [2,0]`|3|
|...|...|...|
|`m` (= `n`)|`[0,m] + [1,m-1] + ... + [m,0]`|`m+1`|
|...|...|...|
|`2m`|`[m,m]`|1|

For `m = n`, the number of terms grows linearly from 1 to `m+1` and then decreases back to 1. With `schedule(static)`, the middle thread gets roughly twice the load of the edge threads.
##### Why `schedule(dynamic, 1)` is not the answer
Dynamic scheduling with default `chunk_size = 1` would balance the load but introduces false sharing (single-element chunks cause neighboring threads to write to the same cache block). Dynamic scheduling also has higher runtime overhead than static.

##### PM-C2: balanced output decomposition with `schedule(static, X)`
Since the load profile is known and predictable (linear growth then linear decay), optimal partitioning can be precomputed statically rather than relying on dynamic scheduling. The key is to use `chunk_size = X = cache_line_size / sizeof(int)` with proper alignment of `C`:

```c
int A[m+1], B[n+1], alignas(cache_line_size) int C[m+n+1];
int X = cache_line_size / sizeof(int);

#pragma omp parallel for schedule(static, X)
for (int k = 0; k <= (m + n); k++)
    for (int l = max(0, k - n); l <= min(k, m); l++)
        C[k] += A[l] * B[k-l];
```
- $k$ is an index of the final array 
- $l$ needs to run between 0 and $k-n$, which is in fact $m$ (because $k=n+m$) because the array A is of size $m$
- it creates the diamond-like shape
	- left side of the diamond (where $k$ is low):
		- the $l$ runs from 0 (we are still in the $m$ array) to $k$ (we are still in the $n$ array)
	- right side of the diamond (where $k$ is large):
		- the $l$ runs from $k-n$ (beyond A's range)

This works because `schedule(static, X)` distributes chunks of size `X` in round-robin fashion across threads. The symmetric load profile (grows then shrinks) means each thread gets a mix of cheap chunks (near edges) and expensive chunks (near center), naturally balancing the total work. At the same time, each chunk occupies exactly one cache block (given alignment), eliminating false sharing. The requirement is that `m, n ≫ Xp` (the polynomials must be sufficiently large).
##### PM-C3: local accumulation optimization
This further optimization accumulates the inner sum in a local register variable `s`, writing to shared memory only the final result:

```c
int A[m+1], B[n+1], alignas(cache_line_size) int C[m+n+1];
int X = cache_line_size / sizeof(int);

#pragma omp parallel for schedule(static, X)
for (int k = 0; k <= (m + n); k++) {
    int s = 0;
    for (int l = max(0, k - n); l <= min(k, m); l++)
        s += A[l] * B[k-l];
    C[k] = s;
}
```

The general principle: perform maximum computation locally (in registers/stack) and write to shared memory only when the final result is ready. This minimizes shared memory writes and reduces memory bus traffic.
##### Visualization of PM-C2/C3 load distribution

For `p = 3`, the computation can be visualized as a diamond shape (reflecting the triangular growth and decay of per-index cost). Using `schedule(static, X)`, horizontal stripes of width `X` are assigned round-robin to threads. Each color (thread) covers approximately 1/3 of the total area, illustrating balanced load distribution.
![[Pasted image 20260517180850.png]]

---

### Comprehensive comparison of all strategies

| Aspect                       | PM-A (outer i-loop)                      | PM-B1/B2 (inner j-loop)                         | PM-C2/C3 (output decomposition)                                               |
| ---------------------------- | ---------------------------------------- | ----------------------------------------------- | ----------------------------------------------------------------------------- |
| Atomic needed                | Yes                                      | No                                              | No                                                                            |
| Race conditions              | Yes (overlapping writes to `C`)          | No (disjoint writes per iteration)              | No (disjoint writes globally)                                                 |
| Synchronization              | 1 barrier                                | `m × T_barr` (B1: fork-join, B2: barriers only) | 1 barrier                                                                     |
| False sharing                | Yes (can eliminate by inflating `C`)     | Unavoidable (mobile write regions)              | Eliminated by alignment + `schedule(static, X)`                               |
| Load balance                 | Good (uniform iterations)                | Good (uniform iterations)                       | Poor with `static`, good with `static,X` (round-robin over symmetric profile) |
| Memory overhead              | None (or `p × (m+n+1)` if inflating `C`) | None                                            | None                                                                          |
| Thread creation              | 1 fork-join                              | B1: `m` fork-joins, B2: 1 fork-join             | 1 fork-join                                                                   |
| Best variant within strategy | -                                        | PM-B2 (single team)                             | PM-C3 (local accumulation)                                                    |

---

### Key design lessons from polynomial multiplication

The polynomial multiplication example demonstrates several important principles:

- **Output decomposition is often the best strategy**: By making each thread responsible for computing disjoint output elements, race conditions and atomic operations are eliminated entirely. This is a general technique applicable whenever the output can be partitioned.
- **Load balance and false sharing can conflict**: `schedule(dynamic, 1)` balances load but causes false sharing. `schedule(static)` avoids false sharing overhead but may imbalance load. `schedule(static, X)` with round-robin distribution over a symmetric load profile achieves both goals simultaneously.
- **Mobile write regions defeat false sharing elimination**: When thread write regions shift with each outer iteration (as in PM-B), no static assignment of cache blocks to threads is possible.
- **Minimize shared memory writes**: Accumulating partial results in local variables (PM-C3) and writing only the final result to shared memory reduces bus traffic and potential for false sharing.
- **Avoid repeated fork-join**: Creating a single thread team (PM-B2 over PM-B1) eliminates overhead proportional to the number of outer iterations.

---

### Potential exam questions

1. State the formula for the coefficients $c_k$ of the product polynomial $C = A \times B$. What is the sequential complexity of classical polynomial multiplication?
2. Write OpenMP code for parallelization of the outer `i`-loop (PM-A). Why are atomic operations required? Draw the access pattern for `C` with `m = n = 5`, `p = 3`.
3. Write OpenMP code for parallelization of the inner `j`-loop (PM-B1). Why are atomic operations not needed here? What is the synchronization overhead?
4. Explain the difference between PM-B1 and PM-B2. Why is PM-B2 faster? Write both codes.
5. Explain why false sharing is unavoidable in PM-B1/B2. What is fundamentally different about the cause of false sharing here compared to PM-A?
6. Write OpenMP code for the output decomposition approach (PM-C1). Why is `schedule(static)` insufficient for good performance? Use the table of `[i,j]` contributions for `m = n = 5` to explain the load imbalance.
7. Why is `schedule(dynamic, 1)` a poor choice for PM-C, despite solving the load imbalance problem?
8. Explain how `schedule(static, X)` with `X = cache_line_size / sizeof(int)` and alignment of `C` simultaneously achieves load balance and eliminates false sharing in PM-C2. What assumption on `m, n` is needed?
9. Write the PM-C3 code with local accumulation. What general principle does this optimization illustrate?
10. Compare all three strategies (PM-A, PM-B, PM-C) in terms of atomicity requirements, synchronization overhead, false sharing, and load balance. Which is the best overall and why?
11. For `m = n = 5` and `p = 3`, how many terms does each thread compute under `schedule(static)` in PM-C1? Quantify the load imbalance.
12. Describe the diamond-shaped visualization of PM-C2/C3 load distribution. Why does round-robin assignment of cache-line-sized chunks naturally balance the symmetric load profile?