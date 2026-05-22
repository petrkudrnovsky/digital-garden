
> [!tldr] First 5 minutes of hell
> MergeSort is a recursive Divide-and-Conquer algorithm. It halves the input array, recursively sorts both halves, then merges them together and returns.
> - is data independent (the distribution and structure of data do not affect the performance)
> - is out-of-place (needs an extra array of the same size as the input)
> - is stable (preserves the order of equal elements)
> 
> Why does not the standard task parallelism work?
> - because unlike the QuickSort (which does the work and then recurses), the MergeSort first recurses and then does the work
> - with the recursion first, it creates many small OpenMP tasks, which are merging only individual elements (to the array of 2) => massive task management overhead + massive false sharing (all threads essentially competing for the cache blocks, because they merge and write next to each other) 
> - just a note: the directive `taskwait` is needed before merging to ensure that both sides are recursively sorted (before merging them)
> 
> How to improve this?
> - thresholding the input size for creating parallel OpenMP tasks - do not create many small OpenMP tasks and do the sequentially (faster)
> - instead of Divide-and-Conquer, do Divide-and-KeepOneHalf - same problem as in the QuickSort, the parent thread creates two tasks and waits idling on the `taskwait`, better is to keep it busy by executing one of the halves
> - parallelization of the merge operation -> see Parallel 2-way merge
> 	- because the sequential merging operation is the biggest bottleneck
>   
> Parallel 2-way merge
> - suppose, we have got sorted arrays C and D and have to merge them using $p$ threads
> - split C into $p$ chunks and D into $p$ chunks so that:
> 	- if we merge chunk 1 from C with chunk 1 from D, we get exactly `n/p` elements (of the final array having $n$ elements)
> 	- all elements in both chunks 1 are smaller than all elements in both chunks 2 and so on
> - each thread does it's own sequential merge on it's chunk and writes the sorted merge independently to the final array part
> - how to find the splitters (points, where to split C and D)?
> 	- get the matrix (below) representing the merge (moving right = take from D, moving down = take from C)
> 	- draw $p-1$ equidistant antidiagonals, which cut the staircase into $p$ slices, where each slice represents a slice of C (the rows it spans) and the slice of D (the columns it spans) for the given thread
> 	- so each thread:
> 		- finds it's splitter (using binary search find where it's antidiagonal crosses the staircase)
> 		- waits on barrier until all threads find their splitters
> 		- reads it's neighbours' splitters to figure out it's slices of C and D (where they start and end)
> 		- do a sequential merge of the slices, producing an output of size `n/p` and write to the determined location in the output array (no conflicts between threads)
> - total cost is then $O(n/p + log(n))$ 
> 	- $O(log(n))$ for binary search to find the splitter
> 	- $O(n/p)$ to do the local merges


### MergeSort properties relevant to parallelization

MergeSort is a recursive Divide-and-Conquer algorithm that is **data oblivious** (performance does not depend on input distribution), **out-of-place** (requires an auxiliary array of the same size as the input), and **stable** (the ordering of equal elements is preserved). Unlike QuickSort, it cannot be implemented with only Compare-and-Swap operations - it requires data movement (copying elements to and from the auxiliary array).

The critical structural property is that MergeSort performs its main work (merging) **on the way back** from recursion, not on the way down. The recursive calls for halves of input arrays are made first, and only after both return does the merge operation execute. This is the opposite of QuickSort, which performs partitioning first and then recurses.

### Sequential baseline (SUV)

The textbook sequential implementation uses a `seq_merge` function that merges two sorted halves from array `B` into array `A`. The recursive `seq_mergesort_rec` divides the array in half, recursively sorts both halves, then merges them. The top-level function allocates an auxiliary array `B` and copies `A` into it before starting.

```c
void seq_merge(int* A, long lo, long middle, long hi, int* B) {
    long i = lo; long j = middle;
    for (long k = lo; k < hi; k++) {
        if ((i < middle) && ((j >= hi) || (A[i] <= A[j])))
            B[k] = A[i++];
        else
            B[k] = A[j++];
    }
}

void seq_mergesort_rec(int* B, long lo, long hi, int* A) {
    if ((hi - lo) < 2) return;
    long middle = (hi + lo) / 2;
    seq_mergesort_rec(A, lo, middle, B);
    seq_mergesort_rec(A, middle, hi, B);
    seq_merge(B, lo, middle, hi, A);
}

void seq_mergesort(int* A, long n) {
    int* B = (int*)malloc(n * sizeof(int));
    for (long i = 0; i < n; i++) B[i] = A[i];
    seq_mergesort_rec(B, 0, n, A);
    free(B);
}
```

SUV achieves 13.86 s for 100 million random integers, compared to SGNU at 10.77 s.

### Straightforward parallelization (PUV) - the catastrophe

The parallelization follows the same task-parallel pattern as QuickSort: `#pragma omp parallel` with `#pragma omp single`, two `#pragma omp task` directives for recursive calls. The only structural difference from QuickSort is that `#pragma omp taskwait` is required before the merge to ensure both recursive sorts have completed.

```c
void par_mergesort_rec(int* B, long lo, long hi, int* A) {
    if ((hi - lo) < 2) return;
    long middle = (hi + lo) / 2;
    #pragma omp task
    par_mergesort_rec(A, lo, middle, B);
    #pragma omp task
    par_mergesort_rec(A, middle, hi, B);
    #pragma omp taskwait
    seq_merge(B, lo, middle, hi, A);
}
```

> The parallel version is a catastrophe. 200 seconds instead of 10 sequentially. You run something which, using one thread, takes 10 seconds. You run it on a 12-core CPU, and the achievement you made is 20 times slower than the sequential part.

MergeSort PUV takes **198.46 s** on 12 threads - 14 times slower than the sequential 13.86 s. This contrasts sharply with QuickSort PUV which achieved a 4.86x speedup.

### Why PUV fails: work order and false sharing

The root cause is the **inverted work-recursion order** compared to QuickSort:

- **QuickSort PUV** performs the work first (function `partition`), then calls recursively on subarrays of various sizes. The largest, most useful tasks appear early at the top of the TRC.
- **MergeSort PUV** makes the recursive calls for halves first. Only after the recursion bottoms out does the work happen at return via the `merge` operation.

The consequence is that after the recursion fully unwinds, approximately `n/2` = 50 million tiny OpenMP tasks are created at the bottom of the recursion tree, each merging at most 2 elements. These tiny tasks are simultaneously executed by all threads, which access neighboring array indices. With a cache line holding 64 integers, up to 32 threads compete for the same cache block, and each tiny merge invalidates the remaining copies. This massive **false sharing** is encoded in the very logic of the algorithm and is what destroys performance.

### Three improvements for parallel MergeSort

1. **Thresholding** of the input size for creating parallel OpenMP tasks
2. **Divide-&-KeepOneHalf** instead of creating two new parallel tasks - a new task is created only for the left half, while the right half is completed by the current thread
3. **Parallelization of the `seq_merge` operation**

### Improvement 1+2: Thresholding and Divide-&-KeepOneHalf (PUV+ST)

Both optimizations are applied together. When the subarray is below the threshold, the algorithm falls back to sequential MergeSort. Only the left half is dispatched as a new OpenMP task; the current thread continues with the right half directly. This avoids the situation where a thread creates two tasks, waits for both via `taskwait`, and has nothing to do during that wait.

```c
void par_mergesort_rec2(int* B, long lo, long hi, int* A, long seq_thr) {
    if ((hi - lo) < seq_thr) {
        seq_mergesort_rec(B, lo, hi, A);   // sequential fallback
        return;
    }
    long middle = (hi + lo) / 2;
    #pragma omp task
    par_mergesort_rec2(A, lo, middle, B);   // new OpenMP task (left)
    par_mergesort_rec2(A, middle, hi, B);   // current thread (right)
    #pragma omp taskwait
    seq_merge(B, lo, middle, hi, A);
}
```

PUV+ST achieves **3.14 s** (down from 198.46 s), giving speedup 4.41 over SUV. However, it is still 4.5x slower than PGNU (0.98 s). The remaining bottleneck is the sequential `seq_merge` operation - to close the gap with PGNU, the merge itself must be parallelized.

### Improvement 3: Parallel 2-way merge

##### The problem with sequential merge

The classical `seq_merge` is inherently sequential: it traverses two sorted arrays left-to-right, compares the head elements, moves the smaller one to the output, and advances the corresponding pointer. There is a data dependency between iterations - you must know which element was removed before proceeding to the next comparison. This makes naive parallelization impossible.

##### The binary matrix construction

Given two sorted arrays `C` and `D` of size `k = n/2`, define a conceptual **binary matrix** `M[k, k]` where `C` is mapped to row indices and `D` to column indices:

$$M[i, j] = \begin{cases} 0 & \text{if } C[i] > D[j] \ 1 & \text{otherwise} \end{cases}$$

Because both `C` and `D` are sorted, the matrix has two key properties:

- Each **row** is a sorted binary sequence `0^r 1^(k-r)`, `0 <= r <= k` (zeros followed by ones), containing a unique jump from 0 to 1
- Each **column** is an inversely sorted binary sequence `1^s 0^(k-s)`, `0 <= s <= k` (ones followed by zeros)

The boundary between 0s and 1s forms a **rightwards-downwards-staircase-like curve** from the upper-left corner to the lower-right corner (the "thick blue line" in the slides).

![[Pasted image 20260520170325.png]]
- moving right = take from D
- moving down = take from C
- the matrix is only a mental model, the binary search evaluates the 0/1 on the few places it visits
##### Concrete example

For `n = 16`, `p = 4`, with sorted arrays `C = [4, 6, 7, 11, 13, 14, 15, 16]` and `D = [1, 2, 3, 5, 8, 9, 10, 12]`, the matrix is constructed row by row: for a given `C[i]`, as you move across columns (increasing `D[j]` values), at some point `D[j]` exceeds `C[i]` and entries switch from 0 to 1. The staircase boundary always progresses rightward and downward.

##### Antidiagonal splitters

Place `p - 1` **equidistant antidiagonals** at diagonal distance `k/p` in the matrix. Let `x_1, ..., x_{p-1}` denote the intersections of these antidiagonals with the staircase boundary, and let `x_0` and `x_p` be the terminal points of the main diagonal.

For `i = 1, ..., p`, define:

- `C_i` = the part of `C` bounded by the horizontal projection of intersections `x_{i-1}` and `x_i`
- `D_i` = the part of `D` bounded by the vertical projection of intersections `x_{i-1}` and `x_i`

Let `X_i = merge(C_i, D_i)` for `i = 1, ..., p`. Then:

**(1)** For `i = 1, ..., p`, `X_i` is a sorted array of size `|X_i| = n/p`.

**(2)** For `i = 1, ..., p-1`, the last element of `X_i` is less than the first element of `X_{i+1}`.

**Corollary**: The array constructed by concatenating `X_1 ... X_p` is sorted.

Each "color group" of numbers always contains exactly `n/p` elements (a consequence of equidistant antidiagonals), and all elements of one color are strictly less than all elements of the next color. Each thread receives its color group, performs an independent sequential sub-merge, and writes to a known contiguous segment of the output array. There is no conflict between threads.

##### Computing splitters via binary search

Each thread `tau_i` can independently compute its splitter `x_i` by performing **binary search** along the `i`-th antidiagonal. The antidiagonal represents a sorted binary sequence (since the staircase boundary is monotone), so the unique jump from 0 to 1 can be found in `O(log n)` time.

After all `p - 1` threads simultaneously compute their splitters in time `O(log n)`, a barrier synchronizes them. Then each thread reads its own splitter and its neighbor's to determine the start and end indices of its sub-arrays `C_i` and `D_i`, and proceeds with its local sequential 2-way merge.

The complete parallel 2-way merge therefore proceeds as:

1. Each thread `tau_i` computes `x_i` via binary search on the `i`-th antidiagonal - `O(log n)` time
2. Barrier
3. Each thread reads `x_{i-1}` and `x_i` to determine `C_i`, `D_i`
4. Each thread performs sequential `merge(C_i, D_i)` and writes `X_i` into the preallocated disjoint part of the output array - `O(n/p)` time

### Performance of PUV+ST+PM

Adding parallel 2-way merge (PM) to the thresholded version yields PUV+ST+PM at **2.01 s**, which is speedup 6.91 over SUV. However, this is still significantly worse than PGNU (0.98 s).

The 2-way merge implementation may still suffer from false sharing. The parallel 2-way merge is not as efficient as the parallel partitioning in QuickSort. The way to further improve MergeSort for `p << n` is to use **p-way merge** instead (PMWMS), which is the basis of the GNU library's MergeSort implementation.

> The principle behind the best known parallel merge sort is not based on two-way merge, even though it seems perfect. If you are given the task to find the best possible merge sort with p threads from the beginning, you should forget about standard merge sort.

### Performance summary

|Variant|Runtime|Speedup (vs SUV)|
|---|---|---|
|SGNU|10.77 s|-|
|SUV|13.86 s|1.00|
|PGNU|0.98 s|~14.1|
|PUV|198.46 s|0.07 (slowdown!)|
|PUV+ST|3.14 s|4.41|
|PUV+ST+PM|2.01 s|6.91|
|PMWMS|~1.0 s|~14|

### Key conceptual contrasts: QuickSort vs MergeSort parallelization

QuickSort does work on the way **down** the recursion (partition first, then recurse). The largest tasks appear at the top of the TRC, which is favorable for early parallel utilization. MergeSort does work on the way **back** from recursion (recurse first, then merge). This creates an explosion of tiny tasks at the bottom of the TRC, which causes catastrophic false sharing in the naive parallelization.

The `taskwait` directive is essential in MergeSort's parallel version because the merge cannot begin until both halves are sorted. In QuickSort, the partitioning happens before recursion, so no `taskwait` is needed between the two recursive task calls.

---

### Potential exam questions

1. Why does the straightforward parallelization (PUV) of MergeSort result in a 14x slowdown compared to sequential, while QuickSort PUV achieves a 4.86x speedup? Explain the structural difference in work order.
2. What is false sharing and why does MergeSort PUV suffer from it catastrophically? How many tiny tasks are created and what cache effects occur?
3. Explain the Divide-&-KeepOneHalf optimization for MergeSort. Why is it preferable to creating two tasks, and what happens when a thread creates two tasks and then waits via `taskwait`?
4. Why is `#pragma omp taskwait` required in the parallel MergeSort before the merge call, but not needed in the parallel QuickSort between the two recursive calls?
5. Describe the binary matrix `M[k,k]` construction for parallel 2-way merge. What are the properties of each row and column? What shape does the 0/1 boundary form?
6. How are antidiagonal splitters used to decompose the merge into `p` independent sub-merges? Prove or argue that each `X_i = merge(C_i, D_i)` has exactly `n/p` elements.
7. How does each thread compute its splitter `x_i`? What is the time complexity of this step and why can it be done independently by all threads?
8. What is the complete time complexity of the parallel 2-way merge? Break it down into the splitter computation and the merge phases.
9. Why is the parallel 2-way merge (PUV+ST+PM at 2.01 s) still significantly slower than PGNU (0.98 s)? What fundamental approach does PGNU use instead?
10. Compare the thresholding optimization in MergeSort vs QuickSort. What additional optimization (Divide-&-KeepOneHalf) is needed for MergeSort that is analogous to TCO in QuickSort?
11. Given `n = 16`, `p = 4`, `C = [4, 6, 7, 11, 13, 14, 15, 16]`, `D = [1, 2, 3, 5, 8, 9, 10, 12]`, construct the binary matrix and identify the antidiagonal splitters. Show how the merge is decomposed into 4 independent sub-merges.
12. Why is the sequential `seq_merge` inherently sequential? What data dependency prevents straightforward parallelization?