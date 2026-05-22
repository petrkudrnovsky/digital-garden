
> [!tldr] First 5 minutes in hell
> A $n$-dimensional mesh $M(z_1, z_2, \ldots, z_n)$ is like a grid of $n$ dimensions, where each $z_i$ denotes a number of points along the $i$-axis. A vertex has an unique address $[a_1, a_2, \ldots, a_n]$ and two vertices are neighbors iff their adresses differ at one dimension by exactly one.
> 
> A binary hypercube $Q_n$ is just a special example of $n$-dimensional mesh $M(2,2,\ldots, 2)$.
> - $M(k,k,\ldots, k)$ is a $k$-ary cube
> 
> It is a Cartesian product of linear arrays: $M(z_1, z_2, \ldots, z_n) \equiv M(z_1) \times M(z_2) \times \cdots \times M(z_n)$.
> 
> The most practical meshes are 2D and 3D.
> 
> Properties:
> - number of vertices is just multiplying all dimensions $z_i$ 
> - diameter: $\sum_{i=1}^{n} (z_i - 1)$ - the maximum path length is from one corner to the opposite corner (I have to travel $z_i-1$ edges in each dimension)
> - meshes are hierarchically recursive (the constructor is a Cartesian product)
> 	- we can have submeshes of the two flavors:
> 		- same $n$ dimensional, but some dimensions have smaller values (less vertices along the $i$-th axis)
> 		- fix some dimensions to fixed values, effectively reducing the dimensionality of the whole mesh
> 	- useful for D&C algorithms (splitting the problem into smaller subproblems and run them independently on smaller meshes)
> - degree set is ${n,\ldots , 2n}$, because corner vertices have 1 neighbors along each dimension/axis and inner has 2 neighbors per dimension (and there are $n$ dimensions)
> 	- so mesh is not regular, and that also implies that it is not vertex-symmetric (no automorphism can map a corner vertex onto the inner one (they have different degrees))
> - bisection width: 
> 	- slice through the largest dimension ($N/max z_i$ edges), if the largest dimension is even, the mesh is split exactly in half
> - mesh are always bipartite (so I can color all vertices in a way so no edge has the same color vertices), but not always balanced
> - a mesh always has a Hamiltonian path (visit every vertex once)
> 	- it has a Hamiltonian circuit (return to start after visiting all vertices) exists only of the number of vertices is even (if there is even number of vertices, the circuits alternates colors and both color classes are balanced) = at least one dimension is even
> - connectivity
> 	- $\kappa =\lambda =\delta =n$
> 	- to disconnect a corner vertex, we need to cut only $n$ edges, so the connectivity cannot exceed $n$
> 
> Routing is dimension-ordered:
> - 2D mesh: XY routing (go first along the X axis until you hit the X-position of the destination vertex, then continue on another dimension (Y) until you hit the Y-position of the destination vertex)
> - 3D mesh: XYZ routing
> - it's like generalized Manhattan distance to multiple dimensions
> - it is deadlock free, because fixing the dimensions in a given order prevents cyclic dependencies
> 
> Why use meshes?
> - constant degree regardless of size (compared to hypercubes, which have degree $log(N)$, which grows with the size of the network)
> - embed naturally in physical world (chips are 2D, server rooms are 3D)


### Definition

The n-dimensional mesh of dimensions $z_1, z_2, \ldots, z_n$, denoted $M(z_1, z_2, \ldots, z_n)$, is an orthogonal direct topology whose constructor is the Cartesian product. It is defined as follows. Each $z_i \geq 2$.

Vertex set: $$
V(M(\ldots)) = \{\, [a_1, a_2, \ldots, a_n] \mid 0 \leq a_i \leq z_i - 1 \;\; \forall i \in \{1, \ldots, n\} \,\}
$$

Edge set (two vertices are adjacent iff they differ in exactly one coordinate by exactly $\pm 1$): $$
E(M(\ldots)) = \{\, \langle [\ldots, a_i, \ldots], [\ldots, a_i + 1, \ldots] \rangle \mid 0 \leq a_i \leq z_i - 2 \,\}
$$

Equivalently as Cartesian product of linear arrays: $$M(z_1, z_2, \ldots, z_n) \equiv M(z_1) \times M(z_2) \times \cdots \times M(z_n)$$

A 1-D mesh $M(z)$ is just a linear array (path of $z$ vertices) - the counterpoint to the complete graph.

When all dimensions are equal, $M(k, k, \ldots, k)$ is called a $k$-ary $n$-cube. The binary hypercube is the special case $M(2, 2, \ldots, 2) \equiv Q_n$, so n-dimensional meshes are direct generalizations of $Q_n$.

### Basic parameters

Number of vertices: $$|V(M(\ldots))| = \prod_{i=1}^{n} z_i$$

Number of edges: $$|E(M(\ldots))| = \sum_{i=1}^{n} (z_i - 1) \prod_{\substack{j=1 \ j \neq i}}^{n} z_j$$

Diameter (sum of side lengths minus $n$): $$\mathrm{diam}(M(\ldots)) = \sum_{i=1}^{n} (z_i - 1) = \Omega!\left(\sqrt[n]{|V(M(\ldots))|}\right)$$

Degree set (mesh is NOT regular because corner/edge vertices have lower degree than interior vertices): $$
\deg(M(\ldots)) \in \{n, \ldots, n + j\}, \quad j = |\{\, z_i \mid z_i > 2 \,\}|
$$

In particular, $\Delta(M(\ldots)) = 2n$ for interior vertices and $\delta(M(\ldots)) = n$ for corner vertices (assuming all $z_i > 2$).

Bisection width (slice orthogonally through the largest dimension): $$
\mathrm{bw}_e(M(\ldots)) =
\begin{cases}
\dfrac{\prod_{i=1}^{n} z_i}{\max_i z_i} & \text{if } \max_i z_i \text{ is even,} \\[6pt]
\Omega\!\left(\dfrac{\prod_{i=1}^{n} z_i}{\max_i z_i}\right) & \text{otherwise.}
\end{cases}
$$

The piecewise formula is messy precisely because of the irregularity (an odd-sized largest dimension cannot be split exactly in half).

### Diameter comparison: meshes vs. logarithmic bound

The theorem on sparse-graph diameters (Theorem 15) states $\mathrm{diam}(G) = \Omega(\log N)$ for any $N$-vertex sparse graph. The mesh has constant degree (when $n$ is held fixed), hence it is sparse, and its diameter $\Omega(\sqrt[n]{N})$ is therefore strictly worse than the optimal $\Omega(\log N)$ for any fixed $n$. Concretely:

- $\sqrt[3]{N} \geq \log N$ for $N > 1000$
- $\sqrt{N} \geq \log N$ for $N > 16$

So meshes pay a real latency price compared to hypercubes for the benefit of having constant degree and being naturally embeddable in 2-D or 3-D physical space.

### Structural properties

Hierarchical recursivity (very strong - this is a key advantage of meshes):

- Same-dimension submeshes: for example $\mathrm{SubM}([1\text{-}3], *, [2\text{-}5], _) \subset M(6, 5, 8, 3)$, where $\_$ means "the whole range" and $[a\text{-}b]$ means coordinates restricted to that interval.
- Lower-dimension submeshes: for example $\mathrm{SubM}(*, 1, *, 3) \subset M(3, 4, 2, 7)$, obtained by fixing some coordinates.

Connectivity: meshes have optimal connectivity, $\kappa(M(\ldots)) = \lambda(M(\ldots)) = \delta(M(\ldots)) = n$ (limited by the corner-vertex degree).

Regularity / symmetry: meshes are NOT regular and therefore NOT vertex-symmetric (degrees range from $n$ at corners to $2n$ in the interior).

Bipartiteness: meshes are ALWAYS bipartite (regardless of the parities of the $z_i$). The 2-coloring is the parity of the sum of coordinates: $\mathrm{color}([a_1, \ldots, a_n]) = (a_1 + a_2 + \cdots + a_n) \bmod 2$. Moving along any edge flips exactly one coordinate by $\pm 1$, so it always flips the parity. Meshes are not necessarily balanced.

Hamiltonicity:

- $M(\ldots)$ has a Hamiltonian path always.
- $M(\ldots)$ is Hamiltonian (has a Hamiltonian circuit) iff at least one side has even length.

Number of vertices at distance $i$ in a $k$-ary $n$-cube: due to the irregularity there is no simple closed form, but the count is of order $O(i^{n-1})$.

Practical dimensions: 2-D and 3-D meshes are the most common in practice. 2-D dominates VLSI (chips are flat) and 3-D is common in large machine rooms (racks form a 2-D floor grid, stacked vertically).

### Routing

The basic shortest-path routing on meshes is dimension-ordered routing. For 2-D and 3-D meshes these are known as XY routing and XYZ routing respectively.

Algorithm (dimension-ordered routing from $u = [u_1, \ldots, u_n]$ to $v = [v_1, \ldots, v_n]$):

1. Resolve the offset in dimension $1$ first: move from $u_1$ to $v_1$ along dimension 1.
2. Then resolve the offset in dimension $2$, then dimension $3$, and so on, up to dimension $n$.

At each stage the routing uses only edges of the current dimension; the next dimension is touched only after the current offset has been fully resolved. Because the mesh has no wraparound, the shortest path in each dimension is uniquely the linear traversal from the current coordinate to the target.

Properties of dimension-ordered routing:

- It produces a shortest path - the total length equals the Manhattan distance $\sum_i |u_i - v_i|$, which equals the graph distance in $M(\ldots)$.
- It is deadlock-free under the standard dimension-ordered ordering (always traversing dimensions in the same fixed order across all packets).
- It is simple and stateless: each router only needs to know the destination address.

Many optimal communication and parallel mesh algorithms exist - "optimal" here meaning that the number of steps matches the mesh lower bounds (e.g., from diameter for broadcast, or from bisection width for permutation routing).

### Summary table of properties

- Constructor: Cartesian product of linear arrays, $M(z_1, \ldots, z_n) \equiv M(z_1) \times \cdots \times M(z_n)$
- $|V| = \prod_i z_i$, $|E| = \sum_i (z_i - 1) \prod_{j \neq i} z_j$
- $\mathrm{diam} = \sum_i (z_i - 1) = \Omega(\sqrt[n]{N})$, degree $\in {n, \ldots, 2n}$
- Not regular, not vertex-symmetric
- Always bipartite (parity of coordinate sum), not always balanced
- Hamiltonian iff at least one $z_i$ is even; always has a Hamiltonian path
- Optimal connectivity $\kappa = \lambda = \delta = n$
- Hierarchically recursive: contains both same-dimension and lower-dimension submeshes
- Routing: dimension-ordered (XY, XYZ, ...), shortest, deadlock-free
- Generalizes $Q_n$ since $M(2, 2, \ldots, 2) \equiv Q_n$

### Potential exam questions

The questions below match the lecturer's proof-heavy, definition-precise style.

1. Define formally the n-dimensional mesh $M(z_1, \ldots, z_n)$: give $V$, $E$, and write its expression as a Cartesian product.
2. Derive the formula for $|E(M(z_1, \ldots, z_n))|$. Verify it on the example $M(3, 3, 4)$.
3. Why is $M(z_1, \ldots, z_n)$ not regular, and what is its degree set? Give an example showing all degree values for $M(3, 3, 4)$.
4. State and justify the diameter of $M(z_1, \ldots, z_n)$. Compare it with the diameter lower bound for sparse graphs (Theorem 15) and explain why the mesh is strictly suboptimal in diameter for fixed $n \geq 2$.
5. Prove that $M(z_1, \ldots, z_n)$ is bipartite for all choices of $z_i$. Give the explicit 2-coloring.
6. Under what condition is $M(z_1, \ldots, z_n)$ Hamiltonian? What weaker property holds for any mesh?
7. Show that $M(z_1, \ldots, z_n)$ has optimal connectivity, i.e., $\kappa = \lambda = \delta$. What is this common value?
8. Describe the bisection width formula for $M(z_1, \ldots, z_n)$ and explain why it splits into two cases depending on the parity of $\max_i z_i$.
9. Show that $M(2, 2, \ldots, 2) \equiv Q_n$. In what sense are n-dimensional meshes generalizations of the binary hypercube?
10. Describe the dimension-ordered routing algorithm (XY / XYZ) on a mesh. Argue that it produces a shortest path and that it is deadlock-free.
11. Explain the hierarchical recursivity of meshes. Give one example of a same-dimension submesh and one example of a lower-dimension submesh inside $M(6, 5, 8, 3)$.
12. For $N = 256$, the slides compare $M(8, 8, 4) \subset K(8, 8, 4) \subset Q_8$: $\mathrm{diam} = 17, 10, 8$ respectively. Recompute the mesh diameter from the definition and explain qualitatively why the mesh has the largest diameter of the three.
13. How many vertices are at distance $i$ from a fixed vertex in a $k$-ary $n$-cube? Why is there no simple closed-form formula like there is for $Q_n$?
14. Why are 2-D and 3-D meshes the most common in practice? Relate the answer to physical (VLSI / room-scale) constraints.