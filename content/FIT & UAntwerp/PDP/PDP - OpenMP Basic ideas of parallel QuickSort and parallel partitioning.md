### QuickSort properties relevant to parallelization

QuickSort is a recursive Divide-and-Conquer algorithm that is **data sensitive** (performance depends on input distribution), **in-place** (requires only `O(log₂ n)` auxiliary memory for the recursion stack in efficient implementations), and can be implemented exclusively with Compare-and-Swap operations. It is unstable - the ordering of equal elements is not preserved.

The key structural property for parallelization is that QuickSort performs its main work (partitioning) **on the way down** the recursion tree, before the recursive calls. This means the largest tasks appear at the top of the tree of recursive calls (TRC), which is favorable for parallel task creation - unlike MergeSort, which does work on the way back up.

All performance measurements in the lecture use arrays of 100 million random integers on a 12-core Intel Xeon E5-2680v3 (Haswell) with GNU GCC 5.4. Random data represents a favorable case for QuickSort.

### Sequential baseline: Lomuto's partitioning

The textbook sequential QuickSort uses Lomuto's partitioning variant. Two indices `i` and `j` both traverse the array left-to-right. The pivot is always the last element `A[hi]`. The invariant maintained is:

After each iteration of `j`, all elements in `A[lo, ..., i-1]` are less than the pivot, and all elements in `A[i, ..., j]` are greater than or equal to the pivot.

```c
long seq_partition_L(int* A, long lo, long hi) {
    long pivot = A[hi];
    long i = lo;
    for (long j = lo; j < hi; j++)
        if (A[j] < pivot)
            swap(A, i++, j);
    swap(A, i, hi);   // pivot placement
    return i;          // return pivot index
}
```

When the loop completes, a final swap places the pivot at position `i`, which becomes the splitter index. The sequential textbook version (SUV) achieves 11.46 s, close to the highly optimized GNU sequential version (SGNU) at 10.33 s.

### Straightforward parallelization (PUV)

The standard task-parallel pattern for recursive algorithms is applied. A parallel region is created with a `single` thread initiating the recursion. Each recursive call is wrapped in `#pragma omp task`:

```c
void par_quicksort(int* A, long lo, long hi) {
    #pragma omp parallel
    {
        #pragma omp single
        par_quicksort_rec(A, lo, hi);
    }
}

void par_quicksort_rec(int* A, long lo, long hi) {
    if (lo < hi) {
        long r = seq_partition_L(A, lo, hi);
        #pragma omp task
        par_quicksort_rec(A, lo, r - 1);
        #pragma omp task
        par_quicksort_rec(A, r + 1, hi);
    }
}
```

PUV achieves only speedup 4.86 on 12 cores (2.36 s), far from PGNU's speedup of 11.03 (0.94 s). Two main problems explain this:

- Creating a large number of small OpenMP tasks causes excessive task pool management overhead. When a thread generates two tasks and puts both into the pool, it has nothing to do itself - it must go back to the pool and potentially pick up a task it just created.
- The `seq_partition_L` function is sequential, so partitioning of the entire input array is performed by one thread while others remain idle at the initial levels of the TRC. This is Amdahl's law in action.

### Optimization 1: Tail call optimization (TCO)

One recursive call is replaced with an iteration loop. The second recursive call `par_quicksort_rec(A, r+1, hi)` becomes `lo = r + 1` inside a `while` loop:

```c
void par_quicksort_rec(int* A, long lo, long hi) {
    while (lo < hi) {
        long r = seq_partition_L(A, lo, hi);
        #pragma omp task
        par_quicksort_rec(A, lo, r - 1);   // left part -> task pool
        lo = r + 1;                          // right part -> continue
    }
}
```

Only one task is put into the pool (the left part) while the current thread keeps processing the right part directly. This approximately halves the number of OpenMP tasks created. If the pivot is close to the median, the number of OpenMP tasks drops from `O(n)` to `O(log n)`.

A further optimization (not elaborated in the lecture) would be to put the larger part into the task pool and keep the smaller part, since uneven splits may otherwise waste work.

### Optimization 2: Task parallelism thresholding (ST)

When the subarray size drops below a threshold `n/(k*p)`, the algorithm switches to sequential QuickSort instead of creating new tasks. The parameter `k` controls over-decomposition:

- `k = 1` produces exactly `p` tasks for `p` threads, which does not allow load balancing
- `k = 5` or `k = 10` produces `k*p` tasks total, enabling automatic load balancing through the OpenMP runtime

The optimal `k` is system-dependent and must be determined experimentally.

```c
void par_quicksort_rec2(int* A, long lo, long hi, const long seq_thr) {
    while (lo < hi) {
        if ((hi - lo) < seq_thr) {
            seq_quicksort_L(A, hi, lo);   // sequential fallback
            return;
        }
        long r = seq_partition_L(A, lo, hi);
        #pragma omp task
        par_quicksort_rec2(A, lo, r - 1, seq_thr);
        lo = r + 1;
    }
}
```

The threshold is computed at the beginning as `seq_thr = (hi - lo + 1) / omp_get_num_threads() / k`.

### Optimization 3: Parallel partitioning (PP)

##### Why Lomuto's variant cannot be parallelized

In Lomuto's partitioning, each iteration depends on the previous one - both `i` and `j` advance sequentially left-to-right. There is no way to give meaningful independent work to `p` threads.

##### Hoare's partitioning - the parallelizable alternative

In Hoare's variant, index `i` traverses left-to-right and `j` traverses right-to-left. The process is called **neutralization**. Four cases arise when comparing `A[i]` with pivot and `A[j]` with pivot:

1. `A[i] < pivot` and `A[j] >= pivot` - both correctly placed, advance both pointers inward
2. `A[i] < pivot` and `A[j] < pivot` - left element correct, advance `i` rightward
3. `A[i] >= pivot` and `A[j] >= pivot` - right element correct, advance `j` leftward
4. `A[i] >= pivot` and `A[j] < pivot` - swap both, advance both pointers inward

In each iteration, at least one array element is neutralized. The key insight is that neutralization operations on different pairs of elements are independent - the only requirement is that no two threads work on the same array element.

```c
long seq_partition_H2(int* A, long lo, long hi) {
    long pivot = A[hi];
    long i = lo;
    long j = hi - 1;
    while (i < j) {
        if (A[i] >= pivot && A[j] < pivot) {
            swap(A, i, j); i++; j--;
        } else {
            if (A[j] >= pivot) j--;
            if (A[i] < pivot)  i++;
        }
    }
    if (A[j] < pivot) j++;
    swap(A, j, hi);
    return j;
}
```

##### Parallel partitioning with atomic capture

The indices `i` and `j` become shared variables. Each thread must **atomically capture** a unique value of `i` or `j` before working on that element. The naive attempt without synchronization is **incorrect** because increment and decrement are three operations (read, update, write back) and are not atomic - multiple threads can capture the same value.

The correct solution uses `#pragma omp atomic capture` (Fetch-and-Add):

```c
#pragma omp atomic capture
{ my_i = i; i++; }    // abbreviated as [my_i = i++]

#pragma omp atomic capture
{ my_j = j; j--; }    // abbreviated as [my_j = j--]
```

Each thread captures unique private copies `my_i` and `my_j`, performs neutralization on `A[my_i]` and `A[my_j]`, then captures new elements as needed. At the end, each thread is left with at most one dirty (incorrectly placed) element. Since `p << n`, sequential cleaning of at most `p` dirty elements is fast.

##### Why element-level partitioning is inefficient

Partitioning by individual array elements causes frequent atomic accesses into shared memory and thrashing of cache blocks by different threads (false sharing). With 100 million numbers and 12 threads, each partitioning step involves roughly 50 million neutralizations - doing an atomic capture for each is prohibitively expensive.

##### Block-level parallel partitioning

The solution is to capture and process **disjoint blocks** of `K` elements at a time:

```c
#pragma omp atomic capture
{ my_i = i; i += K; }   // capture a block of K elements
```

Key definitions and properties:

- **Dirty block**: contains elements both less than and greater than or equal to the pivot
- **Clean block**: all elements are correctly placed relative to the pivot
- After sequential neutralization of a left block `B_L(k)` and a right block `B_R(k)`, **at least one block becomes clean** and correctly placed
- Each thread ends with at most 1 dirty block
- Ideally, `K * sizeof(int)` should be a multiple of the cache block size

The thread traverses its left block with pointer `i_k` left-to-right and its right block with pointer `j_k` right-to-left, performing the same neutralization logic as in the single-element case but within block boundaries. When one block becomes clean, the thread captures a new partner block and continues.

```
par_partition_block pseudocode:
1. Each thread atomically captures a left and right block of size K
2. Loop: neutralize the block pair sequentially
   - If left block is clean: capture new left block
   - If right block is clean: capture new right block
3. Barrier
4. Sequential neutralization of remaining <= p dirty blocks
5. Return boundary index
```

After the main parallel loop and a barrier, the master thread sequentially cleans the remaining dirty blocks (at most `p` of them).

### Nested parallelism for parallel partitioning

Parallel partitioning requires **nested OpenMP parallelism**: the thread executing a task from the task pool must create its own team for multi-threaded partitioning. This is activated via `omp_set_max_active_levels(depth)` where `depth > 1`.

At the root of the TRC, all `p` threads can participate in the initial partitioning since the task pool is empty. Deeper in the tree, fewer threads are available per partition since work is distributed across multiple concurrent nodes. The maximum nesting depth must be controlled to avoid creating more threads than cores.

> The number of real threads with respect to the number of real cores should be more or less the same. Maybe you could have twice as many threads as cores, but not more.

### Pivot selection

In block-level neutralization, there is **no swap** of the pivot as the last element with the boundary element. Only the pivot value is needed during partitioning. The master thread returns the boundary index after cleaning dirty blocks, and the pivot ends up somewhere in the right part.

This decoupling allows using any pivot selection method independently of the partitioning mechanism:

- Median of 3 or 5 randomly selected elements
- Median of a larger number of equidistant element samples

Good pivot selection is particularly important at the top levels of the TRC, where poor pivots cause severely unbalanced splits.

### Load balancing for nested parallel regions

QuickSort's data sensitivity complicates load balancing. The ratio of the left and right part sizes after partitioning depends on pivot quality. The idea is to pass the left/right size ratio as an argument to the recursive function, then use the `num_threads` clause of `#pragma omp parallel` to allocate threads proportionally. For example, a 2:5 split should assign threads in roughly a 2:5 ratio. Each part must receive at least 1 thread.

### Final performance evaluation

Sorting 100 million random integers on 12-core Intel Xeon E5-2680v3:

|Variant|Runtime|Speedup (vs SUV)|
|---|---|---|
|SGNU|10.33 s|-|
|SUV|11.46 s|1.00|
|PGNU|0.94 s|11.03|
|PUV|2.36 s|4.86|
|TCO/ST|~1.7 s|~6.7|
|TCO/ST/PP|~1.0 s|~11|

TCO/ST/PP (all three optimizations combined) brings performance to within a few percent of PGNU. The remaining gap is due to additional optimizations in GNU's `libstdc++`: cache memory effects, switching to InsertionSort for small inputs, better pivot selection, and other refinements.

> This near-parity with GNU is specific to random inputs. For non-random data distributions (sorted, reverse-sorted, many duplicates), GNU's implementation would significantly outperform the textbook version due to its many additional optimizations.

### Summary of the three optimizations

- **TCO** (Tail Call Optimization): replace one recursive call with iteration, halving task creation. Reduces OpenMP tasks from `O(n)` to `O(log n)` for median pivots.
- **ST** (Sequential Thresholding): switch to sequential sort below threshold `n/(k*p)`, avoiding overhead of tiny tasks while enabling load balancing through over-decomposition.
- **PP** (Parallel Partitioning): use Hoare's neutralization with block-level atomic capture and nested parallelism to parallelize the partitioning step, addressing the Amdahl's law bottleneck.

---

### Potential exam questions

1. Why is Lomuto's partitioning unsuitable for parallelization, and what property of Hoare's partitioning makes it parallelizable?
2. Explain the concept of neutralization in Hoare's partitioning. What are the four cases, and why is at least one element neutralized per iteration?
3. Why is the first attempt at parallel partitioning (with shared `i`, `j` without synchronization) incorrect? What OpenMP directive fixes it?
4. What is the purpose of `#pragma omp atomic capture` in parallel partitioning? How does it differ from `#pragma omp critical`?
5. Explain why element-level parallel partitioning is inefficient and how block-level partitioning solves this problem. What is the dirty/clean block invariant?
6. Why does parallel partitioning in QuickSort require nested OpenMP parallelism? How is nesting depth controlled and why must it be limited?
7. Describe the three optimizations (TCO, ST, PP) applied to the naive parallel QuickSort. What specific problem does each one address?
8. How does tail call optimization reduce the number of OpenMP tasks from `O(n)` to `O(log n)`? What assumption about the pivot is required?
9. What is the role of the parameter `k` in the thresholding formula `n/(k*p)`? What happens when `k = 1` versus `k = 10`?
10. How is pivot selection decoupled from block-level partitioning? Why is good pivot selection particularly important at the top levels of the TRC?
11. Explain the load balancing problem for nested parallel regions in parallel QuickSort. How can threads be allocated proportionally to subarray sizes?
12. Compare the parallel performance of PUV, TCO/ST, and TCO/ST/PP. What is the main bottleneck at each stage and how is it resolved?