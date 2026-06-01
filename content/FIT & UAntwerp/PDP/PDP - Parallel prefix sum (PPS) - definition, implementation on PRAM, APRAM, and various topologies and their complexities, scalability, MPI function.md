
> [!tldr] First 5 minutes of hell
> The parallel prefix sum is an operation, which cumulatively sums the input array $X$ using any associative and commutative binary operation. The output $Y$ then contains all the prefixes of $X$.
> 
> The sequential approach is simple, just iterate through the array, keep the local sum and in each loop, write the actual sum to the current $i$-position. Since this is data dependent (the $i$-th operation depends on the $i-1$th operation), it is not easily parallelizable.
> 
> Parallel approach (the EREW part):
> - there are three sections:
> 	- initialization (1 thread initializes "it's own" $i$-th value in the shared $M[]$ array)
> 	- in the loop (looping through $j$ variable):
> 		- first reads only: each thread looks on the $2^j$-th field back (exclusive read)
> 			- all threads look back 1 element, then 2 elements, then 4 elements etc.
> 			- only threads with $i$ (their rank) bigger than $2^j$ are active (so no looking out of bounds of the array)
> 			- and adds the value from that element with it's own element in their local register $y_i$ (no writes)
> 		- second writes only: just write the register value into the $M[i]$ value (exclusive writes)
> - the magic is that each element to which the thread looks at, already contains the sum of the chunk that is $2^(j)$ big
> 	- after $j=0$, the chunk contains $2^0$ = 1 elements
> 	- after $j=1$, the chunk contains $2^1$ = 2 consecutive elements 
> 	- etc.
> - it all takes logarithmic time $T(n, n) = \Theta(\log n)$, same as the parallel reduction (despite producing $n$ outputs instead of one)
> - if there are only $p$ processors, the $T(n,p)=O(n/p)+O(log(p))$ 
> 
> Parallel approach (indirect tree):
> ![[Pasted image 20260524193514.png]]
> - there are $n$ leaves, that contain the input data at the start and then the output data in the end
> - the height of the tree is $h(T)$, the whole computation is solved in $2h(T)$ parallel steps ($O(log\ n)$ steps for a complete tree or butterfly)
> - sweep-up phase and sweep-down phase (copying the input from up to both children)
> - the values are only in the leaves, because it is a indirect tree (it has only routers/switches on the inner nodes, only leaves are the CPU+memory+router nodes)
> 
> Parallel approach (direct tree):
> - direct tree has $n$ nodes (all compuatation nodes), so the input values are in all nodes
> 	- every nodes contains the initial input value and then the output value at the end
> - the tree has to be linearized first (so we exactly know, how are the nodes ordered (to match the array order structure)) - POSTORDER traversal linearization is chosen
> 	- the tree is traversed in the POSTORDER, using the same algorithm as in the indirect tree with these modifications
> - ![[Pasted image 20260524194400.png]]
> 	- sweep-down operation is the same (just the value is added to the node value)
> 	- sweep-up node sends the sum up and then distributes the 3 = 3 (from the left) and 4 = 1 + 3 (from the left), so the final prefix order is retained
> - the array is also computed in $2h(T)$ operations
> 
> Parallel approach (any topology):
> - if the topology/graph is connected, we will just run the BFS spanning tree and run the direct tree approach on it
> - PPS of $n$ input values can be solved on an arbitrary $n$-node network with a constant degree in O(diam(G)) parallel steps (the diameter is the depth of the BFS spanning tree)
> 
> More elegant parallel solution for orthogonal topologies:
> - hypercube
> 	- the $n$ has to be $2^r$ (due to hypercube limited scalability)
> 	- each processor (each vertex) has two registers
> 		- $green_i$ accumulates everything it sees
> 		- $yellow_i$ accumulates only the prefix (values only from neighbors with a lower index)
> 	- in round $j$ each processor exchanges data with it's neighbor on the $j$-dimension (the index differs only by 1 bit in the $j$-th position)
> 		- both processors exchange green register values and add them up
> 		- the yellow value flows only from the node with a lower index to the node with the higher index
> 			- since we are going from $j=0$ (the right-most bit), the first element only sends the value up and doesn't get anything from other nodes (the correct way, since it is a prefix sum and this node is the first one)
> 	- ![[Pasted image 20260524200310.png]]
> 	- this is a normal hypercube algorithm
> - store-and-forward mesh/tori
> 	- it needs to be linearized (a mapping from the array to the nodes): different linearizations imply different PPS algorithms
> 	- simple example: map array onto a 2D mesh (in the lexicographically row-wise sense)
> 	- ![[Pasted image 20260524201019.png]]
> - wormhole meshes
> 	- distances are not respected, 1D-mesh can simulate any multidimensional scheme (e.g. the indirect tree PPS algorithm), completing in the $O(log(n))$ time
> 
> Scalability
> - it would not make sense to have one processor per array value, in reality there are less processors, so each processor has to take more array values
> - ![[Pasted image 20260524201824.png]]
> - on APRAM model:
> 	- $T(n, p) = \alpha \frac{n}{p} + \beta \log^2 p$
> 	- the same as on the PRAM model, but there is 
> 		- $log(p)$ operations every loop
> 		- each loop is synchronized using barrier $log(p)$ (binary tree reduction barrier implementation)
> 		- $n/p$ = $p$ threads take the $n$ elements which they do sequentially
> 
> MPI function
> - PPS is implemented in MPI, two versions:
> 	- `MPI_Scan` (standard, inexclusive) process $P_i$'s `recvbuf` contains the prefix over data in $P_0 \ldots P_i$.
> 	- `MPI_Exscan` (exclusive) process $P_i$'s `recvbuf` contains the prefix over data in $P_0 \ldots P_{i-1}$
> - look below for the signature and the properties:
> 	- there are also `MPI_Iscan` as for 'immediate/nonblocking'
> 

### MPI function: `MPI_Scan` (and `MPI_Exscan`)

PPS in MPI exists in two versions with the same signature:

- Standard `MPI_Scan` — inclusive: process $P_i$'s `recvbuf` contains the prefix over data in $P_0 \ldots P_i$.
- Exclusive `MPI_Exscan` — process $P_i$'s `recvbuf` contains the prefix over data in $P_0 \ldots P_{i-1}$.

Signature:

```c
MPI_Scan(const void* sendbuf, void* recvbuf, int count,
         MPI_Datatype datatype, MPI_Op op, MPI_Comm comm)
```

Properties:

- Input determined by `sendbuf`, `count`, `datatype`; output by `recvbuf`, `count`, `datatype`.
- The predefined binary operations are the same as in `MPI_Reduce` (`MPI_SUM`, `MPI_MAX`, `MPI_LXOR`, ...).
- `MPI_IN_PLACE` can be used in place of `sendbuf` exactly as in `MPI_Reduce`.
- User-defined binary operations are supported via `MPI_Op_create`.
- Apart from blocking scans, nonblocking scans exist (e.g. `MPI_Iscan`).
- SPPS (segmented PPS) is implementable using a user-defined reduction operation that acts on `(value, segment_number)` pairs.


### Definition (PPS / scan)

Given:

- An input array $X[0, \ldots, n-1]$ over a domain $D$.
- An associative and commutative binary operation $\oplus$ in $D$.

Output: an array $Y[0, \ldots, n-1]$ of all prefixes of $X$: $$ Y[i] = X[0] \oplus X[1] \oplus \cdots \oplus X[i] $$ Example: $X = [2,4,1,5,3,9,8,3,2]$ produces $Y = [2,6,7,12,15,24,32,35,37]$.

Exemplary application: the CountingSort algorithm (PPS turns a count array into the offsets at which each value writes itself in the output). More generally, PPS solves any problem that can be expressed via a recurrence relation, because any recursively defined sequence with an associative combiner can be unrolled by scan.

Note on operation requirements: the slides require $\oplus$ to be both associative and commutative for PPS. (For pure reduction, when the input array maps to processors in index order, associativity alone suffices; PPS is stated more strictly in Definition 2.)

### Sequential algorithm (and why it cannot be data-parallelized naively)

```
Algorithm PrefixSum(in: X[0..n-1]; out: Y[0..n-1]):
  i := 0; sum := X[i]; Y[i] := sum
  while i < n-1 do
    i := i + 1
    sum := sum (+) X[i]
    Y[i] := sum
```

This differs from sequential reduction only by the continuous write $Y[i] := sum$ on every iteration. The algorithm is inherently sequential: iteration $i$ depends on iteration $i-1$. If $\oplus$ were not associative, there would be no chance to parallelize it at all. Because of this, the parallel PPS algorithm has to abandon the sequential viewpoint completely and adopt a different perspective on the problem.

All the topology-specific algorithms below initially assume $p = n$ (one input number per processor); scaling to $p < n$ is treated separately in the Scalability section.

### PPS on EREW PRAM

$n$ threads compute PPS on the input shared array $M[0, \ldots, n-1]$ by in-place rewriting $M[i]$ with $Y[i]$. Each thread $\tau_i$ has a private variable $y_i$.

```
Alg. PRAM_PPS(in,out: M[0..n-1]):
  for all i := 0..n-1 do_in_par
    y_i := X[i]; M[i] := y_i
  for j := 0..ceil(log n)-1 do_seq
    for all i := 2^j..n-1 do_in_par
      y_i := M[i - 2^j] (+) M[i]
    for all i := 2^j..n-1 do_in_par
      M[i] := y_i
```

Invariant after step $j$: $M[k] = X[k] \oplus \cdots \oplus X[k - 2^{j+1} + 1]$.

In iteration $j$, the operation is applied to pairs whose distance grows exponentially: first adjacent pairs (distance $2^0$), then distance $2^1$, $2^2$, and so on. Tracing back the components each output is built from confirms that $M[i]$ at the end covers exactly the prefix $X[0..i]$.

> The remarkable property: parallel PPS achieves the _same_ $O(\log n)$ complexity as pure reduction, despite computing $n$ prefix sums instead of just one. This is possible because the partial reductions superimpose disjointly across positions; no number is touched twice in the same step.

Complexity on EREW PRAM: $T(n, n) = \Theta(\log n)$, identical to parallel reduction.

### PPS on APRAM

APRAM is derived from the EREW PRAM algorithm by inserting explicit barrier synchronizations between the strictly separated parallel rounds, because each round rewrites the shared array.

```
Alg. APRAM_PPS(in,out: M[0..n-1]):
  for all i := 0..n-1 do_in_par
    y_i := X[i]; M[i] := y_i
  Barrier synchronization
  for j := 0..ceil(log n)-1 do_seq
    for all i := 2^j..n-1 do_in_par
      y_i := y_i + M[i - 2^j]
    for all i := 2^j..n-1 do_in_par
      M[i] := y_i
    Barrier synchronization
```

There are $\log p$ parallel steps, each followed by a barrier synchronization for $p$ processes that takes $O(\log p)$ time (in the best implementations). Therefore: $$ T(n, p) = \alpha \frac{n}{p} + \beta \log^2 p $$ The extra factor of $\log p$ compared with the synchronous PRAM bound comes entirely from the barriers.

### PPS on indirect trees and bidirectional butterflies

Indirect tree: $n$ leaves contain the initial input data and at the end the output data; internal nodes only perform the computation.

Lemma (Lemma 4 in slides): PPS of $n$ input values can be solved on any indirect tree $T$ with $n$ leaves in $2 h(T)$ parallel steps, where $h(T)$ is the height of $T$.

The proof is by induction. The algorithm has two waves with three local rules:

- Sweep-up rule (a): an internal node receiving $a$ from its left child and $b$ from its right child sends $a + b$ upward and passes the left child's value $a$ down into the right subtree.
- Sweep-up rule (b): the three-arity / ternary case (when relevant), receiving $a, b, c$ and sending $a + b + c$ upward while sending $a + b$ rightward (with $a$ already passed to the right subtree).
- Sweep-down rule (c): a node that has received some value $a$ from above duplicates it into both children.

One sweep-up wave initiates up to $h(T)$ sweep-down waves.

Corollary 5: For $T$ = complete binary tree or butterfly, $h(T) = \log n$, so PPS completes in $O(\log n)$ steps.

### PPS on direct trees

Direct tree: every node of an $n$-node tree contains an initial input value and an output value at the end.

PPS on a direct tree requires linearization first. With POSTORDER indexing, the previous indirect-tree algorithm applies with a single modification at internal nodes: the internal node's own value participates in the sums (both when sending its accumulated value upward and when receiving from above). The reason this works is that in POSTORDER an internal node's own value lies "between" its left and right children in the linear order.

Result: PPS on the array stored POSTORDER in a direct tree $T$ takes $2 h(T)$ parallel steps. The proof is again by induction on the tree's recursive structure.

### PPS on an arbitrary topology

The direct-tree algorithm extends to any connected graph $G$ by constructing a breadth-first spanning tree and linearizing the graph via POSTORDER indexing of that spanning tree.

Corollary 7: PPS of $n$ input values can be solved on any $n$-node bounded-degree network in $O(\mathrm{diam}(G))$ parallel steps.

For orthogonal topologies, more elegant specialized solutions exist (see the next sections).

### PPS on hypercubes (normal hypercube algorithm)

Consider $n = 2^r$ inputs and PPS in lexicographic order on $Q_r$, $r \geq 1$. Every $P_i$ has two registers: $\mathit{green}_i$ and $\mathit{yellow}_i$.

```
Algorithm Hypercube_PPS(X[0..2^r - 1]):
  for all P_i, i := 0..2^r - 1, do_in_parallel
    green_i := yellow_i := X[i]               // initialization
    for j := 0..r-1 do_sequentially
      send green_i to P_{i XOR 2^j}           // Bitwise XOR
      receive newgreen from P_{i XOR 2^j}
      green_i := green_i + newgreen
      if (i XOR 2^j < i) then
        yellow_i := yellow_i + newgreen
```

Algorithm proceeds across the hypercube dimensions from $0$ to $r-1$. In each step, adjacent processors exchange data; $\mathit{green}_i$ accumulates _everything_ arriving from the neighbor; $\mathit{yellow}_i$ is updated only when the incoming value comes from a processor with smaller index, tested by $i \oplus 2^j < i$.

At the end:

- $\mathit{yellow}_i = Y[i]$ — each $P_i$ has accumulated into its yellow register exactly the elements belonging to its lexicographic prefix.
- $\mathit{green}_i = Y[2^r - 1]$ — all green registers hold the global sum, so the algorithm simultaneously performs AAR (All-to-All Reduction) as a by-product.

This is an excellent example of a _normal hypercube algorithm_ and is therefore optimal on all hypercubic networks.

### PPS on SF meshes / tori

Multidimensional meshes again require linearization first. The simplest case is the row-wise lexicographic mapping of the input array to a 2-D mesh. PPS proceeds in three phases:

1. Horizontal PPS in every row, executed independently and in parallel.
2. Vertical PPS in the last (rightmost) column only.
3. Horizontal OAB in every row except the first: each rightmost processor broadcasts the value it received in the vertical phase into its entire row, so the local row-prefix sums become global prefix sums.

For a square mesh of side $\sqrt{p}$, both the horizontal and vertical PPS phases run in time proportional to $\sqrt{p}$, matching the structure of a 1-D mesh PPS of $\sqrt{p}$ processors.

Different mesh linearizations imply completely different PPS algorithms (e.g., diagonal or Morton-curve linearizations).

### PPS on WH meshes in logarithmic time

On a wormhole (WH) mesh, distances need not be respected, so far-apart nodes can communicate efficiently. This allows a 1-D mesh to _simulate_ any multidimensional scheme, in particular the indirect-binary-tree PPS algorithm. The result is a logarithmic-time PPS on a WH 1-D mesh, requiring $O(\log n)$ parallel steps, with the data movements representing left-to-right transfers of left-subtree partial sums and the corresponding broadcasts down into right subtrees.

### Scalability of PPS (general case $p < n$)

Input array $X[0, \ldots, n-1]$ is split into $p$ subarrays $X_0, \ldots, X_{p-1}$ of $q = n/p$ elements each, one per processor $P_i$.

```
Algorithm Scaled_PPS(X[0..n-1]):
  for all i := 0..p-1 do_in_parallel                       // O(q) steps
    P_i computes sequential prefix sums S_i = [s_{i,0},...,s_{i,q-1}] of X_i
    Define z_i := s_{i,q-1}
  All P's perform a PPS on Z = [z_0,...,z_{p-1}]            // O(log p) steps
    producing [sigma_0,...,sigma_{p-1}]
  // sigma_i = total sum of all numbers in processors P_0..P_i
  for all i := 0..p-2 do_in_parallel  P_i sends sigma_i to P_{i+1}
  for all i := 1..p-1 do_in_parallel  P_i receives sigma_{i-1} from P_{i-1}
  for all i := 1..p-1 do_in_parallel                       // O(q) steps
    P_i adds sigma_{i-1} to every element of S_i
```

The structure of this scaled algorithm is identical to the row-wise SF 2-D mesh PPS, except that the rows of the mesh correspond to the local memories of each process instead of separate nodes: the per-row horizontal PPS becomes a local sequential PPS, the rightmost-column PPS becomes the global PPS on $Z$, and the per-row broadcast becomes a local addition of $\sigma_{i-1}$ to all elements of $S_i$.

Asymptotic time complexity on PRAM, hypercubes, WH meshes/tori, and networks with logarithmic diameter: $$ T(n, p) = O!\left(\frac{n}{p}\right) + O(\log p) $$ Or, with constants: $$ T(n, p) = \alpha \frac{n}{p} + \beta \log p $$ Key observations:

- PPS has exactly the same scalability as parallel reduction.
- The scaled PPS is a normal hypercube algorithm, hence optimal on all hypercubic topologies.

### MPI function: `MPI_Scan` (and `MPI_Exscan`)

PPS in MPI exists in two versions with the same signature:

- Standard `MPI_Scan` — inclusive: process $P_i$'s `recvbuf` contains the prefix over data in $P_0 \ldots P_i$.
- Exclusive `MPI_Exscan` — process $P_i$'s `recvbuf` contains the prefix over data in $P_0 \ldots P_{i-1}$.

Signature:

```c
MPI_Scan(const void* sendbuf, void* recvbuf, int count,
         MPI_Datatype datatype, MPI_Op op, MPI_Comm comm)
```

Properties:

- Input determined by `sendbuf`, `count`, `datatype`; output by `recvbuf`, `count`, `datatype`.
- The predefined binary operations are the same as in `MPI_Reduce` (`MPI_SUM`, `MPI_MAX`, `MPI_LXOR`, ...).
- `MPI_IN_PLACE` can be used in place of `sendbuf` exactly as in `MPI_Reduce`.
- User-defined binary operations are supported via `MPI_Op_create`.
- Apart from blocking scans, nonblocking scans exist (e.g. `MPI_Iscan`).
- SPPS (segmented PPS) is implementable using a user-defined reduction operation that acts on `(value, segment_number)` pairs.

### Summary table of complexities

|Model / topology|Time complexity of PPS|
|---|---|
|EREW PRAM ($p = n$)|$\Theta(\log n)$|
|APRAM ($p = n$)|$\alpha n/p + \beta \log^2 p$|
|Indirect tree / bidirectional butterfly|$2 h(T)$ steps; $O(\log n)$ for CBT/butterfly|
|Direct tree (POSTORDER)|$2 h(T)$ steps|
|Arbitrary bounded-degree $n$-node $G$|$O(\mathrm{diam}(G))$|
|Hypercube $Q_r$ (lexicographic)|$O(\log n)$, normal hypercube algorithm|
|SF 2-D mesh (row-wise)|three phases, $O(\sqrt{p})$ for square meshes|
|WH 1-D mesh (simulates indirect tree)|$O(\log n)$|
|Scaled, $p < n$ on PRAM / hypercube / WH mesh/torus|$\alpha n/p + \beta \log p$|

### Potential exam questions

1. Define PPS formally. What are the algebraic requirements on $\oplus$, and how do they differ from the requirements for parallel reduction?
2. Why can the sequential PPS algorithm not be parallelized by straightforward loop parallelism? What property of $\oplus$ is the precondition for _any_ parallel PPS algorithm to exist?
3. State and explain the EREW PRAM PPS algorithm. State and prove (or motivate) the invariant after step $j$. What is its parallel time complexity, and how does it compare with parallel reduction?
4. Describe the APRAM PPS algorithm. Where do barrier synchronizations have to be inserted, and why? Derive the time complexity $T(n, p) = \alpha n/p + \beta \log^2 p$.
5. State Lemma 4 on PPS in indirect trees. Sketch the sweep-up and sweep-down rules. Why is the height of $T$ multiplied by 2?
6. Explain how PPS on a direct tree is obtained from PPS on an indirect tree. What role does POSTORDER indexing play, and what is the single modification to the indirect-tree algorithm?
7. State Corollary 7 (PPS on an arbitrary topology) and explain the role of the breadth-first spanning tree and POSTORDER linearization.
8. Present the hypercube PPS algorithm in lexicographic order. Explain the purpose of the green and yellow registers, the role of the test $i \oplus 2^j < i$, and why the algorithm is a "normal hypercube algorithm". What are the final contents of the two registers in each $P_i$?
9. Describe the three-phase PPS on a SF 2-D mesh with row-wise lexicographic mapping. Why do different mesh linearizations lead to different algorithms?
10. How can a WH 1-D mesh perform PPS in logarithmic time? Which abstract algorithm is being simulated?
11. State the scaled PPS algorithm for $p < n$. Derive the time complexity $T(n, p) = \alpha n/p + \beta \log p$. Why does it have exactly the same asymptotic scalability as parallel reduction?
12. Why is the scaled PPS a normal hypercube algorithm and what does that imply about its optimality on hypercubic networks?
13. Give the signature of `MPI_Scan`. Explain the difference between `MPI_Scan` and `MPI_Exscan`. What is the role of `MPI_IN_PLACE`? How can user-defined operations be used (e.g. for SPPS)?
14. Compare the time complexities of PPS on the EREW PRAM and on the APRAM. Where does the extra logarithmic factor come from, and how could it be reduced in practice?
15. Argue that PPS has the same scalability functions $\psi_1(p)$ and $\psi_2(n)$ as parallel reduction. What does this tell you about the cost-efficiency of PPS?