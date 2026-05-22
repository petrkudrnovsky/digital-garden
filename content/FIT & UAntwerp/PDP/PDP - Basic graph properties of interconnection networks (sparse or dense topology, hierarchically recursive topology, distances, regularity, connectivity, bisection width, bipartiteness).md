
> [!tldr] First 5 minutes of hell
> INPC = Interconnection Network of Parallel Computers. Each node in the graph represents a computing node (CPU + memory + router) and edges are communication links.
> 
> For representing the networks, we assume simple, connected graphs without loops.
> 
> Topology $G_n$ is a set of graphs, for which size and structure are defined by dimension size $n$. Those graphs are topology instances.
> - topology is like a family of graphs, it defines, how the graph should look like for $n=1,2,...$ 
> - the dimensions could be multidimensional, e.g. $(a\times b)$ or $(a\times b \times c)$ 
> 
> Hierarchically recursive topology
> - instances of smaller dimensions are subgraphs of instances of greater dimensions (the topology is consistent)
> - we want this property of the topology - if the job scheduler assigns a sub-graph to some job, the sub-network will be of the same type (with same properties)
> 
> Incrementally scalable topology - could be defined for any $n$
> 
> Partially scalable topology - could be defined only for some $n$ (but also infinite amount of them), for example: only for powers of 2
> 
> Sparse topology: $|E(G_n)| = O(|V(G_n)|)$ - vertex degrees upper-bounded by a constant
> - no matter how big the graph is, the number of neighbors of each vertex is bounded by a constant
> 	- e.g. each node has 4 neighbors (applies, when the graph has 100 nodes or 100 millions of nodes)
> 
> Dense topology: $|E(G_n)| = \omega(|V(G_n)|)$ - vertex degrees grow with $n$
> - the extreme case is the complete graph $K_n$
> - by adding more nodes to the graph, one must connect them to all existing nodes (infeasible for real-life networks)
> 
> Distances:
> - path length = number of edges in the path
> - distance between $u$ and $v$ = the length of the shortest path 
> - average distance (between all pairs)
> - eccentricity: $exc(u)$ = the distance from the furthest vertex from $u$
> 	- useful for measuring, how long does the broadcast take from $u$
> - diameter of $G$ = maximum of all distances in the graph ("what is the furthest distance between vertices")
> - radius of $G$ = minimum of all eccentricities
> 	- for every vertex-symmetric graph it holds: diam(G) = radius(G)
> - vertex-disjoint paths do not share any inner vertex
> - edge-disjoint paths do not share any edges
> 
> Regularity:
> - the graph is $k$-regular, if all vertices have the same degree of $k$
> 
> Graph connectivity:
> - vertex cut = subset of vertices, whose removal disconnects $G$
> - edge cut = subset of edges, whose removal disconnects $G$ 
> - vertex connectivity $\kappa(G)$ = the size of the minimum vertex cut (what is the minimum number of vertices, who removal disconnects $G$)
> - edge connectivity $\lambda(G)$ = the same with edges
> - $k$-connected graph is when $\kappa(G)$ and $\lambda(G)$ equals $k$
> - in general applies: $\kappa(G) \leq \lambda(G) \leq \delta(G)$ 
> 	- optimal connectivity: $\kappa(G)=\lambda(G)=\delta(G)$ 
> 	- $\delta(G)$ is the minimum degree (of all vertices)
> 
> Bisection width and bipartidness:
> - bisection width of $G$: $bw_e(G)$= the smallest number of edges removals of which splits $G$ into 2 halves
> - bipartite graph: vertices could be colored with 2 colors so that both ends of each edge will have different colors (the colors are alternating)
> 	- bipartite balanced graph: the graph with equal number of vertices of both colors
> 		- e.g. trees don't have to have same number of vertices of both colors


> [!NOTE] Requirements on INPC
> Primary requirements are actually contradictory:
> - constant vertex degree (having sparse topology)
> 	- allows for low-cost and universal routers
> - small diameter and small average distance
> 	- this decreases the communication latency
> - theorem: the diameter of an $N$-vertex sparse graph $G$ is $\Omega(logN)$ 
> 	- if the sparse topology has a logarithmic diameter, it is optimal
> 
> Vertex-symmetry and hierarchical recursivity
> - easier design of parallel algorithms, since it does not matter where the computation start (all starting points are equal)
> - allows for partial scalability + inductive design and mapping of parallel algorithms
> 
> High connectivity
> - existence of many parallel, disjoint (=redundant) short paths between pairs of vertices
> 
> Bisection width depends on the type of the used algorithm:
> - divide-and-conquer algorithms require large bisection width
> - VLSI design seeks small bisection width
>   
> Embeddability of and into other topologies
> - be able to efficiently simulate other topologies
> - be able to provide efficient mapping of a process graph into a given INPC
> 
> Support for efficient routing and collective communication
> - permutation routing
> - one-to-all communication operations (broadcast, gather, scatter)
> - all-to-all communication operations

### Preliminary assumptions

We consider only `simple, connected, loopless` graphs throughout. Every interconnection network of a parallel computer (INPC) is modeled as such a graph: vertices represent computing nodes (CPU + memory + router), edges represent communication links.

### Basic graph notation (Definition 1)

Let $G$ be such a graph.

- Vertex set: $V(G)$, edge set: $E(G)$
- Size: $N = |V(G)|$
- Adjacency: $u, v$ are adjacent if $\langle u, v \rangle \in E(G)$
- Degree of vertex $u$: $\deg_G(u) =$ number of neighbors of $u$
- Degree set: $\deg(G) = {\deg_G(u);\ u \in V(G)}$
- Maximum degree: $\Delta(G) = \max \deg(G)$
- Minimum degree: $\delta(G) = \min \deg(G)$
- $k$-regular graph: $\Delta(G) = \delta(G) = k$ (every vertex has the same degree $k$)

### Topology, hierarchical recursivity and scalability (Definition 2)

A `topology` $G_n$ is a parameterized infinite family of graphs (topology instances) whose size and structure is defined by a `dimension parameter` $n$ (possibly multidimensional).

`Hierarchically recursive topology`: instances of smaller dimension are subgraphs of instances of larger dimension. This is a desirable property because:

- Job schedulers on large machines assign sub-networks to jobs - if the topology is hierarchically recursive, the assigned sub-network is itself an instance of the same family
- Enables `inductive design and mapping` of parallel algorithms

`Incrementally scalable topology`: defined for any $N$. `Partially scalable topology`: defined for some, but infinitely many, $N$ (e.g., only powers of 2).

> Hierarchical recursivity $\Rightarrow$ partial scalability (most realistic topologies are only partially scalable).

### Sparse vs dense topology (Definition 3)

- `Sparse topology`: $|E(G_n)| = O(|V(G_n)|)$ - vertex degrees upper-bounded by a constant
- `Dense topology`: $|E(G_n)| = \omega(|V(G_n)|)$ - vertex degrees grow with $n$

The distinction matters because only `sparse, constant-degree` topologies allow building arbitrarily large parallel machines from a single router component (with a fixed port count).

A fundamental lower bound (Theorem 15) shows that for any sparse $N$-vertex graph, $$ \mathrm{diam}(G) = \Omega(\log N). $$ Proof sketch: if $\Delta(G) \leq k = O(1)$, then a BFS spanning tree from any vertex $u$ has depth at most $\mathrm{diam}(G)$ and the number of vertices at depth $i$ is at most $k^i$. Hence $N = O(k^{\mathrm{diam}(G)})$, so $\mathrm{diam}(G) = \Omega(\log_k N)$.

> A sparse topology with logarithmic diameter is therefore `optimal` - it simultaneously achieves low cost (constant degree) and low communication latency.

### Cartesian product (Definition 4)

For graphs $G_1, G_2$, the Cartesian product $G = G_1 \times G_2$ is defined by:

- $V(G) = {[x, y];\ x \in V(G_1), y \in V(G_2)}$
- $E(G) = {\langle [x_1, y], [x_2, y] \rangle;\ \langle x_1, x_2 \rangle \in E(G_1)} \cup {\langle [x, y_1], [x, y_2] \rangle;\ \langle y_1, y_2 \rangle \in E(G_2)}$

Intuitively, $G_1$ replaces every vertex of $G_2$, and corresponding vertices in these copies are linked according to $E(G_2)$.

`Theorem 5`: the Cartesian product is `commutative` and `associative` up to isomorphism:

- $G_1 \times G_2 \equiv G_2 \times G_1$
- $(G_1 \times G_2) \times G_3 \equiv G_1 \times (G_2 \times G_3)$

Notation: $G \times G = G^2$, $G \times G \times G = G^3$, etc. The Cartesian product is the canonical `constructor of orthogonal topologies` (hypercubes, meshes, tori).

### Vertex symmetry and regularity (Definition 6, Theorem 7)

A graph $G$ is `vertex-symmetric` if for every pair of vertices $u_1, u_2 \in V(G)$ there exists an automorphism $f$ such that $f(u_1) = u_2$. Intuitively, the graph "looks the same" from any vertex.

Theorem 7:

1. If $G_1$ and $G_2$ are vertex-symmetric, then $G = G_1 \times G_2$ is also vertex-symmetric (Cartesian product preserves vertex symmetry).
2. $G$ vertex-symmetric $\Rightarrow$ $G$ regular.

The constructive proof of (1) defines, for given pairs $(u_1, v_1) \in V(G_1)$ and $(u_2, v_2) \in V(G_2)$ with their automorphisms $f^1_{u_1, v_1}$ and $f^2_{u_2, v_2}$, $$ f_{u_1, u_2, v_1, v_2}([x_1, x_2]) = [f^1_{u_1, v_1}(x_1),\ f^2_{u_2, v_2}(x_2)] $$ and verifies it is a bijection preserving adjacency.

Practical consequence: for vertex-symmetric INPCs, "it does not matter where the computation starts" - the placement of a master process or root of communication has no structural impact. Vertex symmetry is thus a desirable property for `easier algorithm design`.

### Distances in graphs (Definition 8)

- `Path length`: $\mathrm{len}(P(u, v)) =$ number of edges in $P(u, v)$
- `Distance`: $\mathrm{dist}_G(u, v) =$ length of a shortest path from $u$ to $v$ in $G$
- `Average distance`: $$ \mathrm{dist}(G) = \frac{1}{N(N-1)} \sum_{\substack{u, v \ u \neq v}} \mathrm{dist}_G(u, v) $$
- `Eccentricity` of $u$: $\mathrm{exc}(u) = \max_{v \in V(G)} \mathrm{dist}_G(u, v)$
- `Diameter`: $\mathrm{diam}(G) = \max_{u, v} \mathrm{dist}_G(u, v) = \max_u \mathrm{exc}(u)$
- `Radius`: $r(G) = \min_u \mathrm{exc}(u)$
- `Vertex-disjoint paths`: $V(P(u, v)) \cap V(P(x, y)) = {u, v} \cap {x, y}$ (share no internal vertex)
- `Edge-disjoint paths`: $E(P(u, v)) \cap E(P(x, y)) = \emptyset$ (share no edge)

Why distances matter for INPCs:

- Diameter and average distance bound the worst-case and average `communication latency`
- Eccentricity of the source bounds broadcast time - "you cannot run any broadcast in fewer steps than the eccentricity of the source"
- Disjoint paths enable simultaneous, collision-free communication, which permits splitting large packets into chunks and sending them in parallel

`Lemma 9` (distances in vertex-symmetric graphs): for every vertex-symmetric $G$, $$ \mathrm{diam}(G) = r(G) $$ because every vertex has the same eccentricity (any vertex can be mapped to any other by an automorphism, which preserves distances), so min eccentricity = max eccentricity.

### Graph connectivity (Definitions 10, 12, Lemma 11)

- `Vertex (edge) cut` of $G$: a subset of vertices (edges) whose removal disconnects $G$
- `Vertex connectivity` $\kappa(G)$: size of a minimum vertex cut
- `Edge connectivity` $\lambda(G)$: size of a minimum edge cut

Lemma 11: $$ \kappa(G) \leq \lambda(G) \leq \delta(G) $$

- $k$-connected graph: $\kappa(G) = k$
- $k$-edge-connected graph: $\lambda(G) = k$
- `Optimal connectivity`: $\kappa(G) = \lambda(G) = \delta(G)$ (all three values coincide)

Example: a graph $G_1$ whose removal of one specific vertex disconnects it has $\kappa(G_1) = 1$, while two edges must be removed to disconnect it: $\lambda(G_1) = \delta(G_1) = 2$. Such a graph does not have optimal connectivity.

Why connectivity matters for INPCs:

- Answers the question "what happens if a node fails or a cable is cut?" - directly characterizes `fault tolerance`
- High connectivity guarantees `many parallel disjoint (redundant) short paths` between vertex pairs
- Enables splitting large packets into smaller chunks and sending them in parallel along disjoint paths

### Bisection width (Definition 13)

The `(edge) bisection width` $\mathrm{bw}_e(G)$ is the smallest number of edges whose removal splits $G$ into two halves of (roughly) equal size.

Distinction from connectivity: bisection requires the two resulting pieces to be of approximately equal cardinality - it is not just about disconnecting the graph but about `halving` it.

Bisection width is `controversial` for INPC design - it has competing demands:

- Parallel binary divide-and-conquer algorithms `require a large bisection width` for high bandwidth between halves
- VLSI design `seeks a small bisection width` to minimize interchip/intermodule wiring

Upper bound: $\mathrm{bw}_e(G) \leq N/2$. Typical values are $N / \log N$ or $N^\varepsilon$ for $0 < \varepsilon < 1$.

### Bipartiteness (Definition 14)

A graph $G$ is `bipartite` if its vertices can be 2-colored such that the endpoints of every edge have different colors. It is `bipartite balanced` if both color classes have equal cardinality.

Examples of bipartite graphs include trees and even-length cycles; odd-length cycles are not bipartite.

### Summary: requirements on INPCs

The above properties translate into the following INPC design requirements (sometimes contradictory):

`Constant vertex degree` (technological): low-cost, universal routers $\Rightarrow$ sparse topology $\Rightarrow$ low connectivity, large distances. `Small diameter and average distance` (algorithmic): low communication latency. `Vertex symmetry and hierarchical recursivity`: easier algorithm design, partial scalability, inductive mapping. `High connectivity`: many parallel disjoint short paths, ability to split large packets. `Bisection width`: large for D&C bandwidth, small for VLSI cost - inherently controversial. `Embeddability` and `support for efficient routing and collective communication`: round out the requirements.

The fundamental lower bound (Theorem 15) ties these together: a sparse topology with $O(\log N)$ diameter is the optimal trade-off between cost and latency.

### Potential exam questions

1. State the formal definition of a sparse vs dense topology and explain why constant vertex degree is a technological requirement for INPCs.
2. State and prove Theorem 15: the diameter of any $N$-vertex sparse graph is $\Omega(\log N)$. What does it mean for a sparse topology to have logarithmic diameter?
3. Define hierarchical recursivity. Why is it desirable for an INPC topology, and what is its relationship to scalability (incremental vs partial)?
4. Define the Cartesian product of two graphs. State and outline the proof of Theorem 5 (commutativity and associativity up to isomorphism). Why is the Cartesian product the natural constructor of orthogonal topologies?
5. Define vertex symmetry and state Theorem 7. Sketch the constructive proof that the Cartesian product preserves vertex symmetry. Why does vertex symmetry matter for parallel algorithm design?
6. Define eccentricity, diameter, radius, and average distance. Prove Lemma 9: in a vertex-symmetric graph, $\mathrm{diam}(G) = r(G)$.
7. Define vertex-disjoint and edge-disjoint paths. Why are disjoint paths important for parallel communication, and how do they relate to connectivity?
8. Define vertex connectivity $\kappa(G)$, edge connectivity $\lambda(G)$, and minimum degree $\delta(G)$. State Lemma 11. What does it mean for a graph to have optimal connectivity? Give an example of a graph that does not have optimal connectivity and explain why.
9. Define edge bisection width. Why is it considered a "controversial" requirement for INPC design? What are typical upper bounds for $\mathrm{bw}_e(G)$?
10. Define bipartite and bipartite balanced graphs. Why is bipartiteness an interesting structural property of an INPC, and how is it typically established (e.g., via parity arguments)?
11. Enumerate the primary requirements on INPCs and explain the contradiction between constant vertex degree and small diameter. What is the resolution offered by Theorem 15?
12. Compare the role of vertex symmetry and hierarchical recursivity in algorithm design. Can a topology be vertex-symmetric without being hierarchically recursive (or vice versa)? Give examples drawn from the orthogonal topologies family.