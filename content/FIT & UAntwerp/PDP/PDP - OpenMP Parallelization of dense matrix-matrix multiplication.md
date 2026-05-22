
> [!tldr] First 5 minutes in hell
> The classic sequential solution has complexity of $O(n^3)$. There are no data dependencies among the loop levels, which allows for multiple parallelization versions (essentially placing the `#pragma omp for` to any loop level).
> 
> The MMM problem is typical CPU-bound parallelization problem. The memory complexity is only $O(n^2)$, so for each "loaded" matrix element, $n$ CPU operations are made. This algorithm then could be well parallelized, since there is no significant CPU-memory bottleneck.
> 
> Parallelization of the outer i-loop:
> - the most simplest and most effective, each thread has a continuous block of  `n/p` rows and computes corresponding `C` values, no overlaps (no conflicts and minor false sharing for big matrices)
> - it's good to parallelize only the outermost loop, since it balances the workload well and there are minor synchronization delays
> 
> Parallelization of the middle j-loop, two versions:
> - with block iteration chunks
> 	- master thread runs the outer loop only, the middle loop schedules `n/p` iterations (disjoint parts of the same row)
> 	- higher synchronization overhead (often fork-join, $n*T_{barr}$)
> 	- no write collisions in the shared memory
> 	- enhancemend could be done by moving the creation of `parallel` region up, so there is no fork-join overhead (but the barrier synchronization overhead stays - that is essential)
> - with cyclic iteration chunks (with `schedule(static,1)`)
> 	- same as block iteration chunks, just each thread gets exactly one row element (which can cause false sharing since each thread operates right next to other threads)
> 
> Parallelization of the inner k-loop, two versions:
> - utilizing the parallel reduction property for the `C[i][j]` calculation
> 	- having a big overhead of $n^2*(T_{barr}+T{reduction}(n,p))$ 
> - having outer- and middle-loop to be duplicated over all threads and splitting work only on the inner loop + having the `master` directive that only master thread writes to the final $C$ 
> 	- slightly better (not having the overhead from $n^2$ fork-joins, but still really slow)
> - so yes, we can parallelize the innermost scalar products, but the synchronization overhead is large and is driving the total parallel time $T(n,p)$

For better visualization:
![[Pasted image 20260520114408.png]]

### Problem definition
- Consider two square matrices `A`, `B` of dimension `n × n`. The goal is to compute the product `C = A × B`.
- The classical algorithm computes `n²` scalar products of rows of `A` and columns of `B`, requiring `n³` multiplications and additions.
- There are no data dependencies among any of the three loop levels, which means one can place `#pragma omp parallel for` at any nesting level. This yields multiple parallelization variants with very different trade-offs. Any scheduling can be used; static is reasonable throughout.

---
### Sequential algorithm (Code MMM-Seq)

```c
float A[n][n], B[n][n], C[n][n];

for (int i = 0; i < n; i++)
    for (int j = 0; j < n; j++) {   // n^2 iterations
        float s = 0.0;
        for (int k = 0; k < n; k++) // scalar product
            s += A[i][k] * B[k][j];
        C[i][j] = s;
    }
```

The local variable `s` accumulates the scalar product in a register before writing the final result to `C[i][j]`. This is itself an optimization - it minimizes writes to the shared output matrix.

---
### Variant MMM-i: parallelization of the outer i-loop

```c
float A[n][n], B[n][n], C[n][n];

#pragma omp parallel for schedule(static)
for (int i = 0; i < n; i++)
    for (int j = 0; j < n; j++) {
        float s = 0.0;
        for (int k = 0; k < n; k++)
            s += A[i][k] * B[k][j];
        C[i][j] = s;
    }
```

Each thread receives a contiguous block of `n/p` rows and computes the corresponding rows of `C`. The written areas in `C` are disjoint (each thread owns a horizontal band of rows).
##### Properties
- **Write conflicts**: None - disjoint row bands.
- **Atomic operations**: Not needed.
- **Synchronization**: Minimal - only 1 implicit barrier at the end of the parallel region.
- **False sharing**: Negligible for large matrices. Can theoretically occur at the boundary between adjacent thread blocks (last cache block of one thread's band may overlap with the first of the next), but with `n/p ≫ X` this affects at most 1 cache block per boundary.
- **Load balance**: Perfect - each thread performs `n/p` identical row computations.
![[Pasted image 20260520114907.png]]

> This is the simplest and, as it turns out, the best option for parallelizing MMM.

---
### Variant MMM-j1: parallelization of the j-loop with block chunks

```c
float A[n][n], B[n][n], C[n][n];

for (int i = 0; i < n; i++)                     // master thread only
    #pragma omp parallel for schedule(static)
    for (int j = 0; j < n; j++) {                // each thread gets n/p iterations
        float s = 0.0;
        for (int k = 0; k < n; k++)
            s += A[i][k] * B[k][j];
        C[i][j] = s;                             // all threads write into the same row of C
    }
```

All threads share a given row index `i` of `A`. Each thread covers a fixed portion of the column space (`n/p` columns), producing the same row of `C` in non-overlapping segments.
##### Properties
- **Write conflicts**: None - disjoint column segments within each row.
- **Atomic operations**: Not needed.
- **Synchronization**: Higher overhead: `n × T_barr` (one fork-join per row `i`).
- **False sharing**: Minor for sufficiently large `n/p`, since each thread writes a continuous segment of size `n/p` within a row.
- **Load balance**: Perfect.
![[Pasted image 20260520114919.png]]

---
### Variant MMM-j2: parallelization of the j-loop with cyclic chunks

```c
float A[n][n], B[n][n], C[n][n];

for (int i = 0; i < n; i++)
    #pragma omp parallel for schedule(static, 1)
    for (int j = 0; j < n; j++) {                // cyclic map of iterations to threads
        float s = 0.0;
        for (int k = 0; k < n; k++)
            s += A[i][k] * B[k][j];
        C[i][j] = s;                             // 2 threads can write into the same cache block
    }
```

Identical to MMM-j1 in synchronization and absence of write collisions, but `schedule(static, 1)` assigns columns cyclically. Each thread writes to every `p`-th element within a row, so two adjacent threads necessarily write into the same cache block.
##### Properties
- **Synchronization**: `n × T_barr` (same as MMM-j1).
- **False sharing**: Significant - different threads write simultaneously into adjacent elements of the same row of `C`, sharing cache blocks. This is measurably slower than MMM-j1.
- **Load balance**: Perfect.
![[Pasted image 20260520115139.png]]

---
### Variant MMM-j3: j-loop within a persistent parallel region

```c
float A[n][n], B[n][n], C[n][n];

#pragma omp parallel
for (int i = 0; i < n; i++)                     // all threads execute simultaneously
    #pragma omp for schedule(static)
    for (int j = 0; j < n; j++) {                // each thread gets n/p iterations
        float s = 0.0;
        for (int k = 0; k < n; k++)
            s += A[i][k] * B[k][j];
        C[i][j] = s;
    }
```

The thread-pool optimization applied to the j-loop: instead of `n`-fold fork-join (MMM-j1), the team of `p` threads is created only once. All threads iterate the outer `i`-loop synchronously (each with its own copy of `i`), with a barrier at the end of each `#pragma omp for`. This saves forking/joining overhead while retaining the same work-sharing as MMM-j1.
- the `for schedule` directive is only for work sharing, not creating a new parallel region
##### Properties

- **Synchronization**: 1 fork-join + `n` barriers (but no repeated thread creation/destruction).
- **False sharing**: Same as MMM-j1 - minor for large `n/p`.
- **Performance**: Should be faster than MMM-j1 for larger `p`, and comparable to MMM-i.

---
### Variant MMM-k1: parallelization of the k-loop with reduction

```c
float A[n][n], B[n][n], C[n][n];

for (int i = 0; i < n; i++)
    for (int j = 0; j < n; j++) {                // sequential n^2 iterations
        float s = 0.0;
        #pragma omp parallel for schedule(static) reduction(+ : s)
        for (int k = 0; k < n; k++)              // scalar product by parallel reduction
            s += A[i][k] * B[k][j];
        C[i][j] = s;                             // master thread writes 1 element of C
    }
```

The master thread runs sequentially `n²` iterations of the `i`- and `j`-loops. In each iteration, `p` threads perform a parallel reduction to compute one scalar product of one row of `A` and one column of `B`.
##### Properties
- **Synchronization**: The largest overhead of all variants: `n² × (T_barr + T_{PR}(n, p))`, where `T_{PR}(n, p)` is the cost of parallel reduction. This is `n²` fork-joins plus `n²` reductions.
- **Performance**: Catastrophic - `n²` barriers completely dominate the computation. The parallel time actually grows with `p`.

> The innermost-loop parallelization is a tragedy. It's even a slowdown with respect to the sequential complexity.

---
### Variant MMM-k2: k-loop with one team

```c
float A[n][n], B[n][n], C[n][n];

#pragma omp parallel
for (int i = 0; i < n; i++)
    for (int j = 0; j < n; j++) {                // n^2 iterations cloned for p threads
        float s = 0.0;
        #pragma omp for schedule(static) reduction(+: s)
        for (int k = 0; k < n; k++)              // scalar product by parallel reduction
            s += A[i][k] * B[k][j];
        #pragma omp master
        C[i][j] = s;
    }
```

All threads redundantly iterate the `i`- and `j`-loops identically. Only the inner `k`-loop is work-shared via `#pragma omp for` with reduction. The team is created once (thread-pool optimization over MMM-k1).
##### Properties
- **Synchronization**: 1 fork-join, but still `n²` barriers (one per scalar product). Better than MMM-k1 (no repeated fork-join), but the `n²` barrier overhead remains devastating.
- **Write to C**: Only the master thread writes (`#pragma omp master`).
- **Performance**: Better than MMM-k1, but still significantly slower than sequential.

---

### Comprehensive comparison

|Aspect|MMM-i|MMM-j1|MMM-j2|MMM-j3|MMM-k1|MMM-k2|
|---|---|---|---|---|---|---|
|Parallelized loop|`i` (outer)|`j` (middle)|`j` (middle)|`j` (middle)|`k` (inner)|`k` (inner)|
|Fork-joins|1|`n`|`n`|1|`n²`|1|
|Barriers|1|`n`|`n`|`n`|`n²`|`n²`|
|Atomics needed|No|No|No|No|No (reduction)|No (reduction)|
|Write conflicts in `C`|None|None|None|None|None (master writes)|None (master writes)|
|False sharing|Negligible|Minor|Significant|Minor|N/A|N/A|
|Load balance|Perfect|Perfect|Perfect|Perfect|Perfect|Perfect|
|Scalability|Linear|Near-linear|Sub-linear|Linear|Negative (slowdown)|Negative (slowdown)|

---

### Why MMM is CPU-bound and parallelizes well

Dense matrix-matrix multiplication is a textbook CPU-bound algorithm. Memory complexity is `O(n²)` (three matrices of size `n²`) while computational complexity is `O(n³)`. Each matrix element is reused `O(n)` times during computation. This high arithmetic intensity means the CPU stays busy while the memory subsystem recovers, making parallelization effective - provided synchronization overhead is kept low (as in MMM-i). This stands in stark contrast to memory-bound operations like scalar product or SpMVM, where each element is used only a constant number of times and performance is limited by memory bandwidth.

---

### Design lessons from MMM parallelization

- **Parallelize the outermost loop when possible**: MMM-i has the lowest synchronization overhead (1 barrier) and naturally produces disjoint write regions. This is the ideal scenario.
- **Fork-join overhead is multiplicative**: Moving from the outer loop to the middle loop multiplies barriers by `n`; moving to the inner loop multiplies by `n²`. The measured data confirms this dramatic effect.
- **Thread-pool optimization matters**: Replacing repeated `#pragma omp parallel for` (MMM-j1) with a persistent `#pragma omp parallel` + `#pragma omp for` (MMM-j3) eliminates fork-join overhead and recovers near-optimal performance.
- **Scheduling affects false sharing**: `schedule(static)` with block chunks (MMM-j1) is superior to `schedule(static, 1)` with cyclic chunks (MMM-j2) because block assignment keeps each thread's writes within contiguous cache blocks.
- **Parallel reduction on the innermost loop is devastating**: Even though each individual scalar product can be parallelized, the overhead of `n²` reductions + barriers far exceeds any computational benefit. The synchronization cost `n² × (T_barr + T_{PR})` dwarfs the `O(n³/p)` parallel computation time.
- **Measure, don't guess**: The performance table shows that theoretically correct parallelizations can range from 11.8× speedup to 3× slowdown depending on variant choice. All six codes produce identical correct results - only performance differs.

---

### Potential exam questions

1. Write the sequential MMM algorithm. What is its time complexity and why is MMM classified as CPU-bound?
2. Write the MMM-i code. Why are the write regions in `C` disjoint? What is the synchronization overhead?
3. Write the MMM-j1 code. Why are `n` barriers needed? How does the access pattern for `C` differ from MMM-i?
4. Explain why MMM-j2 (`schedule(static, 1)`) is slower than MMM-j1 (`schedule(static)`). What specific hardware mechanism causes the difference?
5. Write the MMM-j3 code. Explain the thread-pool optimization and why MMM-j3 recovers performance comparable to MMM-i.
6. Write the MMM-k1 code. Derive the synchronization overhead `n² × (T_barr + T_{PR}(n, p))`. Why does parallel time increase with the number of threads?
7. Explain the difference between MMM-k1 and MMM-k2. Why is MMM-k2 faster, yet still slower than sequential?
8. Given the measured data (`n = 1200`, `T(n,1) = 2.84s`), compute the speedup of MMM-i for `p = 12`. Compare with MMM-k1 for `p = 12` and explain the difference.
9. Why does MMM achieve near-linear speedup with the i-loop parallelization, while a memory-bound algorithm like SpMVM cannot? Relate to the CPU-bound vs memory-bound classification.
10. Compare the six MMM variants in terms of: number of fork-joins, number of barriers, false sharing, and measured scalability. Present as a table.
11. What general principle determines which loop level should be parallelized in a nested loop computation? Illustrate with the MMM variants.
12. The lecturer called the k-loop parallelization "a tragedy." Explain quantitatively why `n² × T_barr` dominates `n³/p` computation time for moderate `n` and `p`. For what values of `n` and `p` would MMM-k1 break even with sequential execution?