

> [!tldr] First 5 minutes in hell
> This QuickSort is build on SPPS (segmented PPS) - it does all the data manipulation. It is trivially scalable and can be implemented on both shared and distributed memory easily (without any algorithmic changes).
> - only data manipulation phase is the final permutation (done via writing to the shared memory in OpenMP or via communication in MPI)
> 
> It is data-sensitive (each segment may be of different size), but in the reality, it is data-insensitive, because each thread processes the same amount of elements $n/p$.
> - normal parallel QS is data-sensitive, the run depends on the choice of the pivot
> - it is therefore load-balanced (regardless of data distribution)
> - the run of the SPPS each iteration is constant
> 
> It is out-of-place (each iteration, each process scannes/permutes the input and writes new data into a new array (doing PPS)). Loses in-place property, but gains stability (elements with the same value do not get swapped in the process).
> 
> Process:
> - split the input array into $p\lt n$ processes/processors/threads => we have one global segment $A$
> 	- firstly ask, if $A$ is already sorted:
> 		- for each neighboring pair of values in $A$, calculate $f_i$ whether they are in the right order (1 = right order, 0 = wrong order)
> 		- perform parallel reduction with operation AND and if I get 1 at the end (so all of $f_i=1$, then it is sorted)
> 	- parallel reduction costs $O(\log p)$ and $n/p$ local AND operations
> - in general:
> 	- for all segments $S$ in $A$, do in parallel:
> 		- select first element in $S$ as pivot 
> 		- broadcast the pivot within the section $S$ ($S$ could span over more processes) using SPPS with the same
> 			- using degenerate operation $a \oplus b = a$ (keep-left operand), which while using segmented PPS effectively broadcasts the left-most element in the segment across the whole segment (so all processes holding this segment know the value of the pivot)
> 		- once having the pivot, mark each $a_i$ in $S$ with one of three symbols $g=\{\lt, =, \gt\}$ (in parallel)
> 		- apply SPPS to compute new position indexes of elements with the $g=\lt$ flag (each element in $S$ has 1 = has this flag or 0 = does not have this flag) within the given segment
> 			- + broadcast the offset of this soon-to-be segment along the whole segment (using the same operation, but from right-to-left (keeping the right operand)):
> 				- $|S_\lt|$ is the offset of $S_=$
> 				- thanks to the nature of SPPS, the count of elements in this subsection is in the right-most element (the final running count)
> 		- apply SPPS to get new positions for elements with $g='='$
> 			- I have the indexes 0,1,2,..., $S_=-1$, but actually I need to add the previously broadcasted $|S_\lt|$ to each index to shift them to the correct index 
> 			- broadcast the offset $|S_\lt|+|S_=|$
> 				- $|S_\lt|+|S_=|$ = offset of $S_\gt$
> 		- apply SPPS to get new positions for elements with $g=\gt$ the same way
> 			- the index positions need to be shifted by the broadcasted $|S_\lt|+|S_=|$ in the same way 
> 		- permute (packaging routine) $S$ to create 3 subsegments 
> 	- when each segment is of length of one, the array is fully sorted
> 
> Complexity:
> - if $A$ is random, then the QuickSort is expected to complete in $O(\log n)$ iterations (PPS runs on each iteration) => $T_{\text{QuickSort}}(n, p) = O(\log n \cdot T_{\text{PPS}}(n, p))$
> 	- if the pivot is random-ish, the segments halve each round, therefore it is logartihmic (with bad pivots, it could have $n$ linear count of iterations)


### Why this variant of QuickSort

The in-place parallel QuickSort already discussed in Lectures 4-5 uses swaps inside one array; it is fast in practice but inherently load-imbalanced because the size of each task depends on the pivot selection and the data distribution. The variant treated here is fundamentally different:

- It is _out-of-place_: each iteration copies the input array into another array rather than swapping in place. This loses the in-place property but gains _stability_.
- It is built _solely_ on packing, so all the data manipulation is expressed as SPPS calls. Thanks to the properties of SPPS, the algorithm is trivially scalable, automatically load-balanced, and can be implemented on both shared and distributed memory without algorithmic changes.

This makes it an exceptional algorithm in the QuickSort family: data-sensitive in principle (segment sizes depend on the input distribution), but data-insensitive in execution (every processor processes its share of the array on every iteration).

### The basic step: split a segment into three sub-segments

QuickSort proceeds by repeatedly _splitting_ each current segment into three sub-segments based on a pivot:

- $S_<$: elements strictly less than the pivot.
- $S_=$: elements equal to the pivot.
- $S_>$: elements strictly greater than the pivot.

Within each sub-segment the original relative order is preserved (this is the stability property). The algorithm denotes each input number with one of three flags - $<$, $=$, $>$ - by comparing it against the pivot, then packs first the $<$ numbers, then the $=$ numbers, and finally the $>$ numbers into the output array.

After one iteration, one input segment becomes three output segments (the middle one $S_=$ is already in its final sorted position, since all its members equal the pivot). The algorithm then recurses on $S_<$ and $S_>$ within each segment in parallel. The number of segments grows at every iteration until eventually each segment has length one, at which point the array is sorted.

### Pivot selection

The choice of pivot is deliberately simple: the _first element_ within each current segment is used as that segment's pivot. The lecturer noted that the focus of the lecture is the parallel structure, not the optimality of pivot choice; this convention keeps the algorithm clean.

This convention raises a distribution issue. In a distributed implementation, a pivot residing in one process may be needed by other processes that happen to share the same current segment. The pivot must therefore be broadcast to all processes whose local data falls within the same segment - but only to those processes. The scope of each pivot's relevance is exactly its enclosing segment.

How to broadcast within segments. This is itself a segmented operation, and the algorithm solves it with the same primitive: a segmented parallel prefix sum with a special associative operator. Define the operator $$ a \oplus b = a $$ (the result is always the left operand). Within an SPPS, this operator has the effect of propagating the first element of each segment to every element of that segment, in $O(\log n)$ steps. The pivot diffuses from the left edge of the segment until it hits the next segment boundary, at which point it stops; then the new pivot inside the next segment takes over.

This is a striking instance of the SPPS framework's flexibility: with no special communication primitive, the local broadcast of pivots within segments is expressed as a standard SPPS call where the binary operation happens to be a degenerate "keep-left" operator.

### Detecting termination

The algorithm terminates when the array is sorted - that is, when every adjacent pair $(a_i, a_{i+1})$ is in order. Each processor can locally compute the flag $$ f_i = (a_i \leq a_{i+1}) $$ for every adjacent pair it holds. The global termination condition is then $$
f = f_0 \land f_1 \land \cdots \land f_{n-2}
$$ which is a global AAR (all-to-all reduction) under operation AND. If $f = 1$, every adjacent pair is in order and the algorithm exits; if $f = 0$, at least one adjacent pair is out of order and a new iteration is required.

The lecturer made this concrete: the termination test is a clear demonstration of parallel reduction. Each processor computes its local AND of adjacency flags, and an AAR fuses these into a single global flag. In the degenerate case of already-sorted input, the first reduction returns $1$ immediately and the algorithm does no work at all.

### The three packings per iteration

Once the pivot is broadcast within each segment, every $a_i$ in the segment has its flag $g_i \in {<, =, >}$ computed locally. The split into three sub-segments is then performed by _three_ PPSs and one data permutation:

1. SPPS to compute new position indices of all elements with $g_i = \text{`<'}$ within the segment. This packs all $<$ elements into the front of the new segment.
2. Broadcast the maximum index of the $<$ block leftwards within the segment using SPPS. This value, $|S_<|$, is the _offset_ at which the $=$ block must start.
3. SPPS to compute new position indices of all elements with $g_i = \text{`='}$, packed starting after the $<$ block.
4. Broadcast the maximum index of the $=$ block leftwards within the segment. This value, $|S_<| + |S_=|$, is the offset at which the $>$ block must start.
5. SPPS to compute new position indices of all elements with $g_i = \text{`>'}$, packed starting after the $=$ block.
6. Permute the data to create three sub-segments $S = {S_< ,|, S_= ,|, S_>}$.

Every offset broadcast is itself a left-going SPPS with the "keep-left" operator described above. So one iteration consists of $O(1)$ SPPS calls plus one data permutation (which is the only step that actually moves data between processes in the distributed implementation).

### Algorithm in pseudocode

```
B:
  // termination test
  for all i := 0..n-2 do_in_parallel
    f_i := (a_i <= a_{i+1})
  perform AAR of f_i with operation AND, yielding f
  if f = 1 then EXIT

  // one iteration of the parallel QuickSort
  for all current segments S in A do_in_parallel
    select first element b in S as pivot
    broadcast b within S using SPPS with operation (a (+) b = a)
    for all a_i in S do_in_parallel
      g_i := (a_i compared with b) in { '<', '=', '>' }

    // pack S<
    apply SPPS to compute new indices of elements with g_i = '<' within S
    broadcast |S<| within S leftwards via SPPS    // offset of S=

    // pack S=
    apply SPPS to compute new indices of elements with g_i = '=' within S
    broadcast |S<| + |S=| within S leftwards via SPPS  // offset of S>

    // pack S>
    apply SPPS to compute new indices of elements with g_i = '>' within S

    permute S into { S< | S= | S> }

  goto B
```

Example layout (slide 37). Five processors $P_0, \ldots, P_4$ holding an array that has already been split into four segments $S_0, S_1, S_2, S_3$ with pivots $b_0, b_1, b_2, b_3$ as the first elements of each segment. The first iteration on this configuration splits each segment further:

- $S_0$ becomes $S_{0<} | S_{0=} | S_{0>}$ (since $S_0$ spans $P_0$ and part of $P_1$).
- $S_1$ becomes $S_{1<} | S_{1=} | S_{1>}$ within the part of $P_1$ that holds it.
- $S_2$ already consists of only equal elements (assumed in the example) so it does not split.
- $S_3$ becomes $S_{3<} | S_{3=} | S_{3>}$ spanning $P_3$ and $P_4$.

Every processor participates in every SPPS regardless of which segments touch it, because the SPPS framework is segmentation-agnostic.

### Load balance, scalability, and memory model independence

Three properties make this QuickSort variant exceptional:

Load balance. In every iteration, every processor scans through its $n/p$ local elements during each of the three packings. The work per processor is essentially the same regardless of the pivot choice and the segment-size distribution, because SPPS itself is load-balanced and the segmentation only affects the binary operation, not the work distribution. Contrast with in-place parallel QuickSort, where pivot quality directly determines task imbalance.

Scalability. Each iteration costs $O(1)$ SPPS calls. If the input is random, the algorithm terminates in $O(\log n)$ iterations on average. Hence: $$ T_{\text{QuickSort}}(n, p) = O(\log n \cdot T_{\text{PPS}}(n, p)) $$ With scaled PPS giving $T_{\text{PPS}}(n, p) = O(n/p + \log p)$, this yields $$ T_{\text{QuickSort}}(n, p) = O!\left(\log n \cdot \left(\frac{n}{p} + \log p\right)\right). $$ Memory model independence. The same algorithm runs unchanged on shared memory (OpenMP, threads) and on distributed memory (MPI, processes). The only step that involves actual data movement between memories is the final permutation; in a distributed implementation this is the communication phase, while in a shared-memory implementation it is just a local write. Everything else - the SPPS calls, the AAR for termination, the segmentation - has identical structure in both models.

The lecturer emphasized this as the algorithm's most distinctive property: a single, uniform formulation that captures both shared-memory and distributed parallelism with no algorithmic changes.

### Recap: why SPPS is the right primitive

SPPS solves three otherwise awkward problems in distributed parallel QuickSort simultaneously:

1. _Pivot broadcast within a segment_ is an SPPS with the keep-left operator.
2. _Per-segment offset computation for packing_ is an SPPS with operator $+$ over $0/1$ flags.
3. _Offset broadcast leftwards within a segment_ is another SPPS with the keep-left operator (in the leftward direction).

Every one of these is segmentation-aware but parallel-uniform, because the segmentation is absorbed by the operation $\overline{\oplus}$. The lecturer's framing: even though the segmentation is data-sensitive, the SPPS framework ignores it entirely from the perspective of the processor array - all processors run uniform code, all the time.

### Potential exam questions

1. Distinguish the out-of-place parallel QuickSort from the in-place parallel QuickSort discussed in earlier lectures. What is gained, what is lost, and why is the out-of-place variant suited to SPPS?
2. Define one _iteration_ of this algorithm on a single segment $S$. What are the three sub-segments produced, and what stability property does each preserve?
3. Why is the first element of each segment used as the pivot? What is the consequence of this choice for the distribution of pivot broadcasts in the distributed setting?
4. Pivot broadcast within a segment is itself an SPPS. State the binary operation used, and explain why an SPPS with this operation has exactly the effect of "spread pivot to every position inside the segment".
5. Describe the termination test. What primitive is used to combine the per-processor adjacency flags into a global decision? What is the cost of the test?
6. List the three packings performed in one iteration and describe what each one accomplishes. How many SPPS calls in total are required per iteration?
7. Why is the maximum index of $S_<$ broadcast leftwards after the first packing? What does this value represent, and how is the broadcast performed?
8. Argue why this algorithm is automatically load-balanced even though the segmentation is data-dependent. Contrast with the in-place parallel QuickSort.
9. Derive the parallel time complexity $T_{\text{QuickSort}}(n, p) = O(\log n \cdot T_{\text{PPS}}(n, p))$. Under what assumption about the input does the $O(\log n)$ factor hold?
10. Explain why this algorithm can be implemented unchanged on both shared-memory and distributed-memory systems. Which step is the only one that materially differs between the two models?
11. Trace one iteration of the algorithm on the 4-segment example from slide 37. Identify the pivots, the per-segment splits, and the participating processors.
12. Sketch how the entire algorithm reduces to a small number of distinct SPPS primitives. What three roles does SPPS play, and what binary operations are used in each?