
> [!tldr] First 5 minutes of hell
> A hypercube $Q_n$ is a graph, where each vertex is an $n$-bit string. Two vertices are connected by edge, if they differ by exactly one bit. 
> - $Q_1$ - vertices 0 and 1, connected by the edge
> - $Q_2$ - vertices 00, 01, 10, 11, forms a square
> - $Q_3$ - a cube etc.
> 
> Building $Q_n$ is by doing a Cartesian product of two $Q_{p}$ and $Q_{n-p}$. It has a recursive structure. $Q_n$ is composed of two $Q_{n-1}$, where matching vertices are connected. 
> - $Q_n=Q_1\times Q_{n-1}$ 
> - $Q_5=Q_2\times Q_3$ 
> 
> Hamming distance $\rho(u,v)$ counts how many bit positions differ between two vertices. The graph distance in $Q_n$ equals the Hamming distance. The distance from $u$ to $v$ is exactly the number of bit switches.
> - diameter is $n$: the furthest I can go is $n$ (= flipping all bits)
> - number of vertices at distance $i$ from any given vertex is equal to $\binom{n}{i}$ (just pick, which bits to flip)
> - average distance is $n/2$ (by the symmetry of the Pascal's triangle)
> 
> Properties:
> - $|V|=2^n$ as there are $2^n$ binary strings of lenght $n$
> - $|E|= n \cdot 2^{n-1}$ (each of $2^n$ vertices has $n$ edges + divide by 2, because each edge is counted twice)
> - $Q_n$ is $n$-regular, each vertex has a degree of $n$
> - it is a dense topology (since degree grows with $n$)
> 	- this is the price, which I have to pay for a optimal logartihmic diameter (each node needs more connections as the network grows)
> - it has optimal connectivity: $\kappa(Q_n) = \lambda(Q_n) = n$ 
> - it has a largest possible bisection width: $bw_e(Q_n) = N/2$ (ideal for Divide-and-Conquer algos)
> - for a given pair of vertices, there exists $n!$ automorphism
> 	- one automorphism per permutation of the vertex's binary string
> 	  
> Bipartiteness
> - the $Q_n$ is bipartite balanced (= same number of both colors)
> 	- vertices with even number of 1-bits => color A
> 	- vertices with odd number of 1-bits => color B
> 	- if we switch one bit, we move exactly one edge and we change parity
> 	- each color has exactly $2^{n-1}$ vertices (balanced)
> 
> Routing (how to get from $u$ to $v$):
> - there exist $k!$ shortest paths between two vertices in distance $k$ (each shortest path is a permuation of all bits in which $u$ and $v$ differ)
> - e-cube routing is used to find the shortest paths
> 	- walks the bits from LSB (= least significant bit) to MSB 
> 	- if the current vertex disagrees with the destination, flip that bit (by flipping the disagreeing bit, we actually move one edge closer to the destination)
> 	- important is that the sequence is deterministic and prevents deadlocks (messages cannot take arbitrary shortest path - it could lead into cyclic deadlock)
> 
> Symmetry of $Q_n$ 
> - $Q_n$ is vertex-symmetric with $2^n\times n!$ different automorphisms
> 	- if we fix one vertex, there are $n!$ automorphisms
> 
> Drawbacks:
> - it is dense topology, so in real-life, only smaller dimensions make sense, e.g. $Q_7$ and each node has to have a router with 7 connections
> - scalability is limited only to $2^n$ nodes
> 
> Today's importance
> - used in supercomputers only for smaller dimensions
> - thanks to it's density, it is able to simulate almost every other topology
> - it serves as a "PRAM" model for distributed computing, if serves as a test for feasibility of parallel solutions (if the problem does not have a good solution on the hypercube model, it may be hard to be parallelized at all)


# Binary hypercube $Q_n$

Topic: the binary hypercube $Q_n$, its formal definition and key graph-theoretic parameters, structural properties (recursivity, vertex symmetry, bisection, bipartiteness, hamiltonicity, vertex-disjoint paths), and standard `e-cube` shortest-path routing.

### Definition of the binary hypercube $Q_n$

The `n-dimensional binary hypercube` $Q_n$ is the graph with: $$ \begin{aligned} V(Q_n) &= \{0, 1\}^n = \{\, x_{n-1} x_{n-2} \ldots x_0 \;\;\; x_i \in \{0, 1\} \,\} \\ E(Q_n) &= \{\, \langle x, \mathrm{neg}_i(x) \rangle \;\;\; x \in V(Q_n),\ 0 \leq i \leq n-1 \,\} \end{aligned} $$ where $\mathrm{neg}_i(x)$ denotes the $n$-bit string obtained from $x$ by flipping bit $i$. So vertices are $n$-bit binary addresses, and two vertices are adjacent iff they differ in exactly one bit.

Key parameters: $$ \begin{aligned} |V(Q_n)| &= 2^n \\ |E(Q_n)| &= n \cdot 2^{n-1} \\ \mathrm{diam}(Q_n) &= n \\ \deg(Q_n) &= \{n\} \quad (n\text{-regular}) \\ \mathrm{bw}_e(Q_n) &= 2^{n-1} = N/2 \end{aligned} $$ The figure of $Q_4$ appears as two copies of $Q_3$ (addresses $0xxx$ and $1xxx$) joined by a perfect matching of "dimension-3" edges.

### Hamming distance

The `Hamming distance` $\varrho(u, v)$ is the number of bit positions in which the two $n$-bit addresses $u$ and $v$ differ. It coincides with the graph distance: $$ \mathrm{dist}_{Q_n}(u, v) = \varrho(u, v). $$ This identification underlies essentially all properties of $Q_n$.

### Properties I - recursivity, Boolean structure, connectivity, bisection, bipartiteness, hamiltonicity

`$Q_n$ is regular with logarithmic diameter $\Rightarrow$ dense topology.` Since $\deg = n$ grows with $n$, $Q_n$ is dense (not sparse). Nevertheless its $\mathrm{diam} = n = \log_2 N$ is the optimal logarithmic diameter predicted by Theorem 15.

`Hierarchically recursive` via Cartesian product: $$ Q_n \equiv Q_p \times Q_{n-p} \equiv Q_p \times Q_q \times Q_{n-p-q} = Q_1^n $$ for any $0 < p, q < n$. Subcubes are addressed by strings $s_{n-1} \ldots s_1 s_0$ with $s_i \in {0, 1, \_}$ (where $\_$ is the "don't care" symbol). These correspond exactly to `terms in Boolean algebra` (minterms/maxterms when all bits are fixed, more general terms with $*$).

`Optimal connectivity`: $$ \kappa(Q_n) = \lambda(Q_n) = \delta(Q_n) = n. $$ The minimum number of vertices (or edges) whose removal disconnects $Q_n$ equals its regular degree.

`Largest possible bisection width`: $$ \mathrm{bw}_e(Q_n) = N/2 = 2^{n-1}. $$ This is the upper bound for any graph on $N$ vertices, and makes $Q_n$ ideal for binary divide-and-conquer algorithms. Cutting $Q_n$ into two $Q_{n-1}$'s along any single dimension removes exactly $2^{n-1}$ edges, because each vertex on one side has exactly one image on the other side.

`Balanced bipartite graph`. `Parity` (the XOR of all $n$ bits, or equivalently the count of 1-bits mod 2) provides a valid 2-coloring: every edge flips exactly one bit and therefore flips the parity, so adjacent vertices have opposite colors. Both color classes have size $2^{n-1}$.

`Hamiltonian graph`. Any $n$-bit Gray code is a Hamiltonian circuit in $Q_n$ - e.g., the binary reflected Gray code. In fact $Q_n$ has many Hamiltonian cycles.

### Properties II - vertex symmetry (Theorem 16)

Theorem 16: $Q_n$ is vertex-symmetric, with $2^n \cdot n!$ different automorphisms.

Proof sketch. Vertex symmetry follows from Theorem 7(1), since $Q_1$ is trivially vertex-symmetric and $Q_n \equiv Q_1^n$. The full count comes from composing two independent families:

1. `Dimension permutations` $\kappa(\pi)$. For a permutation $\pi : {0, \ldots, n-1} \to {0, \ldots, n-1}$, $$ \kappa(\pi)(x_{n-1} \ldots x_0) = x_{\pi(n-1)} \ldots x_{\pi(0)}. $$ This preserves Hamming distance (just relabels bit positions), hence adjacency. There are $n!$ such permutations.
    
2. `Translations` $\tau_{u,v}$. For any $u, v \in V(Q_n)$, $$ \tau_{u,v}(x) = x \oplus (u \oplus v). $$ XOR with a fixed vector preserves Hamming differences (hence adjacency) and is trivially a bijection. There are $2^n$ such translations.
    

Composing these yields $2^n \cdot n!$ automorphisms in total.

> The hypercube has much more symmetry than is needed by the vertex-symmetry definition.

### Properties III - automorphisms between a given pair (Corollary 17)

Corollary 17: for any $u, v \in V(Q_n)$, there exist $n!$ automorphisms $f_{u, v}$ such that $f_{u, v}(u) = v$.

Constructive proof. Pick any permutation $\pi$ of the $n$ dimensions (out of $n!$ choices). Compose with a translation that sends $\pi(u)$ to $v$: $$ f(x) = \pi(x) \oplus (v \oplus \pi(u)). $$ Substituting $x = u$: $$ f(u) = \pi(u) \oplus v \oplus \pi(u) = v. $$ Since $\pi$ was arbitrary out of $n!$ choices, there are $n!$ such automorphisms.

### Properties IV - recursivity and `e-cube` routing

Hierarchical recursivity visually: $Q_6 \equiv Q_3 \times Q_3$ can be drawn as a $Q_3$ each of whose vertices is itself a $Q_3$.

The hypercube is a highly cyclic graph: smallest cycles are 4-cycles, then 6-cycles, 8-cycles, and so on - always `even-length` (because $Q_n$ is bipartite). This cyclicity provides routing redundancy.

The standard shortest-path routing in $Q_n$ is called `e-cube routing` (dimension-ordered routing):

- Bits in the $n$-bit addresses are tested always `from the right to the left` (from LSB to MSB)
- At each step, if the current bit differs between the current node and the destination, traverse the edge in that dimension; otherwise skip to the next bit

Why bit-ordered routing? A routing scheme must be `deadlock-free`. Because $Q_n$ is highly cyclic, routing must follow a fixed dimension order to break cycles; testing bits always in the same order (LSB to MSB, or equivalently high-to-low) prevents deadlock.

Pseudocode for `e-cube` routing from source $s$ to destination $d$:

```
current = s
for i = 0, 1, ..., n-1:
    if bit_i(current) ≠ bit_i(d):
        current = neg_i(current)    // traverse dimension-i edge
return current == d
```

The path length equals $\varrho(s, d) = \mathrm{dist}(s, d)$, i.e., `e-cube` is shortest-path. Total algorithm steps: at most $n$.

Optimal algorithms exist for all `collective communication operations` on $Q_n$ - broadcast, gather, scatter, all-to-all, reduction, prefix sum, etc. - all hitting the theoretical lower bounds.

### Properties V - distance distribution (Lemmas 18, 19)

Lemma 18: the number of vertices at distance $i$ from a given vertex in $Q_n$ is $\binom{n}{i}$. Consequently the average distance in $Q_n$ is approximately $\lceil n/2 \rceil$: $$ \mathrm{dist}(Q_n) \approx n/2. $$ Proof. The number of $n$-bit strings differing from a given vertex in exactly $i$ bits equals the number of ways to choose $i$ bit positions out of $n$, which is $\binom{n}{i}$. Pascal's triangle is symmetric, $\binom{n}{i} = \binom{n}{n-i}$, so the distance distribution is symmetric around $n/2$.

Lemma 19: between two vertices at distance $k$ in $Q_n$, there are exactly $k!$ distinct shortest paths.

Proof. A shortest path from $u$ to $v$ with $\varrho(u, v) = k$ must invert exactly the $k$ bits in which they differ - once each. The order in which these $k$ bits are inverted is an arbitrary permutation of those $k$ coordinates. Hence $k!$ distinct shortest paths.

### Properties VI - vertex-disjoint paths (Lemma 20)

Lemma 20: if $u, v \in V(Q_n)$ with $\varrho(u, v) = k$, then there exist $n$ vertex-disjoint paths $P(u, v)$ among which:

- $k$ are of length $k$
- $n - k$ are of length $k + 2$

Construction of the $k$ short paths. Build the first path by inverting the $k$ differing bits in some fixed order. For the remaining $k - 1$ short paths, use $k$ different rotations of the initial permutation. The key invariant: on any two different paths, the first $1 \leq i \leq k - 1$ inversions must use a different subset of $i$ dimensions out of the $k$ differing coordinates - guaranteed by rotation.

Construction of the $n - k$ longer paths. Pick any bit $j$ in which $u$ and $v$ `do not differ`. Start by inverting bit $j$ (step aside into a disjoint sub-cube), traverse the $k$ differing bits in some order (length $k$ inside that sub-cube), then invert bit $j$ again (step back). Total length $k + 2$. There are $n - k$ such "side trip" dimensions, giving $n - k$ longer disjoint paths.

Practical significance: $Q_n$ has $n$ parallel routes between any pair of vertices. A large packet can be split into $n$ chunks and sent in parallel, achieving up to $n$-fold throughput over a single-route transmission.

### Drawbacks and today's importance

Two main `drawbacks` of the hypercube:

- `Logarithmic degree`: $\deg = n$ grows with system size, so a single router component can only support a fixed maximum dimension. Arbitrarily large hypercubes cannot be built from a single router type.
- `Scalability only by powers of two`: sizes $N = 2^n$.

Consequence: real supercomputers use only hypercubes of `lower dimensions`. Example: `Salomon` at IT4I (SGI) uses $Q_7$ (routers with 7 ports), organized into "M-cells" that are 7-dimensional hypercubes.

Nevertheless, the hypercube remains the `testbed for feasibility of parallel solutions` in the distributed-memory model - analogous to PRAM in the shared-memory world:

> If you are not able to find a good solution on a hypercube topology, it is probably difficult to solve in general in a distributed manner.

Because of its density, $Q_n$ simulates efficiently almost any other topology.

### Summary table (key formulas)

- $|V(Q_n)| = 2^n$
- $|E(Q_n)| = n \cdot 2^{n-1}$
- $\deg(Q_n) = {n}$ ($n$-regular)
- $\mathrm{diam}(Q_n) = n$ (logarithmic in $N$, optimal for dense topology with this degree)
- $\mathrm{dist}(Q_n) \approx n/2$
- $\kappa(Q_n) = \lambda(Q_n) = n$ (optimal connectivity)
- $\mathrm{bw}_e(Q_n) = 2^{n-1} = N/2$ (maximum possible)
- Bipartite, balanced; 2-coloring = parity
- Hamiltonian (any $n$-bit Gray code is a Hamiltonian cycle)
- Vertex-symmetric with $2^n \cdot n!$ automorphisms; $n!$ automorphisms per ordered vertex pair
- Number of vertices at distance $i$: $\binom{n}{i}$
- Number of shortest paths between vertices at distance $k$: $k!$
- Between any $u, v$ with $\varrho(u, v) = k$: $n$ vertex-disjoint paths ($k$ of length $k$, $n - k$ of length $k + 2$)

### Potential exam questions

1. Give the formal definition of the $n$-dimensional binary hypercube $Q_n$. Write down the vertex set, edge set, and the parameters $|V|, |E|, \mathrm{diam}, \deg, \mathrm{bw}_e$.
2. Draw $Q_3$ and $Q_4$. Label all vertices with their binary addresses. Identify the two sub-$Q_3$'s inside $Q_4$ and the "dimension-3" matching between them.
3. Express $Q_n$ as a Cartesian product. Use this to argue hierarchical recursivity: what is $Q_n$ in terms of $Q_p$ and $Q_{n-p}$?
4. Define the Hamming distance and explain why $\mathrm{dist}_{Q_n}(u, v) = \varrho(u, v)$. What is the average distance in $Q_n$, and how does it follow from Pascal's triangle symmetry?
5. Prove that $\mathrm{bw}_e(Q_n) = N/2 = 2^{n-1}$. Why does this make $Q_n$ "ideal for binary divide-and-conquer algorithms"?
6. Prove that $Q_n$ is balanced bipartite. What is the natural 2-coloring, and why does every edge connect vertices of opposite color?
7. Prove that $Q_n$ is Hamiltonian. What Hamiltonian cycle is provided by an $n$-bit Gray code?
8. State and outline the proof of Theorem 16: $Q_n$ is vertex-symmetric with $2^n \cdot n!$ automorphisms. Describe the two independent families $\kappa(\pi)$ (dimension permutations) and $\tau_{u,v}$ (translations).
9. State and prove Corollary 17: for any $u, v \in V(Q_n)$, there are $n!$ automorphisms $f_{u,v}$ with $f_{u,v}(u) = v$. Write down the explicit formula $f(x) = \pi(x) \oplus (v \oplus \pi(u))$ and verify it.
10. Describe `e-cube routing` in $Q_n$. Why is the bit order fixed (LSB to MSB)? Why is it deadlock-free despite the high cyclicity of $Q_n$?
11. Prove Lemma 18: the number of vertices at distance $i$ from a given vertex is $\binom{n}{i}$. Deduce the average distance in $Q_n$.
12. Prove Lemma 19: there are exactly $k!$ shortest paths between two vertices at Hamming distance $k$.
13. State and prove Lemma 20 (vertex-disjoint paths). Construct $n$ vertex-disjoint paths between $u$ and $v$ with $\varrho(u, v) = k$: describe both the $k$ short paths (length $k$) and the $n - k$ longer ones (length $k + 2$).
14. What are the two main drawbacks of the hypercube? Why do real supercomputers use only low-dimensional hypercubes? Give an example of a machine that uses a hypercube topology.
15. Why is the hypercube still considered the "testbed" for distributed-memory parallel algorithms? What does the lecturer's analogy to PRAM mean?