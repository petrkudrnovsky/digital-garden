Topic: graph-theoretic properties used to characterize and compare interconnection networks of parallel computers - sparse vs dense topologies, hierarchical recursivity, distances, regularity, connectivity, bisection width, and bipartiteness.
### Preliminary assumptions
We consider only `simple, connected, loopless` graphs throughout. Every interconnection network of a parallel computer (INPC) is modeled as such a graph: vertices represent computing nodes (CPU + memory + router), edges represent communication links.
### Basic graph notation (Definition 1)
Let `G` be such a graph.
- Vertex set: `V(G)`, edge set: `E(G)`
- Size: `N = |V(G)|`
- Adjacency: `u, v` are adjacent if `<u,v> ∈ E(G)`
- Degree of vertex `u`: `deg_G(u) = ` number of neighbors of `u`
- Degree set: `deg(G) = {deg_G(u); u ∈ V(G)}`
- Maximum degree: `Δ(G) = max deg(G)`
- Minimum degree: `δ(G) = min deg(G)`
- `k`-regular graph: `Δ(G) = δ(G) = k` (every vertex has the same degree `k`)
### Topology, hierarchical recursivity and scalability (Definition 2)
A `topology G_n` is a parameterized infinite family of graphs (topology instances) whose size and structure is defined by a `dimension parameter n` (possibly multidimensional).

`Hierarchically recursive topology`: instances of smaller dimension are subgraphs of instances of larger dimension. This is a desirable property because:
- Job schedulers on large machines assign sub-networks to jobs - if the topology is hierarchically recursive, the assigned sub-network is itself an instance of the same family
- Enables `inductive design and mapping` of parallel algorithms

`Incrementally scalable topology`: defined for any `N`.
`Partially scalable topology`: defined for some, but infinitely many, `N` (e.g., only powers of 2).

> Hierarchical recursivity ⇒ partial scalability (most realistic topologies are only partially scalable).
### Sparse vs dense topology (Definition 3)
- `Sparse topology`: `|E(G_n)| = O(|V(G_n)|)` - vertex degrees upper-bounded by a constant
- `Dense topology`: `|E(G_n)| = ω(|V(G_n)|)` - vertex degrees grow with `n`

The distinction matters because only `sparse, constant-degree` topologies allow building arbitrarily large parallel machines from a single router component (with a fixed port count).

A fundamental lower bound (Theorem 15) shows that for any sparse `N`-vertex graph,
`diam(G) = Ω(log N)`.
Proof sketch: if `Δ(G) ≤ k = O(1)`, then a BFS spanning tree from any vertex `u` has depth at most `diam(G)` and the number of vertices at depth `i` is at most `k^i`. Hence `N = O(k^{diam(G)})`, so `diam(G) = Ω(log_k N)`.

> A sparse topology with logarithmic diameter is therefore `optimal` - it simultaneously achieves low cost (constant degree) and low communication latency.
### Cartesian product (Definition 4)
For graphs `G_1, G_2`, the Cartesian product `G = G_1 × G_2` is defined by:
- `V(G) = {[x,y]; x ∈ V(G_1), y ∈ V(G_2)}`
- `E(G) = {<[x_1,y],[x_2,y]>; <x_1,x_2> ∈ E(G_1)} ∪ {<[x,y_1],[x,y_2]>; <y_1,y_2> ∈ E(G_2)}`

Intuitively, `G_1` replaces every vertex of `G_2`, and corresponding vertices in these copies are linked according to `E(G_2)`.

`Theorem 5`: the Cartesian product is `commutative` and `associative` up to isomorphism:
- `G_1 × G_2 ≡ G_2 × G_1`
- `(G_1 × G_2) × G_3 ≡ G_1 × (G_2 × G_3)`

Notation: `G × G = G^2`, `G × G × G = G^3`, etc. The Cartesian product is the canonical `constructor of orthogonal topologies` (hypercubes, meshes, tori).
### Vertex symmetry and regularity (Definition 6, Theorem 7)
A graph `G` is `vertex-symmetric` if for every pair of vertices `u_1, u_2 ∈ V(G)` there exists an automorphism `f` such that `f(u_1) = u_2`. Intuitively, the graph "looks the same" from any vertex.

Theorem 7:
1. If `G_1` and `G_2` are vertex-symmetric, then `G = G_1 × G_2` is also vertex-symmetric (Cartesian product preserves vertex symmetry).
2. `G` vertex-symmetric ⇒ `G` regular.

The constructive proof of (1) defines, for given pairs `(u_1,v_1) ∈ V(G_1)` and `(u_2,v_2) ∈ V(G_2)` with their automorphisms `f^1_{u_1,v_1}` and `f^2_{u_2,v_2}`,
```
f_{u_1,u_2,v_1,v_2}([x_1, x_2]) = [f^1_{u_1,v_1}(x_1), f^2_{u_2,v_2}(x_2)]
```
and verifies it is a bijection preserving adjacency.

Practical consequence: for vertex-symmetric INPCs, "it does not matter where the computation starts" - the placement of a master process or root of communication has no structural impact. Vertex symmetry is thus a desirable property for `easier algorithm design`.
### Distances in graphs (Definition 8)
- `Path length`: `len(P(u,v)) = ` number of edges in `P(u,v)`
- `Distance`: `dist_G(u,v) = ` length of a shortest path from `u` to `v` in `G`
- `Average distance`: 
```
dist(G) = (1 / (N(N-1))) * Σ_{u,v ; u≠v} dist_G(u,v)
```
- `Eccentricity` of `u`: `exc(u) = max_{v ∈ V(G)} dist_G(u,v)`
- `Diameter`: `diam(G) = max_{u,v} dist_G(u,v) = max_u exc(u)`
- `Radius`: `r(G) = min_u exc(u)`
- `Vertex-disjoint paths`: `V(P(u,v)) ∩ V(P(x,y)) = {u,v} ∩ {x,y}` (share no internal vertex)
- `Edge-disjoint paths`: `E(P(u,v)) ∩ E(P(x,y)) = ∅` (share no edge)

Why distances matter for INPCs:
- Diameter and average distance bound the worst-case and average `communication latency`
- Eccentricity of the source bounds broadcast time - "you cannot run any broadcast in fewer steps than the eccentricity of the source"
- Disjoint paths enable simultaneous, collision-free communication, which permits splitting large packets into chunks and sending them in parallel

`Lemma 9` (distances in vertex-symmetric graphs): for every vertex-symmetric `G`,
```
diam(G) = r(G)
```
because every vertex has the same eccentricity (any vertex can be mapped to any other by an automorphism, which preserves distances), so min eccentricity = max eccentricity.
### Graph connectivity (Definitions 10, 12, Lemma 11)
- `Vertex (edge) cut` of `G`: a subset of vertices (edges) whose removal disconnects `G`
- `Vertex connectivity` `κ(G)`: size of a minimum vertex cut
- `Edge connectivity` `λ(G)`: size of a minimum edge cut

Lemma 11:
```
κ(G) ≤ λ(G) ≤ δ(G)
```
- `k-connected` graph: `κ(G) = k`
- `k-edge-connected` graph: `λ(G) = k`
- `Optimal connectivity`: `κ(G) = λ(G) = δ(G)` (all three values coincide)

Example: a graph `G_1` whose removal of one specific vertex disconnects it has `κ(G_1) = 1`, while two edges must be removed to disconnect it: `λ(G_1) = δ(G_1) = 2`. Such a graph does not have optimal connectivity.

Why connectivity matters for INPCs:
- Answers the question "what happens if a node fails or a cable is cut?" - directly characterizes `fault tolerance`
- High connectivity guarantees `many parallel disjoint (redundant) short paths` between vertex pairs
- Enables splitting large packets into smaller chunks and sending them in parallel along disjoint paths
### Bisection width (Definition 13)
The `(edge) bisection width` `bw_e(G)` is the smallest number of edges whose removal splits `G` into two halves of (roughly) equal size.

Distinction from connectivity: bisection requires the two resulting pieces to be of approximately equal cardinality - it is not just about disconnecting the graph but about `halving` it.

Bisection width is `controversial` for INPC design - it has competing demands:
- Parallel binary divide-and-conquer algorithms `require a large bisection width` for high bandwidth between halves
- VLSI design `seeks a small bisection width` to minimize interchip/intermodule wiring

Upper bound: `bw_e(G) ≤ N/2`. Typical values are `N / log N` or `N^ε` for `0 < ε < 1`.
### Bipartiteness (Definition 14)
A graph `G` is `bipartite` if its vertices can be 2-colored such that the endpoints of every edge have different colors. It is `bipartite balanced` if both color classes have equal cardinality.

Examples of bipartite graphs include trees and even-length cycles; odd-length cycles are not bipartite.
### Summary: requirements on INPCs
The above properties translate into the following INPC design requirements (sometimes contradictory):

`Constant vertex degree` (technological): low-cost, universal routers ⇒ sparse topology ⇒ low connectivity, large distances.
`Small diameter and average distance` (algorithmic): low communication latency.
`Vertex symmetry and hierarchical recursivity`: easier algorithm design, partial scalability, inductive mapping.
`High connectivity`: many parallel disjoint short paths, ability to split large packets.
`Bisection width`: large for D&C bandwidth, small for VLSI cost - inherently controversial.
`Embeddability` and `support for efficient routing and collective communication`: round out the requirements.

The fundamental lower bound (Theorem 15) ties these together: a sparse topology with `O(log N)` diameter is the optimal trade-off between cost and latency.
### Potential exam questions
1. State the formal definition of a sparse vs dense topology and explain why constant vertex degree is a technological requirement for INPCs.
2. State and prove Theorem 15: the diameter of any `N`-vertex sparse graph is `Ω(log N)`. What does it mean for a sparse topology to have logarithmic diameter?
3. Define hierarchical recursivity. Why is it desirable for an INPC topology, and what is its relationship to scalability (incremental vs partial)?
4. Define the Cartesian product of two graphs. State and outline the proof of Theorem 5 (commutativity and associativity up to isomorphism). Why is the Cartesian product the natural constructor of orthogonal topologies?
5. Define vertex symmetry and state Theorem 7. Sketch the constructive proof that the Cartesian product preserves vertex symmetry. Why does vertex symmetry matter for parallel algorithm design?
6. Define eccentricity, diameter, radius, and average distance. Prove Lemma 9: in a vertex-symmetric graph, `diam(G) = r(G)`.
7. Define vertex-disjoint and edge-disjoint paths. Why are disjoint paths important for parallel communication, and how do they relate to connectivity?
8. Define vertex connectivity `κ(G)`, edge connectivity `λ(G)`, and minimum degree `δ(G)`. State Lemma 11. What does it mean for a graph to have optimal connectivity? Give an example of a graph that does not have optimal connectivity and explain why.
9. Define edge bisection width. Why is it considered a "controversial" requirement for INPC design? What are typical upper bounds for `bw_e(G)`?
10. Define bipartite and bipartite balanced graphs. Why is bipartiteness an interesting structural property of an INPC, and how is it typically established (e.g., via parity arguments)?
11. Enumerate the primary requirements on INPCs and explain the contradiction between constant vertex degree and small diameter. What is the resolution offered by Theorem 15?
12. Compare the role of vertex symmetry and hierarchical recursivity in algorithm design. Can a topology be vertex-symmetric without being hierarchically recursive (or vice versa)? Give examples drawn from the orthogonal topologies family.