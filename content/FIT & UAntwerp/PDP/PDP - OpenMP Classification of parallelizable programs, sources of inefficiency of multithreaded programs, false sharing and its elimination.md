
> [!tldr] First 5 minutes of hell
> The parallel algorithms fall into 2 categories (from the view of effective parallelizability on multicore CPUs with shared memory):
> 
> 1) CPU/compute bound algorithms
> 	- the performance is limited by CPU, the compute time given data is larger than the time needed for moving the data from shared memory to the CPU (and back)
> 	- examples: matrix-matrix multiplication, combinatorial NP-Hard problems...
>    
> 2) Memory bound algorithms
> 	- the performance is limited by the CPU-memory bandwidth, the compute time given data is less than the time needed for moving the data from shared memory to the CPU and back
> 	- CPU finishes early and has to wait for new data to arrive
> 		- this is called the CPU-memory bottleneck
> 	- examples: sequentially linear algorithms, where data pieces are used only few times
> 
> Sources of code inefficiencies:
> - thread load is not balanced = faster threads wait on slower ones (unnecessary idling)
> - too many sychronization points (barriers, critical sections) = each point has significant overhead (some threads must wait)
> - limited parallelism = not enough work for all threads (having 10 threads and 5 iterations)
> - a big inherently sequential part = we should parallelize as much as we can (the sequential part is the bottleneck due to Amdahl's Law)
> - inefficient usage of cache, not using space locality principle (random memory accesses, many cache misses etc.)
> - ignorance of cache coherence protocols, causing false sharing
> 
> False sharing
> - happens when another thread performs a close-enough write to the location I am looking at (= the same cache block), it causes an invalidation of that cache block (because of the cache coherence protocol - I cannot look on stale data)
> 	- so I need to constantly reload the new cache blocks as other threads write into almost-same locations
> - it's called false sharing, because the threads do not share any data logically, but from the hardware point of view, they share data (on the cache-block granularity)
>   
> How to reduce false sharing?
> - not using `schedule(static, 1)`, but `schedule(static)` - only reduces the problem, not eliminates
> - for large arrays:
> 	- calculate the chunk-size, so the chunks fit into the cache block without any overflow + align the array to the address divisible by the size of the cache block 
> - for small arrays (e.g. size $kp$ for small $k$):
> 	- artificial enlargement of each array element to fit the cache block (to pad the cache memory with dummy data), so each thread has a dedicated space to write the result of it's own computations without trigerring any cache invalidation for other threads
> 	- not suitable for large arrays because of flooding the memory bus with dummy data
> 
> False sharing cannot be eliminated 100%:
> - e.g. when we need to perform indirect addressing (e.g. when computing the histogram)
> - the write regions are dynamically shifted according to parallel for schedule (e.g. when computing the polynomial product)


### Classification of parallelizable programs
- two categories (from the view of effective parallelizability on multicore CPU with shared memory): 
	- CPU/compute-bound algorithms
	- Memory-bound algorithms
##### CPU/compute-bound algorithms
- performance is limited by CPU
	- compute time for given data is larger than the time needed for moving the data from memory to CPU and back
	- these algorithms perform many calculations over the same loaded data (e.g. matrix multiplication: memory complexity is `O(n²)` while computational complexity is `O(n³)`, so each matrix element is reused many times)
- examples:
	- matrix multiplication
	- CSSS (combinatorial state space search problems) for NP-hard problems (TSP)
##### Memory-bound algorithms
- the performance is limited by the CPU-memory bandwidth - the compute time on given data is less than the time needed for moving the data from memory to CPU and back
- CPU finishes early and idles, waiting for next data to arrive
- examples:
	- sequentially linear algorithms (each data element is used only $k$ times, where $k$ is a small constant)
		- scalar product, prefix sum, dynamic programming

##### Practical rule: does the parallelization make sense for problem $X$?
- only if there is a theoretical speedup (e.g., in the PRAM model)
- and sequential time complexity should be greater than linear, or hidden constants should be big

##### The CPU-memory bottleneck
- the fundamental latency ordering for current commodity multi- and manycore processors is:

$$T_{computation(CPU\ registers)} \ll T_{memory (read,\ write)} \ll T_{synchronization\ (barrier)}$$
- the memory bus becomes more and more the narrower bottleneck for multicore CPUs (especially if the algorithm cannot use transferred data many times)
- example:
	- a scalar product on a 4-core i7-6700 using AVX, each core can execute 16 FP multiply-and-add operations per clock cycle, requiring 32 float numbers (= 128 bytes) per cycle. One core therefore needs `3.4 GHz × 128B = 435 GB/s` of bandwidth - even the L1 cache (approx. 320 GB/s) cannot supply data fast enough
- how to mitigate it? (how to optimize the sequential codes):
	- maximize the number of operations per byte read from memory
	- maximize the utilization of cache
	- use the fact that the data are brought from memory through the cache blocks
	- try to use space locality (so no random jumping), so the prefetching can work
---
### Sources of OpenMP code inefficiencies

##### 1. Imbalanced thread load

A parallel region terminates with a barrier, so shorter threads wait for longer ones - wasting cores. Load imbalance arises when iterations have different computational costs (e.g., varying row lengths in sparse matrices, triangular iteration spaces in polynomial multiplication).
##### 2. Too tight synchronization

A great number of barriers or critical sections serializes execution. Every synchronization point has overhead proportional to `T_synchro`, which satisfies `T_synchro ≫ T_mem ≫ T_comp`. The overhead of `n` barriers is `n × T_barr`.
##### 3. Limited parallelism

When the number of iterations of a `for` loop is less than the number of threads (`# iterations < # threads`), some threads receive no work at all.
##### 4. High overhead of thread management

This includes frequent forks and joins of threads (e.g., placing `#pragma omp parallel for` inside a sequential outer loop causes repeated fork-join overhead) and the runtime overhead of `schedule(dynamic)`. A key optimization is to create a thread team once with `#pragma omp parallel` outside the loop and use `#pragma omp for` inside, avoiding repeated fork-join.
##### 5. Significant inherently sequential part

Due to Amdahl's law, as large a part of the code as possible should be parallelized. At the same time, due to thread management overheads, one should strive to use a moderate number of parallel threads (ideally equal to the number of cores).
##### 6. Inefficient utilization of cache memories

This encompasses violations of temporal and spatial locality: random, chaotic, irregular, or noncontinuous access patterns to large data structures, and indirect addressing. When locality is violated, data prefetching does not work and large memory access latency comes into effect. Key guidelines: maximize the number of operations per byte read from memory, maximize cache utilization, and be aware that data are brought from memory to caches with the granularity of 1 cache block (e.g., 64 bytes).
##### 7. Ignorance of cache coherence protocols (MESI, MOESI, MESIF)

This manifests as frequent writes into cached shared variables and, critically, false sharing. Every time a thread writes to a shared variable that is cached, the coherence protocol invalidates copies in other cores' caches, causing cache misses on subsequent accesses by those cores.

---
### False sharing
##### Definition
- occurs when various threads write to different memory addresses that are close enough to each other that they are cached into the same memory block
	- the cache coherence protocol invalidates all other cache block copies in the other cache memories
	- both cores have the same cache block for reading, if one of them writes into it, it gets invalidated for all cores having this block loaded (so they won't read stale data) - so before reading/writing, the second core must reload the cache block again (and when it writes in it, it invalidates it again for the first core) and this continues in a loop, slowing the algorithm
- it appears typically in the data parallelism
- typical situation: 1 cache block size `β = 64B`, an `int` number has 4B, so a cache block consists of `X = 16` numbers. A write from another thread into a shared array at index `+/- i`, where `i ≤ 15`, may cause cache block invalidation (e.g., in the MESI protocol)
- worst-case: `schedule(static,1)`
	- all threads essentially always writing into the same block 
- it's called false sharing, because the threads do not share any data logically, but from the hardware point of view, they share data (on the cache-block granularity)
### Elimination of false sharing

##### Reducing false sharing with `schedule(static)`
Using `schedule(static)` (default chunk size `n/p`) assigns contiguous blocks of iterations to threads:

```c
int A[n];
#pragma omp parallel for shared(A) schedule(static)
for (int i = 0; i < n; i++)
    A[i] = foo(i);
```

This reduces false sharing but does not eliminate it. Each thread gets `n/p > X` contiguous elements that fill more than one cache block. However, at the boundary between adjacent thread chunks, one cache block may be split between two threads. For example, for `n/p = 24`, even-odd pairs of neighboring threads will always share and invalidate one cache block.
##### Elimination for big arrays - alignment + chunk size
False sharing of an array `A` is **eliminated** if the following two conditions are satisfied:
1. `chunk_size ≥ X = cache_line_size / sizeof(element_type)`
2. Array `A` is **aligned** to an address divisible by `cache_line_size` (the alignment requirement) In such a mapping, exactly 1 thread will write into 1 cache block:

```c
alignas(cache_line_size) int A[n];
int X = cache_line_size / sizeof(int);
#pragma omp parallel for shared(n, A) schedule(static, X)
for (int i = 0; i < n; i++)
    A[i] = foo(i);
```

This approach works when `n` is large relative to `X × p`, which is the common case.
##### Elimination for small arrays - padding/inflation
The previous approach cannot be applied to small arrays, e.g., `n = p`. In such cases, false sharing is eliminated with an artificial inflation/enlargement of array elements using dummy data. Every array element is enlarged to the cache block size:

```c
alignas(cache_line_size) int A[n][cache_line_size / sizeof(int)];
#pragma omp parallel for shared(A) schedule(static,1)
for (int i = 0; i < n; i++)
    A[i][0] = foo(i);
```

Combined with alignment, no two threads can write into the same cache block. The trade-off is that this trick multiplies memory requirements and floods the memory bus bandwidth with dummy traffic, making it inapplicable for large arrays.
##### Cases where false sharing cannot be eliminated
In some algorithms, false sharing is unavoidable regardless of scheduling strategy. This occurs when:

- Indirect addressing is used (e.g., histogram computation with `histogram[A[j]]++`), where the write target depends on input data values and no schedule can predict or control which cache blocks are hit.
- Write regions shift dynamically (e.g., in polynomial multiplication via inner-loop parallelization, where thread write regions `C[i+j]` move one position right with each outer iteration `i`, continuously sweeping across cache blocks).

---
### Summary of key relationships and formulas

|Parameter|Meaning|
|---|---|
|`T_comp ≪ T_mem ≪ T_synchro`|Fundamental latency ordering on multicore CPUs|
|`T_atom ≥ T_mem`|Atomic operation is at least as expensive as memory access|
|`β` (typically 64B)|Cache block (cache line) size|
|`X = cache_line_size / sizeof(element_type)`|Number of elements per cache block|
|Alignment requirement|Array start address divisible by `cache_line_size`|
|`B = f × S`|Memory hierarchy bandwidth (frequency × bytes per cycle)|

---
### Potential exam questions

1. Define CPU-bound and memory-bound algorithms. Give two examples of each and explain why they fall into their respective categories.
2. Explain the latency relationship `T_comp ≪ T_mem ≪ T_synchro` and its implications for OpenMP program design.
3. List and briefly describe all seven sources of inefficiency in OpenMP multithreaded programs.
4. What is false sharing? Explain the mechanism at the hardware level - why does it occur even though threads write to different addresses?
5. Given a cache line of 64 bytes and an array of 4-byte integers, calculate `X` and explain how `schedule(static,1)` causes false sharing.
6. State the two conditions necessary to eliminate false sharing for large arrays. Write OpenMP code that satisfies both conditions.
7. How is false sharing eliminated for small arrays (e.g., `n = p`)? What is the trade-off of the padding/inflation approach?
8. Explain why `schedule(static)` with default chunk size reduces but does not eliminate false sharing. Use a concrete numerical example (e.g., `n/p = 24`, `X = 16`).
9. Why is false sharing unavoidable in the shared-histogram parallel algorithm? What alternative approach avoids it, and at what cost?
10. Compare the overhead of placing `#pragma omp parallel for` inside a sequential loop versus creating a single thread team with `#pragma omp parallel` and using `#pragma omp for` inside. What is the quantitative difference in synchronization overhead?
11. Explain the CPU-memory bottleneck using the AVX scalar product example. Why is even L1 cache bandwidth insufficient?
12. Why does HPCG achieve only 1-5% of peak performance while HPL achieves 60-80%? Relate this to the CPU-bound vs memory-bound classification.