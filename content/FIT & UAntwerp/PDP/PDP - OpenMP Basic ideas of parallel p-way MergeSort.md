
> [!tldr] First 5 minutes of hell
> See the [[PDP - OpenMP Basic ideas of parallel 2-way MergeSort]].
> - 2-way merge implementation may suffer from false sharing and also, due to the recursive MergeSort nature, not all threads are 100% utilized all the time
> 
> The idea is to forget the traditional recursive MergeSort and:
> - split the array to `n/p` parts, each thread will sort its part using the best sequential algorithm there is
> - so we have $p$ sorted arrays and now we need to merge them together
> 	- the idea is that:
> 		- thread 0 will merge the smallest `n/p` elements
> 		- thread 1 will merge the next `n/p` elements
> 		- thread $p-1$ will merge the largest `n/p` elements
> 	- each thread will then compute it's array of splitters (= "at which index does my sequence in each of the sorted array start"?)
> 	- it will than sequentially merge the first `n/p` elements from all sorted arrays starting from the splitter indexes
> 	- and it will write them to the final sorted array B at index `i * n/p` (`i` is the rank of the thread and there are `i-1` blocks of smaller elements before it)
> 		- so the sum of all elements in all sorted arrays before the splitter indexes is equal to `i * n/p`
> 
> Splitters_by_Rank function
> - essentially perform a binary search across all sorted arrays
> 	- for each sorted array, keep two indexes as bounds (at the beginning, the bounds bound the whole array)
> 	- pick a pivot between those bounds for each sorted array and count the number of elements that are less than the picked pivot (and sum the counts across all sorted arrays)
> 		- the sum is the global rank of the pivot in the output array 
> 		- if the global rank is bigger than the `i * n/p` target rank -> pull the right bounds and repeat
> 		- if the global rank is too small, pull the left bounds and repeat
> 	- repeat until the bounds collapse => we have found the splitters in each sorted array
> 		- the goal is to find splitters (values) from which the current thread can start merging (and be sure that it will not conflict with other threads)
> 
> Benefits:
> - there are no conflicts between threads, each one of them is doing own work and the final write to the sorted array is also without conflicts
> - small synchronization overhead - only two barriers (after each phase) compared to a lot of `taskwait` directives on each level of the recursion tree in traditional MergeSort
> - thread utilization is very good (especially at the beginning, where each thread sorts it's own part)


### Motivation: why p-way merge instead of parallel 2-way merge

Adding parallel 2-way merge to the thresholded MergeSort (PUV+ST+PM) achieves 2.01 s with speedup 6.91 over SUV, but remains significantly worse than PGNU (0.98 s). The 2-way merge implementation may still suffer from false sharing, and the recursive structure of traditional MergeSort inherently limits how efficiently threads can be utilized.

> The principle behind the best known parallel merge sort is not based on two-way merge, even though it seems perfect. If you are given the task to find the best possible merge sort with p threads from the beginning, you should forget about standard merge sort.

The way to improve MergeSort for `p << n` is to use **p-way merge**. This variant is called **Parallel Multi-Way MergeSort (PMWMS)** and is the basic component of the PGNU MergeSort implementation in `libstdc++`.

The fundamental insight is to abandon the recursive MergeSort structure entirely. Instead: given `p` threads and `n` numbers, each thread takes `n/p` numbers and sorts them locally using the best available sequential sort. Up to this point, all `p` threads work at 100% utilization with no synchronization overhead. The remaining problem is how to merge `p` sorted sequences of size `n/p` using `p` threads, keeping all of them busy.

### The principle of p-way PMWMS

The algorithm proceeds in four synchronized phases:

**Phase 1 - Local sort**: `p` threads split the input unsorted array of size `n` into `p` parts `S[0], ..., S[p-1]`, each of size `n/p`. Every thread `tau_i` sorts sequentially its part `S[i]`.

**Phase 2 - Splitter computation** (after barrier): Every thread `tau_i`, `i > 0`, computes its array of `p` **splitters** `splitters_ar[i]`, which are indices into the `p` sorted sequences `S[0], ..., S[p-1]`. These splitters must satisfy two properties:

- The sum of sizes of disjoint parts defined in `S[0], ..., S[p-1]` by two neighboring `splitters_ar[i]` and `splitters_ar[i+1]` has total size exactly `n/p`
- All numbers before `splitters_ar[i]` are less than or equal to all numbers after `splitters_ar[i]`

**Phase 3 - Cut-out extraction** (after barrier): Every thread `tau_i` takes its `p` disjoint sorted parts (cut-outs) from all `S[j]`, defined by its splitters, of total size `n/p`.

**Phase 4 - Sequential p-way merge**: Every thread `tau_i` merges its `p` cut-outs into its output contribution `B[i]` of size `n/p`. Different threads contribute to different (disjoint) parts of the output array `B`.

### PMWMS pseudocode

```c
int S[p][n/p], splitters_ar[p][p], my_tuple[p][n/p];

void ParMultiWayMergeSort(int* A, int n) {
    int* B = (int*)malloc(n * sizeof(int));
    #pragma omp parallel
    {
        my_id = omp_get_thread_num();
        S[my_id] = A[my_id*n/p .. (my_id+1)*n/p - 1];
        seq_sort(S[my_id]);    // Phase 1: each thread sorts its part

        #pragma omp barrier

        splitters_ar[my_id] = Splitters_by_Rank(S, my_id * n/p);
        // Phase 2: each thread computes its array of splitters

        #pragma omp barrier

        my_tuple[my_id] = split(S, splitters_ar, my_id);
        // Phase 3: each thread extracts p cut-outs from S[0]..S[p-1]

        B[my_id*n/p .. (my_id+1)*n/p - 1] =
            SeqMultiWayMerge(my_tuple[my_id], p);
        // Phase 4: sequential p-way merge of p cut-outs
    }
}
```

### Concrete example: 3-way PMWMS with n = 27, p = 3

The input array of 27 numbers is split into three parts of 9 elements each. After each thread sorts its part:

```
S[0] = [1, 3, 6, 7, 12, 19, 21, 24, 27]
S[1] = [4, 9, 10, 13, 14, 16, 18, 20, 23]
S[2] = [2, 5, 8, 11, 15, 17, 22, 25, 26]
```

Each thread `tau_i` (for `i > 0`) computes splitters - indices into all three sorted sequences such that the cut-outs between neighboring splitters sum to exactly 9 elements. Visually, each thread's cut-outs are color-coded across all three sequences. For example, `tau_0` receives the smallest 9 numbers from across all three sequences (e.g., the "red" numbers `{1, 2, 3, 4, 5, 6, 7, 8, 9}`), `tau_1` receives the next 9, and `tau_2` receives the largest 9.

Each thread then performs a sequential 3-way merge of its 3 cut-outs (one from each `S[j]`) into a sorted output segment `B[i]` of size exactly 9.

![[Pasted image 20260520174136.png]]

### The `Splitters_by_Rank` algorithm

This is the most important algorithmic component of PMWMS. Each thread `tau_i` (`i > 0`) computes its splitters using its **rank** `my_id * n/p`, which is the index where its contribution in the output array starts.

The function takes the shared array of `p` sorted subarrays `S[0], ..., S[p-1]` of sizes `n/p` and the rank. It generates an array of `p` splitters such that the total number of elements to the left of the splitters, summed over all `S[i]`, equals exactly the given rank.

The algorithm uses **pivotization** - an iterative narrowing of bounds:

```c
void Splitters_by_Rank(int* S, int rank) {
    int L[p], R[p], m[p]; int v;
    for (i = 0; i < p; i++) {
        L[i] = 0; R[i] = n/p - 1;
    }
    while (there exists i such that L[i] < R[i]) {
        v = Pickup_Random_Pivot(S[0],..,S[p-1],
                                L[0],R[0],..,L[p-1],R[p-1]);
        for (i = 0; i < p; i++)
            m[i] = binarySearch(v, S[i]);
        if (m[0] + .. + m[p-1] >= rank)
            R[0],..,R[p-1] = m[0],..,m[p-1];
        else
            L[0],..,L[p-1] = m[0],..,m[p-1];
    }
    return L[0],..,L[p-1];   // array of splitters
}
```

The pivotization loop works as follows:

1. Each thread maintains left and right boundary arrays `L[i]` and `R[i]` for all `p` sorted sequences, initially covering the full range `[0, n/p - 1]`
2. A random pivot `v` is chosen from within the current bounds
3. Binary search determines the rank of `v` in each sequence `S[i]`, yielding `m[i]`
4. The **global rank** `m[0] + ... + m[p-1]` is compared with the requested rank
5. If the global rank is too large (>= rank), the right boundaries are narrowed: `R[i] = m[i]`
6. If the global rank is too small, the left boundaries are narrowed: `L[i] = m[i]`
7. The loop terminates when left and right boundaries coincide in all sequences

The number of iterations depends on how lucky the random pivot selection is - this is a strongly data-dependent complexity.

### Sequential p-way merge via binary heap

The sequential p-way merge step within each thread is best implemented using a **binary heap** (min-heap). The heap maintains the head elements of each of the `p` cut-out sequences. Finding and extracting the minimum of `p` heads costs `O(log p)` time per element. Since each thread merges a total of `n/p` elements, the sequential p-way merge costs `O((n/p) log p)`.

### Asymptotic parallel time

$$T(n, p) = O\left(\frac{n}{p} \log \frac{n}{p} + p \log \frac{n}{p} \cdot \log n + \frac{n}{p} \log p\right)$$

The three terms correspond to:

- **First term** `(n/p) log(n/p)`: sequential sort of `n/p` numbers by each thread (Phase 1)
- **Second term** `p log(n/p) log n`: splitter computation - `log n` iterations of the pivotization loop, each performing `p` binary searches in arrays of size `n/p` (Phase 2)
- **Third term** `(n/p) log p`: sequential p-way merge of `p` cut-outs totaling `n/p` elements using a binary heap (Phase 4)

For small `p` and large `n` (the typical HPC scenario), the **first term dominates**, meaning the algorithm achieves near-optimal parallel time - essentially the time to sequentially sort `n/p` elements.

### Final performance analysis

Sorting 100 million random integers on 12-core Intel Xeon E5-2680v3:

|Variant|Runtime|Speedup (vs SUV)|
|---|---|---|
|SGNU|10.77 s|-|
|SUV|13.86 s|1.00|
|PGNU|0.98 s|~14.1|
|PUV|198.46 s|0.07 (slowdown)|
|ST (threshold + D&KOH)|3.14 s|4.41|
|ST/PM (+ par. 2-way merge)|2.01 s|6.91|
|PMWMS (p-way, seq. sort with SGNU)|~1.0 s|~14|

PMWMS is essentially competitive with PGNU, with only negligible differences. Because the algorithm is largely **data-oblivious** (the merge phase does not depend on input distribution), it works well regardless of the input data. This data-obliviousness is precisely why the GNU library adopted this approach. PGNU retains a slight advantage due to additional optimizations in `libstdc++`.

### Why PMWMS outperforms recursive parallel MergeSort

The key advantages of the p-way approach over recursive 2-way MergeSort are:

**Maximum thread utilization during sorting**: In Phase 1, all `p` threads sort their `n/p` portions simultaneously with zero synchronization overhead. There is no tree of recursive calls with gradually increasing parallelism - all threads are busy from the start.

**No false sharing**: Each thread works on its own contiguous `n/p` portion during sorting (Phase 1) and writes to its own contiguous `n/p` portion of the output during merging (Phase 4). The cut-outs read during the p-way merge are from different regions of different arrays.

**Single synchronization point**: Only two barriers are needed (after Phase 1 and after Phase 2), compared to the recursive structure which requires `taskwait` at every level of the TRC.

**Scalability**: For `p << n`, the first term dominates the parallel time, giving near-linear speedup. The splitter computation (second term) involves `p` binary searches per iteration - for small `p`, this is negligible.

### Relationship to exam question scope

This question focuses on the p-way merge approach. Be prepared to contrast it with the 2-way approach (exam question 15) and explain why abandoning the recursive MergeSort structure is necessary to achieve GNU-level performance. The `Splitters_by_Rank` pivotization algorithm is the core technical contribution - understand the narrowing-of-bounds loop, the role of binary search in computing per-sequence ranks, and the termination condition.

---

### Potential exam questions

1. Describe the four phases of the PMWMS algorithm. What synchronization (barriers) is needed between them and why?
2. What is the role of the `Splitters_by_Rank` function? What inputs does it take and what does it produce?
3. Explain the pivotization loop in `Splitters_by_Rank`. How are the left and right boundaries narrowed, and what determines whether to narrow from the left or the right?
4. What is the asymptotic parallel time `T(n,p)` of PMWMS? Identify each of the three terms and which phase it corresponds to. Which term dominates for small `p` and large `n`?
5. Why is a binary heap used for the sequential p-way merge? What is the time complexity per element and for the entire merge of `n/p` elements from `p` sequences?
6. Why does PMWMS achieve near-optimal parallel time while recursive parallel 2-way MergeSort (PUV+ST+PM) does not? What structural difference explains the performance gap?
7. In the 3-way PMWMS example with `n = 27` and `p = 3`, each thread must receive cut-outs totaling exactly 9 elements. Explain how the splitters ensure this property.
8. Why is PMWMS largely data-oblivious? How does this contrast with parallel QuickSort, and why did the GNU library adopt the p-way approach?
9. How many iterations does the pivotization loop in `Splitters_by_Rank` require? Why is this data-dependent?
10. Compare the thread utilization patterns of PMWMS vs recursive MergeSort. At what point during execution are all `p` threads first fully busy in each approach?
11. What is the relationship between `Splitters_by_Rank` and the concept of rank in sorted sequences? How does the global rank `m[0] + ... + m[p-1]` relate to the requested rank?
12. Explain why only two barriers suffice in PMWMS, whereas recursive parallel MergeSort requires `taskwait` at every level of the TRC. What is the practical impact on performance?