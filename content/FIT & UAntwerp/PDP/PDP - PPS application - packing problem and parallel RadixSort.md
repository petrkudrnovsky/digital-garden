
> [!tldr] First 5 minutes of hell
> The packing problem (a notorious usage of the PPS algorithm)
> - why?
> 	- it solves an universal problem of computing dense addresses within a sparse, distributed subset
> 	- we have a lot of locations where we have some array values or processes and we want to relocate them to some denser, contiguous space while preserving their order, we use the PPS to calculate their offsets over their characteristic vector (a vector of zeros and ones indicating if the value/process is there or not)
> - so each process knows that he belongs to $Z$ or not ($f_i = 1 \;\;\text{iff}\;\; P_i \in Z$), but it has no idea, what rank it is (globally)
> - treat the flags as binary integers and perform the PPS with operation $+$ to the array $[f_0, f_1,\ldots ,f_{n-1}]$ 
> 	- now every process knows its rank (the $f_k$ denotes the rank of the last member at or before $P_i$)
> - the packing operation/routine now physically moves the "package" (the process/array value) from the $k-th$ processor to the output port $k$ (the index in the output array)
> 	- the result is a dense array of members, where each member knows it's rank (where to send packages) and how many processes are before it (in the linear order)
> - ![[Pasted image 20260524205227.png]]
> 	- the final output port addresses are $[0,1,2,3]$ for four processes (there is minus one, because the port addresses are zero-based)
> - it completes in $O(log(n))$ time (the PPS complexity)
> 
> Parallel/distributed RadixSort
> - it is a canonical sorting algorithm on hypercubic networks (it fully utilizes the PPS operation, which is effective on various networks)
> - ![[Pasted image 20260524205919.png]]
> 	- splits are going from the LSB to the MSB
> 	- there are two PPS running at the same time
> 		- one calculates the offset of all numbers with bit 0 in $i$-th position (preserving order)
> 			- packing upward
> 		- the second calculates the offset of all numbers with bit 1 in $i$-th position 
> 			- packing downward
> 	- positions are calculated and a packing routing actually moves the data to their new positions
> - the time complexity: $O(\log N) \cdot O(\log N) = O(\log^2 N)$ (each PPS has a complexity of $O(\log N)$ and there are $O(\log N)$ Split iterations)
> 	- $N=2^n$

### The packing problem

Setup. Consider a distributed system where each processor $P_i$ holds a single bit $f_i$ of a characteristic vector of some subset $Z$ of the processor set: $$
f_i = 1 \;\;\text{iff}\;\; P_i \in Z
$$ The bits are arbitrary; some processors belong to $Z$, others do not. The problem is that every processor knows only its own local membership flag and has no idea about its position within $Z$ (how many members precede it).

Goal: each processor $P_i \in Z$ should learn its rank within $Z$, i.e. its index among the members of $Z$ in the linear order of processor indices. This is called _ranking within the distributed subset_ $Z$.

Solution via PPS. Treat the boolean flags as binary integers and apply a PPS with operation $+$ to the array $[f_0, f_1, \ldots, f_{n-1}]$. After the PPS, each $P_i$ holds the value $$ \sum_{k = 0}^{i} f_k $$ which is exactly the number of members of $Z$ in the prefix $P_0, \ldots, P_i$. For each $P_i \in Z$ this number is its 1-based rank within $Z$; for non-members it is the rank of the last member at or before $P_i$.

Packing. Once each member knows its rank $k$, the _packing_ operation routes the packet from the $k$-th processor in $Z$ to output port $k$. The members of $Z$ thus end up densely packed into the leading output positions, in their original order, with non-members skipped. After this routing each member knows both how many members are in its prefix and where its data should be sent.

Topological example (slide 27). The slide shows packing on an indirect ordinary butterfly $oBF_3$:

- Initial flags (top to bottom): $0,1,0,1,1,0,1,0$.
- After PPS, the array of prefix sums is: $0,1,1,2,3,3,4,4$.
- Final output port addresses for members of $Z$ are the prefix-sum values minus one (zero-based), so the four members are routed to output ports $0, 1, 2, 3$.

This illustration uses an indirect butterfly because PPS on indirect trees and butterflies runs in $O(\log n)$ steps (Lemma 4), so the packing itself completes in logarithmic time.

### Why packing matters

Packing has many applications and is one of the canonical building blocks of distributed parallel algorithms. The lecturer described it as the _notorious example_ of PPS usage. The intuition is that packing solves the universal problem of _computing dense addresses within a sparse, distributed subset_: any time a subset of processes (or array entries) has to be relocated into a contiguous region while preserving their order, the offsets are computed by a PPS over their characteristic vector. Parallel RadixSort, treated next, is the most important sorting algorithm built entirely on packing.

### Parallel / distributed RadixSort

Input: an array $A[0, \ldots, 2^n - 1]$ of $N = 2^n$ numbers, each at most $n$ bits wide (binary case; the algorithm generalizes to arbitrary radix bases).

```
Algorithm RadixSort(A[0..2^n - 1]):
  for i := 0..n-1 do_sequentially
    Split(A, i)
```

Definition of $\mathrm{Split}(A, i)$: a permutation of $A$ such that

- all numbers whose bit $i$ is $0$ are packed upward, and
- all numbers whose bit $i$ is $1$ are packed downward, with stable ordering inside each of the two groups.

Implementation of one Split. Inside $\mathrm{Split}(A, i)$, the two sub-packings are completely independent and may be run simultaneously if enough ports are available:

- One PPS computes, for every position whose bit $i$ is $0$, its destination offset in the upper half of the output array.
- A second PPS does the same for positions whose bit $i$ is $1$, computing offsets in the lower half.
- Two packing routings then permute the data into the new positions.

Thus: $$ \mathrm{Split}(A, i) = 2 ;\text{PPSs} + 2 ;\text{packing routings} $$ Iteration order. The iteration index $i$ runs from $0$ (least significant bit) to $n - 1$ (most significant bit). The number of iterations equals the number of digits in the representation; it is assumed to be a fixed, known constant determined by the bit-width of the numbers.

The lecturer summarized the iteration as a _split_ operator that combines a PPS-based offset computation with a permutation of the numbers. After $n$ such splits, the input array is fully sorted.

Parallel time. For $N = 2^n$ numbers on the ordinary butterfly $oBF_n$: $$ T_{\text{RadixSort}}(N) = O(\log^2 N) $$ The reasoning: there are $n = \log N$ Split iterations, each of which executes $O(1)$ PPSs on a butterfly, and each PPS takes $O(\log N)$ steps - hence $O(\log N) \cdot O(\log N) = O(\log^2 N)$.

Example (slide 28). Starting from the eight 3-bit numbers $$ 010, 111, 011, 001, 100, 111, 010, 101 $$ the slide traces three Splits, one per bit position (least significant first). After $\mathrm{Split}(A,0)$, numbers ending in $0$ are at the top and those ending in $1$ at the bottom, with stability preserved. After $\mathrm{Split}(A,1)$ and $\mathrm{Split}(A,2)$, the array is sorted: $$ 001, 010, 010, 011, 100, 101, 111, 111 $$

### Why RadixSort fits PPS so well

The lecturer's framing: RadixSort is based _solely_ on packing. Every Split is just two PPSs followed by two permutations - no comparisons, no merges, no recursion. Because PPS has efficient implementations on every topology of interest (PRAM, hypercube, butterfly, mesh, WH networks), RadixSort inherits all of these implementations. This makes RadixSort one of the cleanest illustrations of how a single primitive (PPS) can power an entire sorting algorithm, and explains why it is the canonical sorting algorithm for hypercubic networks.

### Potential exam questions

1. Define the packing problem precisely. What does each processor hold initially, and what does each processor need to learn?
2. Show how a single PPS over the characteristic vector of a subset $Z$ solves the packing problem. What is the binary operation used, and what does $P_i$'s post-PPS value represent?
3. On the indirect ordinary butterfly $oBF_3$ with initial flag vector $[0,1,0,1,1,0,1,0]$, compute the array of PPS results and the final output port addresses of the members of $Z$.
4. Why is the indirect butterfly (or any indirect binary tree) a convenient topology for packing? What is the parallel time complexity?
5. State the RadixSort algorithm in pseudocode. What is the input size assumption, and what does one iteration of the main loop do?
6. Define $\mathrm{Split}(A, i)$. Why is it a permutation of $A$, and what stability property does it preserve?
7. Decompose $\mathrm{Split}(A, i)$ into its PPS and routing components. Why are two PPSs needed rather than one?
8. Derive the parallel time complexity of RadixSort on the ordinary butterfly $oBF_n$. Why does the bound come out as $O(\log^2 N)$?
9. In which order are the bit positions processed in RadixSort - least-significant first or most-significant first - and why does the chosen order ensure correctness when each Split is stable?
10. Explain why RadixSort is described as being based "solely on packing". What primitive does _not_ appear in the algorithm, and what consequence does that have for its implementability on parallel topologies?
11. Trace one full Split of the input $[010, 111, 011, 001, 100, 111, 010, 101]$ for bit position $i = 0$. Show the resulting array order.
12. How would RadixSort generalize from binary digits to an arbitrary radix base $r$? What would change in the per-iteration PPS structure?
13. Compare RadixSort's reliance on PPS with QuickSort's reliance on segmented PPS (covered in Lecture 10's QuickSort discussion). In what sense are both algorithms "scan-based sorts"?
14. Why is the packing problem regarded as the canonical exemplary application of PPS in distributed computing? Name two other algorithms that build directly on packing.