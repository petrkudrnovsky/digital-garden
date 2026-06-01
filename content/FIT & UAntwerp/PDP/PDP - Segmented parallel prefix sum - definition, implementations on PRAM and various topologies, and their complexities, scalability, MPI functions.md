
> [!tldr] First 5 minutes of hell
> The input array is arbitrarily segmented into multiple segments and the goal is to calculate the PPS for each segment independently. 
> 
> Sequential solution is simple, just go one by one and write the running sum, each time you go into a new section, reset the running sum. 
> 
> Parallel approach:
> - we cannot reliably map segments to processors, as the counts and sizes are independent, so one processor could end up with a big segment and others with small ones
> - the solution is to bring up a new binary operation with the segmentaion-awareness baked into it (and then do the global PPS as normally)
> 	- see the operation below
> 	- the values are lefthand-protected, so if they begin a new segment, they are not influenced by the running sum from the left
> 	- we know that if the original operation is associative, the special derived operation is also associative
> 	- so this SPPS has all properties of the PPS (it is segment-agnostic, it just uses a different operation) and it can run on all networks (PRAM, APRAM, hypercube, SF mesh/tori, WH mesh, wBF, oBF...)
> - scalability is also the same as in the PPS 
> 
> Implementation in the MPI
> - via standard `MPI_Scan` and `MPI_Exscan` functions, but we have to register a new operation:
> 	- 1) commit a new MPI-aware datatype: a pair (value, segment_number)
> 		- so we transfer the "|" into segment numbers, so when those numbers differ, we know that the next number is from a different segment
> 	- 2) register the operation using `MPI_Op_create`
> 	- 3) call the `MPI_Scan` or some other equivalent with this operation


### Definition (segmented PPS)

The input array is _arbitrarily_ separated into disjoint segments the aim is to calculate all prefix sums _within these segments in isolation_. Crucially:

- The number of segments and the sizes of segments are arbitrary, part of the input.
- There is no relationship whatsoever between the segmentation and the number of processes or threads. These two structures are completely independent.

Example (slide 32). A 4-segment input array $$ |2,1,3,5|2,7,3|9,4,5,6|2,8,4,3,1| $$ under operation $+$ becomes, by applying 4 isolated PPSs: $$ |2,3,6,11|2,9,12|9,13,18,24|2,10,14,17,18| $$ Each segment is treated entirely independently of the others - the scan accumulator is conceptually _reset_ at every segment boundary.

Sequential case. Sequentially the algorithm is trivial: walk left to right in linear time and reset the accumulator whenever a segment boundary is crossed. The sequential SPPS is identical to non-segmented PPS except for this small reset rule.

Why parallelization is non-trivial. The naive approach of mapping segments to processors is hopeless: there could be too many or too few segments relative to the processor count, and the size distribution is data-dependent, so load balance is unpredictable - one thread might have nothing to do while another is overloaded. For efficient parallelization, the algorithm must abstract away from the segmentation entirely the parallel framework must be _segmentation-agnostic_. The only way to do that is to push the awareness of segmentation into the binary operation itself, so that the standard PPS parallel framework can be reused verbatim.

### The main idea: a modified binary operation $\overline{\oplus}$

Given an associative binary operation $\oplus$, define a new operation $\overline{\oplus}$ that operates on operands of two types: _plain_ operands $a$ and _protected_ (segment-starting) operands $|a$. The bar notation $|a$ marks the operand as the beginning of a new segment, hence as a value that must be shielded from any influence coming from outside the segment.

The operation $\overline{\oplus}$ is defined by the following table (rows = left operand, columns = right operand):

| $\overline{\oplus}$ | $b$                   | $\lvert b$ |
| ------------------- | --------------------- | ----------- |
| $a$                 | $a \oplus b$          | $\lvert b$ |
| $\lvert a$         | $\lvert(a \oplus b)$ | $\lvert b$ |

Reading the table:

- $a\overline{\oplus}b = a \oplus b$ — both plain: just do the usual operation.
- $a\overline{\oplus}|b = |b$ — the right operand starts a new segment, so the left operand is irrelevant the bar is preserved.
- $|a\overline{\oplus}b = |(a \oplus b)$ — the left operand started a segment combine with the right, but keep the bar so the result remains protected against any further left-coming influence.
- $|a\overline{\oplus}|b = |b$ — the right starts a new segment the left is ignored.

Two intuitive rules captured by the table:

1. _Protected on the right cannot be changed by anything to its left._ Any value with a bar absorbs whatever comes from the left without modification: a segment boundary on the right blocks all left-coming influence.
2. _Protection is preserved leftward._ Once a value has been protected (its left edge marked with a bar), all subsequent left-extensions inherit the bar the protection persists until explicitly broken by another segment start.

Associativity is preserved.

Lemma 9: If $\oplus$ is associative, then so is $\overline{\oplus}$.

Proof sketch (slide 33): just check the individual cases. As one example, $$ (a\overline{\oplus}|b)\overline{\oplus}c = |b\overline{\oplus}c = |(b \oplus c) = a\overline{\oplus}|(b \oplus c) = a\overline{\oplus}(|b\overline{\oplus}c). $$ The other cases are equally mechanical. Since associativity is the only algebraic property the standard parallel PPS algorithms require, this proves that SPPS reduces to a single standard PPS with $\overline{\oplus}$ as the operation.

### From SPPS to a single global PPS

$$ \text{SPPS} = 1 \text{ global PPS with the modified binary operation } \overline{\oplus}. $$ This is the central insight. The parallel array of processors or threads runs _the same PPS algorithm_ as in the non-segmented case - hypercube PPS, indirect-tree PPS, EREW PRAM PPS, scaled PPS, anything - and the only modification is that the binary operation knows about segment boundaries. The segmentation is handled _inside_ the operation, so the parallel structure is segmentation-agnostic and inherits all the efficiency, load balance, and scalability of the standard PPS framework.

The lecturer's framing: the operation itself takes care of the segmentation, so the array of processors can completely forget about it. Computation that would otherwise have to dispatch differently per segment instead runs a single, uniform parallel pattern, with all the segmentation logic absorbed into pairwise combinations.

### Implementation on indirect trees / butterflies

The non-segmented indirect-tree PPS algorithm (Lemma 4: $2 h(T)$ steps on any indirect tree with $n$ leaves) applies verbatim to the SPPS, with $\overline{\oplus}$ in place of $\oplus$. The complexity is unchanged because the operation itself does the bookkeeping. For complete binary trees and butterflies this gives $O(\log n)$ steps.

Worked example (slide 34). Input $$ |,3,5,1,|,4,5,|,8,2,| $$ (three segments). Running the indirect-tree PPS over 6 steps with $\overline{\oplus}$ produces the leaf values $$ 3,8,9,4,9,8,10 $$ (re-stated from the lecturer's expected output). Each leaf hosts the prefix sum within its own segment no information ever crosses a segment boundary, because $\overline{\oplus}$ blocks it.

### Implementations on PRAM and other topologies

Because SPPS reduces to a standard PPS with a different binary operation, all the PPS implementations of the parent question apply, with the same time complexities:

- EREW PRAM ($p = n$): the algorithm `PRAM_PPS` (with $\oplus$ replaced by $\overline{\oplus}$) runs in $\Theta(\log n)$ parallel steps. The invariant becomes: after step $j$, $M[k]$ holds the $\overline{\oplus}$-reduction of the suffix $X[k - 2^{j+1} + 1] \ldots X[k]$, automatically respecting segment boundaries.
- Indirect trees / bidirectional butterflies: $2 h(T)$ steps, $O(\log n)$ for CBTs and butterflies.
- Direct trees with POSTORDER linearization: $2 h(T)$ steps.
- Arbitrary $n$-node bounded-degree network: $O(\mathrm{diam}(G))$ steps.
- Hypercube $Q_r$ in lexicographic order: $O(\log n)$ steps, normal hypercube algorithm. The green and yellow registers carry ${\text{plain}, \text{protected}}$-typed values, and the dimension-by-dimension exchanges use $\overline{\oplus}$ instead of $\oplus$.
- SF 2-D mesh, WH meshes, scaled PPS for $p < n$: the same three-phase or scaled structure applies.

Scalability. Scaled SPPS on $p$ processors with $q = n/p$ elements per processor follows the same template as scaled PPS: $$ T(n, p) = O!\left(\frac{n}{p}\right) + O(\log p) = \alpha \frac{n}{p} + \beta \log p $$ on PRAM, hypercubes, WH meshes/tori, and networks with logarithmic diameter. It is a normal hypercube algorithm, hence optimal on hypercubic topologies. The only difference from non-segmented PPS is the use of $\overline{\oplus}$ inside the local sequential prefix sums, the global PPS over the per-processor totals, and the broadcast / addition phase - in every phase the operation is segmentation-aware.

The lecturer was explicit: SPPS has exactly the same scalability properties and the same set of optimal topologies as plain PPS.

### MPI implementation of SPPS via user-defined reduction

MPI does not provide a built-in segmented scan. However, SPPS is implementable using `MPI_Op_create` and the standard `MPI_Scan` / `MPI_Exscan` calls.

Slight semantic shift. The MPI implementation uses a richer encoding than the bar notation: instead of inserting bars between operands, each value is paired with a _segment number_. Two values from the same segment combine under $\oplus$ two values from different segments combine by simply keeping the right operand. The segmentation is captured by the segment IDs directly: $$ (u, i)\overline{\oplus}(v, j) = (w, j), \quad \text{where } w = \begin{cases} u \oplus v & \text{if } i = j, \ v & \text{otherwise.} \end{cases} $$ This is semantically slightly richer than the bar formulation, because every segment has a unique identifiable ID. The operation is associative (analogously to Lemma 9) and non-commutative.

Code structure (from slide 39):

```c
typedef struct { double val int sn } SegScanPair

void segScan(SegScanPair *in, SegScanPair *inout,
             int *len, MPI_Datatype *dptr) {
  SegScanPair c
  for (int i = 0 i < *len ++i) {
    if (in->sn == inout->sn) c.val = in->val + inout->val
    else                     c.val = inout->val
    c.sn = inout->sn
    *inout = c
    in++ inout++
  }
}
```

The integration into MPI follows the same recipe as the user-defined complex-number multiplication from Lecture 10 (slide 7). MPI must be told what the new data type looks like, and the new operator must be registered:

```c
MPI_Datatype type[2] = { MPI_DOUBLE, MPI_INT }
MPI_Aint disp[2]
int blocklen[2] = { 1, 1 }
MPI_Datatype sspair

MPI_Get_address(&a,    disp)
MPI_Get_address(&a.sn, disp + 1)
int base = disp[0]
for (int i = 0 i < 2 ++i) disp[i] -= base
MPI_Type_create_struct(2, blocklen, disp, type, &sspair)
MPI_Type_commit(&sspair)

MPI_Op myOp
MPI_Op_create(segScan, 0, &myOp)

MPI_Scan(&a, &answer, 1, sspair, myOp, comm)
```

Two registration steps:

1. _Commit the data type._ `MPI_Type_create_struct` plus `MPI_Type_commit` make the `(value, segment_number)` pair an MPI-aware datatype. This is required because MPI must know how to communicate and buffer the composite values.
2. _Create the operator._ `MPI_Op_create` registers the C function `segScan` as a user-defined MPI operator, which can then be passed to `MPI_Scan` like any built-in operator. The second argument (`0`) declares the operator as _non-commutative_ (the segmentation-aware operator is not commutative).

Once both are registered, `MPI_Scan` runs the standard MPI scan with the new datatype and operator, and produces a segmented scan as output. Nonblocking variants and `MPI_Exscan` are equally available.

The lecturer noted that this exactly mirrors the procedure used for the user-defined complex-multiplication reduction (lecture 10, slide 7): commit data type, create operator, call collective. The recipe is identical only the semantics of the operator differs.

### Summary of properties

|Property|Value|
|---|---|
|Reduces to|one standard PPS with operation $\overline{\oplus}$|
|Associativity requirement|$\oplus$ associative $\Rightarrow$ $\overline{\oplus}$ associative (Lemma 9)|
|Commutativity of $\overline{\oplus}$|non-commutative (left/right operands play different roles)|
|Time on EREW PRAM ($p = n$)|$\Theta(\log n)$|
|Time on indirect CBT / butterfly|$O(\log n)$|
|Time on hypercube ($p = n$)|$O(\log n)$, normal hypercube algorithm|
|Time on arbitrary bounded-degree $G$|$O(\mathrm{diam}(G))$|
|Scaled time, $p < n$|$\alpha n/p + \beta \log p$ on PRAM / hypercube / WH mesh-torus|
|MPI support|no built-in implementable via `MPI_Op_create` over `(value, segment_id)` pairs and `MPI_Scan` / `MPI_Exscan`|
|Optimality|normal hypercube algorithm $\Rightarrow$ optimal on hypercubic topologies|

The take-home message: SPPS inherits everything from plain PPS - implementations, complexities, scalability, optimality - by absorbing segmentation into the operation rather than into the algorithmic structure.

### Potential exam questions

1. Define the segmented PPS problem. What is the relationship between the segmentation of the input array and the number of processors?
2. Explain why the naive parallelization approach - assigning whole segments to processors - is unworkable. What property must a good SPPS implementation have, and why?
3. State the main idea behind SPPS: how is segmentation pushed into the binary operation, and what does it gain us at the algorithmic level?
4. Define the modified binary operation $\overline{\oplus}$ via its $2 \times 2$ table on ${a, |a}$ versus ${b, |b}$. Explain the semantics of each of the four cases.
5. State Lemma 9 and prove that $\overline{\oplus}$ is associative whenever $\oplus$ is, by checking representative cases.
6. Is $\overline{\oplus}$ commutative? Justify your answer using the table.
7. List four implementations of SPPS on different topologies and state their time complexities. Why are they exactly the same as for plain PPS?
8. Give the algorithm and time complexity of SPPS on the EREW PRAM. What invariant does $M[k]$ satisfy after step $j$?
9. Describe how SPPS runs on a hypercube. What do the green and yellow registers carry, and how does the dimension-by-dimension exchange use $\overline{\oplus}$?
10. Derive the scaled SPPS complexity $T(n, p) = \alpha n/p + \beta \log p$ on hypercubic networks. On which topologies is this scaled algorithm optimal?
11. Explain how SPPS is implemented in MPI. What are the two registration steps required before `MPI_Scan` can be called with a user-defined segmented operator?
12. Write out the encoding of $\overline{\oplus}$ used by the MPI implementation as a function of $(value, segment_number)$ pairs. Why is this slightly richer than the bar notation, and what does the unique segment ID give you?
13. Explain why `MPI_Op_create` is called with the commutativity flag set to $0$. What would go wrong if it were set to $1$?
14. Trace an SPPS by hand on the input $|,3, 5, 1,|,4, 5,|,8, 2,|$ using the indirect-tree algorithm. Verify that the result is $3, 8, 9, 4, 9, 8, 10$ and that no information crosses segment boundaries.
15. The lecturer claimed SPPS has "exactly the same scalability as PPS". Justify this claim by comparing the structure of the scaled algorithms in both cases.