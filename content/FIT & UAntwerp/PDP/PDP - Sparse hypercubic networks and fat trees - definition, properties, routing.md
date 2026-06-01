
> [!tldr] First 5 minutes in hell - simplified version
> Hypercube $Q_n$: great diameter, bad degree (grows with size) and bad scalability (only powers of 2 - cannot build a $Q_n$ with e.g. 1500 nodes)
> 
> Sparse hypercubic topologies are the constant-degree derivatives of the hypercube with logarithmic diameters (the maximum possible distance between nodes)
> - so they retain the algorithmic advantages of the hypercube and also allow the realistic builds with single fixed-degree routers (extending the network does not require exchange of all routers for routers with more connections)
> - the cost of doing so:
> 	- scalability is worse (we cannot choose the exact amount on nodes we want)
> 		- $N=2^n$ for hypercube, $N=n\cdot 2^n$ for wrapped butterfly yield even more gaps between vertex counts
> 
> Wrapped butterfly: fix the degree by exploding each vertex into a cycle of $n$ nodes
> - so the $n$ edges connecting to the vertex in hypercube are now distributed into $n$ nodes in the cycle (one edge per node)
> 	- number of vertices: $n\cdot 2^n$ 
> 	- diameter is $n+ \lfloor n/2 \rfloor$
> 		- $n$ to flip all the bits to get to the distance node (but we end up somewhere on the exploded cycle, so we need to travel at most the half of the cycle)
> - sparse, vertex-symmetric, Hamiltonian
> 
> Ordinary butterfly: cut the cycles of the wrapped butterfly open
> - each vertex is not a cycle, but a straight line 
> 	- straight edges and cross (hypercube) edges
> - ![[Pasted image 20260522164026.png]]
> - lose Hamiltonicity and vertex symmetry
> - gain hierarchical recursivity and a column structure (which is perfect for normal hypercubic algorithms))
> 	- $oBF_n$ contains two $oBF_{n-1}$ as subgraphs
> 	- in those algorithms, only one dimension of hypercube edges is used at any step of the algorithm
> - it is a minimal permutation network (there is exactly one shortest path between two vertices)
> 	- it serves as a cheap replacement to crossbar switches
> 
> Direct vs. indirect butterfly
> - direct: every node is a full computing node (CPU+memory+switch)
> - indirect: the inner nodes are pure routing switches (used more in industry, because the most of the network's job is shuffling data, not computing)
> 	- every switch has 4 states (two inputs, two outputs):
> 		- identity = pass through
> 		- exchange (inversion) = swap
> 		- upper broadcast = upper input to both outputs
> 		- lower broadcast = lower input to both outputs
> - routing: using the bits of the destination address (looking from LSB to MSB)
> 	- on direct butterflies:
> 		- if the bit is 0: take the upper output
> 		- if the bit is 1: take the lower output
> 	- on indirect butterflies:
> 		- do a XOR of the source and destination addresses and then go from LSB to MSB 
> 		- if the bit is 0: perform inversion
> 		- if the bit is 1: perform identity
> 
> Bidirectional butterfly: add turnaround (data can hit some middlepoint and get back), get multiple parallel paths between nodes
> - it is an extension to the ordinary butterfly
> - the middle point is the root of the tree connecting the source and the destination (and at this point the data "can decide" on which side they will continue)
> 	- this is the most significant bit that differs for source and destination (the data from source go from LSB to MSB, which is the highest differing) and then in descends back to the destination
> - with this bidirectional butterfly, it behaves like a crossbar switch, a computer on the left side can send something to the computer also on the left side
> - there are multiple common roots, so the traffic could be split
> 	- improves parallelism (parallel routes to take)
> 		- the bigger distance between nodes u and v is, the more turnaround points, there are $2^{m(u,v)}$ of them (where $m(u,v)$ is the position of the highest most significant bit that differs)
> 	- increases fault tolerance (multiple ways to get to the destination)
> 
> Fat tree: same graph as the bidirectional butterfly, drawn as a tree with growing bandwidth towards the root
> - this is what actually gets built and sold (simple routing, commodity switches are enough, modular and extensible, bandwidth scaling)
> - the tree gets fatter and fatter towards the root
> 	
>![[Pasted image 20260526171829.png]]

### Motivation: why sparse hypercubic?

The hypercube $Q_n$ has two structural drawbacks: $n$-regularity (dense, degree grows with size) and power-of-2 scalability. `Sparse hypercubic topologies` are `constant-degree derivatives of the hypercube with logarithmic diameters` - they retain the algorithmic advantages of the hypercube while allowing construction from a single fixed-degree router component.

Common characteristics:

- `Small constant degree` (typically 4) and $O(\log N)$ diameter - optimal per Theorem 15
- `Scalability` worse than the hypercube: $N = n \cdot 2^n$ or similar
- `Bisection width` $\Omega(N / \log N)$ - smaller than $Q_n$'s $N/2$ but still substantial
- Native topology for parallel algorithms like `Fast Fourier Transform`, divide-and-conquer, matrix computations, parallel sorting, collective communication

Two main representatives are the two kinds of `butterflies`. (A related topology is `cube-connected cycles`, not covered in this course.)

### Wrapped butterfly $\mathrm{wBF}_n$ - definition

Vertices are pairs $(i, x)$ combining a cycle index $i$ and an $n$-bit address $x$: $$
\begin{aligned}
V(\mathrm{wBF}_n) &= \{\, (i, x) \mid 0 \leq i < n\ \land\ x \in B^n \,\} \\
E(\mathrm{wBF}_n) &= \{\, \langle (i, x),\ (i \oplus_n 1, x) \rangle \quad \text{(cycle edges)} \\
&\qquad \langle (i, x),\ (i \oplus_n 1, \mathrm{neg}_i(x)) \rangle \quad \text{(hypercubic edges)} \\
&\quad \mid (i, x) \in V(\mathrm{wBF}_n) \,\}
\end{aligned}
$$ Key parameters: $$
\begin{aligned}
|V(\mathrm{wBF}_n)| &= n \cdot 2^n \\
|E(\mathrm{wBF}_n)| &= n \cdot 2^{n+1} \\
\mathrm{diam}(\mathrm{wBF}_n) &= n + \lfloor n/2 \rfloor \\
\deg(\mathrm{wBF}_n) &= 4 \quad (\text{4-regular}) \\
\mathrm{bw}_e(\mathrm{wBF}_n) &= 2^n
\end{aligned}
$$ Construction intuition: take the hypercube $Q_n$ and replace every vertex with an $n$-node cycle; each original hypercubic edge is then `distributed` across the cycles - at cycle position $i$, the edge inverts bit $i$ of the address. Every node has degree 4: 2 cycle neighbors + 2 hypercubic neighbors. The degree is `independent of` $n$, making $\mathrm{wBF}_n$ a truly sparse topology.

### Properties of $\mathrm{wBF}_n$

Theorem 25: $\mathrm{wBF}_n$ is vertex-symmetric. The automorphism for mapping $(i, x)$ to $(j, y)$ must simultaneously rotate cycle indices by $d = j \ominus_n i$ and rotate the $n$-bit addresses by $d$ bits (because the cycle index $k$ is rigidly tied to hypercubic dimension $k$). Then a translation fixes the cycle address: $$ f_{i, x, j, y}((k, z)) = (k \oplus_n d,\ \mathrm{rot}_d(z) \oplus w), \quad \text{where } w = \mathrm{rot}_d(x) \oplus y. $$ $\mathrm{wBF}_n$ is `not` a Cartesian product of $K(n) \times Q_n$ - the binding between cycle index and dimension prevents that - so vertex symmetry does not follow directly from Theorem 7.

Theorem 26: $\mathrm{diam}(\mathrm{wBF}_n) = n + \lfloor n/2 \rfloor$. Proof: by vertex symmetry, compute the eccentricity of an arbitrary vertex. To reach the most distant vertex, traverse $n$ hypercubic edges to reach the most distant cycle, then in the worst case $\lfloor n/2 \rfloor$ more edges within the terminal cycle.

Further properties:

- `Sparse topology with` $O(\log N)$ `optimal diameter` (approximately $(3/2) n$, using $n = \log_2(N / n)$)
- For equal $N = n \cdot 2^n$, compare to the 2-D torus $K(\sqrt{N}, \sqrt{N})$: both 4-regular, but $\mathrm{diam}(\mathrm{wBF}_n) \approx (3/2) n$ whereas $\mathrm{diam}(K(\sqrt{N}, \sqrt{N})) \approx \sqrt{n} \cdot 2^{n/2}$ - exponentially larger
- `Not hierarchically recursive`
- `Balanced bipartite` iff $n$ is even
- `Hamiltonian`

> For the same number of nodes and the same local degree, the butterfly crushes the 2-D torus on diameter.

### Ordinary butterfly $\mathrm{oBF}_n$ - definition

The ordinary butterfly is obtained from $\mathrm{wBF}_n$ by cutting every cycle open - replacing each cycle of size $n$ with a linear path of size $n + 1$: $$
\begin{aligned}
V(\mathrm{oBF}_n) &= \{\, (i, x) \mid 0 \leq i \leq n\ \land\ x \in B^n \,\} \quad (\text{note: } i \leq n,\ \text{not } < n) \\
E(\mathrm{oBF}_n) &= \{\, \langle (i, x),\ (i+1, x) \rangle \quad \text{(straight edges)} \\
&\qquad \langle (i, x),\ (i+1, \mathrm{neg}_i(x)) \rangle \quad \text{(cross/hypercube edges)} \\
&\quad \mid i < n \,\}
\end{aligned}
$$ Key parameters: $$
\begin{aligned}
|V(\mathrm{oBF}_n)| &= (n + 1) \cdot 2^n \\
|E(\mathrm{oBF}_n)| &= n \cdot 2^{n+1} \\
\mathrm{diam}(\mathrm{oBF}_n) &= 2n \\
\deg(\mathrm{oBF}_n) &\in \{2, 4\} \quad (\text{endpoints deg 2, interior deg 4}) \\
\mathrm{bw}_e(\mathrm{oBF}_n) &= 2^n
\end{aligned}
$$ The graph is typically drawn `rotated 90°` to the standard vertical arrangement familiar from textbooks. Columns (`stages`) are indexed $0 \leq i \leq n$; rows $0 \leq x \leq 2^n - 1$ index $n$-bit addresses. Column 0 is the "input" side, column $n$ is the "output" side.

### Properties of $\mathrm{oBF}_n$

- Organized into `columns (stages)` $0 \leq i \leq n$ and `rows` $0 \leq x \leq 2^n - 1$
- Two kinds of edges: `straight` and `cross (hypercube)` edges
- `Not vertex-symmetric` and `not regular` (endpoint nodes have degree 2)
- `Not Hamiltonian`
- `Hierarchically recursive`: $\mathrm{oBF}_n$ contains two copies of $\mathrm{oBF}_{n-1}$ as subgraphs (unlike $\mathrm{wBF}_n$)
- `Trivially bipartite` (bipartition by column parity)
- `Unique shortest path` between any $(0, x)$ and any $(n, y)$, namely e-cube routing
- Serves as a `minimal permutation network` - cheap substitute for crossbar switches

> The butterfly is typically used as a cheap substitution of more expensive crossbar switches.

The butterfly corresponds to `frozen-order hypercubic dimensions`: at each column $i$, only dimension $i$ can be used.

### Normal hypercube algorithms (Definition 27)

The butterfly is the native topology for `normal hypercubic algorithms`, defined as:

1. Only `one dimension` of hypercube edges is used at any step of the algorithm
2. `Consecutive dimensions` are used in consecutive steps

Examples include: `Divide & Conquer`, matrix computations, parallel sorting, `Fast Fourier Transform (FFT)`, and collective communication algorithms. Any algorithm that traverses hypercube dimensions in a fixed order can be executed on an ordinary butterfly in $n$ parallel time steps (one per column).

### Direct vs indirect butterfly

A `direct butterfly` has a full computing node (CPU + memory + switch) at every vertex. An `indirect butterfly` $\mathrm{indBF}_n$ has computing nodes `only on the leftmost and rightmost columns`, with pure routing switches in between. The internal switches are $2 \times 2$ and operate in one of four states:

`identity`, `exchange/inversion`, `lower broadcast`, `upper broadcast`.

- `identity`: upper input $\to$ upper output, lower input $\to$ lower output
- `exchange/inversion`: upper input $\to$ lower output, lower input $\to$ upper output
- `lower broadcast`: one input $\to$ both outputs (lower input broadcast)
- `upper broadcast`: one input $\to$ both outputs (upper input broadcast)

When traversed backwards, these same four states also support `reduction` operations - important for collective primitives. An $\mathrm{indBF}_n$ has $n + 1$ columns of switches rather than $n + 1$ columns of nodes.

### Routing in butterflies

`Direct butterfly routing`: at each stage, a switch routes based on the `destination address bit` at that stage - $0 \to$ upper output, $1 \to$ lower output. Example: routing from $(0, 011)$ to $(3, 110)$ - at each column $i$, consult bit $i$ of the destination address (rightmost bit first, e-cube order) and take the corresponding output.

`Indirect butterfly routing`: at each stage, set the switch to `inversion` if the input and output address bits differ at that stage, and to `identity` otherwise. The bit-by-bit comparison is just the XOR of source and destination. Example: routing from $0101$ to $1110$ - $0101 \oplus 1110 = 1011$, so switch states at stages 0, 1, 2, 3 are `inversion, inversion, identity, inversion`.

The path length is at most $n$ (one hop per column) for a left-to-right traversal. There is `exactly one shortest path` between any input-column vertex $(0, x)$ and any output-column vertex $(n, y)$ - this is the uniqueness property that makes the butterfly a `minimal permutation network`.

### Bidirectional butterflies

`Bidirectional butterflies` extend the ordinary butterfly by allowing switches to transport data in three modes:

- left-to-right
- right-to-left
- left-to-left (turnaround)

Routing algorithm: for vertices $u$ and $v$, let $m(u, v)$ be the index of the `most significant bit` in which $u$ and $v$ differ. The path from $u$ to $v$ is built in two phases:

1. `Ascending` phase: climb to some root of the `least common subtree` containing both $u$ and $v$. There are $2^{m(u, v)}$ such roots
2. `Turnaround` and `descending` phase: from the chosen root, descend uniquely to the target leaf

The count $2^{m(u, v)}$ of possible turnaround points grows with the "distance" between $u$ and $v$, providing `routing redundancy` (load balancing and fault tolerance). Making switches bidirectional turns the butterfly into a crossbar-like device: any input on the left side can reach any output on the left side via an up-then-down path.

### Fat trees

The `bidirectional butterfly is topologically equivalent to a fat tree`: merge pairs of $2 \times 2$ switches at each stage into larger switches ($4 \times 4$, then $8 \times 8$, etc.), and the cross-edges collapse into simple parent-child links. The result is a balanced tree where `the number of links going to parents equals the sum of the number of links from the children` - hence the name "fat tree": the tree gets `fatter` (more parallel links) as you move up towards the root.

Formalized: a fat tree has variable `height`, `arity` (number of ports per router), and link-multiplicity scheme. The "fatness" at each level is chosen so the bandwidth to the root matches the aggregate bandwidth at the leaves.

Historic uses: `Cray X2`, `Earth Simulator`, `Tianhe-2`.

### Properties of fat trees

- `Trivial routing, deadlock-free by definition` (the up-then-down path is unique in structure, though the choice of turnaround root provides parallelism)
- `Fault-tolerant` due to routing redundancy (multiple roots of the least common subtree)
- Many variants varying in tree `height`, router `arity` (number of ports), and incremental scalability
- Can be used not only for data transfer but also for `hardware-supported synchronization` - some machines dedicate a fat-tree sub-network solely to synchronization primitives

The fat tree is `the most common topology for the InfiniBand technology`, a high-throughput, low-startup-latency network used in servers, data centers, and supercomputers to connect computing nodes to each other or to storage.

> InfiniBand used to be, a few years back, the dominant communication infrastructure for high-performance machines.

### Comparison: butterflies vs fat trees

Conceptually, the two are the same object viewed from different angles:

- `Bidirectional butterfly`: flat multi-stage network with $n + 1$ columns, explicit up/down traversal
- `Fat tree`: hierarchical tree with widening bandwidth toward the root

The equivalence means any algorithm or routing policy for one translates directly to the other. In practice, fat trees are the dominant commercial realization today (InfiniBand clusters, data centers), while butterflies appear more often in textbooks and algorithm literature.

### Summary

Sparse hypercubic topologies are the family of `constant-degree, logarithmic-diameter` graphs derived from the hypercube:

Wrapped butterfly $\mathrm{wBF}_n$:

- $n \cdot 2^n$ vertices, 4-regular
- diameter $n + \lfloor n/2 \rfloor$, bisection $2^n$
- vertex-symmetric (Theorem 25), Hamiltonian, not hierarchically recursive

Ordinary butterfly $\mathrm{oBF}_n$:

- $(n + 1) \cdot 2^n$ vertices, degree ${2, 4}$
- diameter $2n$, bisection $2^n$
- hierarchically recursive (two $\mathrm{oBF}_{n-1}$ copies), unique shortest path, trivially bipartite
- serves as minimal permutation network (cheap crossbar substitute)
- native topology for `normal hypercubic algorithms` (Definition 27)
- $2 \times 2$ switches with 4 states: identity, exchange, upper broadcast, lower broadcast

Routing:

- `direct`: destination bit $\to$ output port
- `indirect`: $\mathrm{XOR}(\text{source}, \text{destination})$ bit $\to$ switch state (inversion or identity)
- unique shortest path, length $\leq n$

Bidirectional butterflies add turnaround, with $2^{m(u, v)}$ choices for the turnaround root. They are `topologically equivalent to fat trees`.

Fat trees: trivially deadlock-free, fault-tolerant, the dominant InfiniBand topology, used in `Cray X2`, `Earth Simulator`, `Tianhe-2`.

### Potential exam questions

1. Why are sparse hypercubic topologies interesting? What are their common characteristics (degree, diameter, bisection width, scalability)?
2. Give the formal definition of the wrapped butterfly $\mathrm{wBF}_n$: vertex set, edge set (with both cycle edges and hypercubic edges), and parameters.
3. Sketch the construction of $\mathrm{wBF}_n$ starting from the hypercube $Q_n$. Why is every node of degree 4, independent of $n$?
4. State Theorem 25 and sketch the proof that $\mathrm{wBF}_n$ is vertex-symmetric. Why does the argument not follow directly from Theorem 7?
5. State and prove Theorem 26: $\mathrm{diam}(\mathrm{wBF}_n) = n + \lfloor n/2 \rfloor$.
6. For equal $N = n \cdot 2^n$, compare $\mathrm{wBF}_n$ and the 2-D torus $K(\sqrt{N}, \sqrt{N})$ in terms of diameter. What is the take-away?
7. Give the formal definition of the ordinary butterfly $\mathrm{oBF}_n$. How is it obtained from the wrapped butterfly?
8. Why is $\mathrm{oBF}_n$ not regular? What are the degrees of its endpoint and interior vertices?
9. Show that $\mathrm{oBF}_n$ is hierarchically recursive: it contains two copies of $\mathrm{oBF}_{n-1}$ as subgraphs.
10. Define `normal hypercube algorithms` (Definition 27). Give three examples and explain why the ordinary butterfly is the native topology for them.
11. Explain the distinction between direct and indirect butterflies. What are the four states of the $2 \times 2$ switches in an indirect butterfly, and why are they also useful for reductions when traversed backwards?
12. Describe routing in a direct butterfly. Given source $(0, 011)$ and destination $(3, 110)$, trace the path.
13. Describe routing in an indirect butterfly. Given input $0101$ and output $1110$, compute the XOR and list the switch states stage by stage.
14. Why is the ordinary butterfly a `minimal permutation network`? What makes it a cheap substitute for the crossbar switch?
15. Describe bidirectional butterflies. Define $m(u, v)$ and sketch the up-then-down routing algorithm. How many turnaround roots exist?
16. State the topological equivalence between bidirectional butterflies and fat trees. How does the transformation work (collapsing $2 \times 2$ switches into $4 \times 4$, $8 \times 8$, ...)?
17. What does "fat" mean in "fat tree"? State the bandwidth condition at each level of the tree.
18. List properties of the fat tree: routing, fault tolerance, variants, and the role of InfiniBand. Name at least three machines that used fat trees.
19. Compare $\mathrm{wBF}_n$, $\mathrm{oBF}_n$, bidirectional butterfly, and fat tree in a concise table - degree, diameter, regularity, hierarchical recursivity, and dominant commercial use.