Topic: the `n`-dimensional torus `K(z_1, ..., z_n)` as the `wrapped` variant of the mesh - an orthogonal direct topology built as a Cartesian product of cycles rather than linear arrays. Definition, structural properties (regularity, vertex symmetry, connectivity, bipartiteness, Hamiltonicity, hierarchical behaviour), vertex-disjoint paths, and dimension-ordered routing.

### Definition of `K(z_1, ..., z_n)`

The `n-dimensional torus of side lengths z_1, ..., z_n` - also called a `toroidal mesh`, `wrapped mesh`, or `n-dimensional cycle` - is defined by:

```
V(K(z_1, ..., z_n)) = V(M(z_1, ..., z_n))
                    = { [a_1, ..., a_n] ;  0 ≤ a_i ≤ z_i - 1  ∀ i }
E(K(z_1, ..., z_n)) = { < [..., a_i, ...],  [..., a_i ⊕_{z_i} 1, ...] > ;
                          0 ≤ a_i < z_i }
```

The vertex set is identical to the mesh `M(z_1, ..., z_n)`; the edge set is the mesh edge set `plus wraparound edges` on every axis. Formally, adjacency in dimension `i` uses `modular arithmetic` `⊕_{z_i}` (addition modulo `z_i`) instead of ordinary addition.

Intuitively: take an `n`-dimensional mesh and, for every linear sub-array along each axis, add one wraparound edge connecting its two endpoints.

Key parameters:

```
|V(K(...))|  = Π_{i=1}^n z_i
|E(K(...))|  = n · Π_{i=1}^n z_i
diam(K(...)) = Σ_{i=1}^n ⌊z_i / 2⌋
deg(K(...))  = {2n}                         (i.e., the torus is 2n-regular)
bw_e(K(...)) = 2 · bw_e(M(z_1, ..., z_n))
```

Each dimension contributes `2` to every vertex's degree: one edge in each direction along that axis, even at coordinate `0` and `z_i - 1` (which the wraparound handles). This makes the torus `regular`, unlike the mesh.

### Torus as a Cartesian product

Analogous to the mesh, the torus decomposes by Cartesian product, but each factor is a `cycle` rather than a linear array:

```
K(z_1, z_2, ..., z_n)  ≡  K(z_1) × K(z_2) × ... × K(z_n),
```

where `K(z)` is the 1-D torus (a cycle or ring on `z` vertices).

Special cases:

- `K(z)` = cycle/ring of length `z`
- `K(k, k, ..., k)` = `k-ary n-torus`
- `K(2, 2, ..., 2) ≡ Q_n` (the torus collapses to the hypercube when all side lengths are 2)

Consequently, meshes, tori, and hypercubes are all members of the same family of orthogonal topologies - the hypercube is the `k = 2` special case, and tori and meshes are its generalizations.

### Comparison: torus vs mesh

Adding a very small number of extra edges (the wraparound) to the mesh has dramatic consequences:

- `Diameter` and `average distance` are `approximately halved` vs the equal-sided mesh (since you can now travel the short way around each cycle)
- `Connectivity` and `bisection width` are `approximately doubled` vs the equal-sided mesh
- The torus becomes `regular` (all vertices have degree `2n`), whereas the mesh has vertices of degrees `n` through `n + j`
- The torus becomes `vertex-symmetric` (Theorem 22), whereas the mesh is not

### Hierarchical behaviour

Unlike meshes, tori are `not hierarchically recursive`: a torus cannot be decomposed into sub-tori of the `same` dimension. However:

- Cartesian product is still the constructor: `K(z_1, ..., z_n) ≡ K(z_1) × ... × K(z_n)`
- Tori contain `sub-tori of smaller dimensionality` (e.g., `K(z_1, z_2, z_3)` contains `K(z_1, z_2)` as sub-torus by fixing one coordinate)

The failure of same-dimension recursivity follows from the wraparound: you cannot take a "sub-arc" of a cycle and get another cycle - you get a linear array instead, which is a `mesh`, not a torus.

### Vertex symmetry (Theorems 21, 22)

Theorem 21: the 1-D torus `K(z)` is vertex-symmetric. Constructive proof: for any `a, b ∈ V(K(z))`, define the cyclic shift

```
f_{a,b}(x)  =  x ⊕_z (b ⊖_z a).
```

- Trivially a bijection
- Adjacency preservation from commutativity and associativity of modular arithmetic:

```
f_{a,b}(x ⊕_z 1) = (x ⊕_z 1) ⊕_z (b ⊖_z a)
                 = ((x ⊕_z (b ⊖_z a)) ⊕_z 1
                 = f_{a,b}(x) ⊕_z 1.
```

Remark: besides cyclic shifts, `K(z)` also admits `mirror symmetry`.

Theorem 22: `K(z_1, ..., z_n)` is vertex-symmetric, with automorphisms being `translations` (vector shifts). Proof: immediate corollary of `K(z_1, ..., z_n) ≡ K(z_1) × ... × K(z_n)` combined with Theorems 5, 7, and 21. The automorphisms are `coordinate-wise cyclic shifts`:

```
f([a_1, ..., a_n])  =  [a_1 ⊕_{z_1} d_1,  ...,  a_n ⊕_{z_n} d_n]
```

for some translation vector `(d_1, ..., d_n)`. Any vertex can be mapped to any other by choosing the right translation.

Contrast with meshes: meshes fail vertex symmetry because corner vertices have lower degree than interior ones - by Theorem 7(2), `vertex symmetry implies regularity`, and meshes are not regular. Tori, by adding wraparounds, repair this degree irregularity and become vertex-symmetric.

### Bipartiteness (Theorem 23)

Theorem 23: `K(z_1, ..., z_n)` is bipartite `iff all dimensions have even size` (and balanced in that case). In contrast, `M(z_1, ..., z_n)` is `always bipartite`.

Proof idea:

- Two vertices of a torus are adjacent iff they differ by one in modular arithmetic in exactly one coordinate. The 2-coloring is `parity of the sum of coordinates modulo 2` - moving to any neighbor flips this parity by ±1 (which in turn flips its parity)
- For the mesh, this colouring always works because non-modular `±1` steps always flip parity
- For the torus, the wraparound in dimension `i` connects `0` to `z_i - 1`: if `z_i` is odd, this edge connects two vertices of the `same` parity (since `0` and an even number `z_i - 1` have opposite parities... actually differ by `z_i`, which is odd so parity differs - wait, the condition is more subtle). The core fact is that `odd-sized cycles are not bipartite`. So the torus is bipartite iff all its cycle factors are bipartite, which requires all `z_i` to be even

When all `z_i` are even, the torus is balanced bipartite with `Π z_i / 2` vertices in each color class.

### Hamiltonicity

Tori are `always Hamiltonian`. Every 1-D torus (cycle) is trivially Hamiltonian, and Hamiltonicity propagates through Cartesian products of cycles.

Contrast with meshes: a mesh is Hamiltonian only if at least one side has even length; otherwise it admits only a Hamiltonian path, not a circuit.

### Connectivity

Tori have `optimal connectivity`:

```
κ(K(...)) = λ(K(...)) = δ(K(...)) = 2n.
```

Every vertex has degree `2n`, and this matches both vertex and edge connectivity. Tori have no "bottleneck" vertices or edges.

### Vertex-disjoint paths (Lemma 24)

Lemma 24 generalizes the hypercube result (Lemma 20) to tori. Let `a = (a_1, ..., a_n)` and `b = (b_1, ..., b_n)` be two vertices of `K(z_1, ..., z_n)` with all `z_i > 2`, which WLOG differ in the first `k` dimensions. Define the `Manhattan distance in dimension i`:

```
m_i = min( a_i ⊖_{z_i} b_i,  b_i ⊖_{z_i} a_i )
```

(i.e., the shorter of the two cyclic offsets, choosing to go either forward or backward around the cycle). The total `Manhattan distance` between `a` and `b` is:

```
m = Σ_{i=1}^k m_i.
```

Then there exist `2n` vertex-disjoint paths `P(a, b)` distributed as follows:

- `k` paths of length `m` (shortest paths, one per dimension-order rotation)
- `2(n - k)` paths of length `m + 2` (detours through dimensions where `a` and `b` agree - "step aside" trick)
- the remaining `k` paths of length `m - m_i + z_i` for `i = 1, ..., k` (these go the `long way around` cycle `i` rather than the short way - length penalty is `z_i - 2 m_i`)

This matches the optimal connectivity `κ = 2n` - for each vertex there are exactly `2n` vertex-disjoint paths reaching any other vertex, one per edge leaving that vertex.

> Routing in a torus is like routing in Manhattan, using avenues or streets - except we have `n` such dimensions, and each axis is a cycle so you can go either direction.

### Routing in tori - dimension-ordered (XY / XYZ)

The basic shortest-path routing in tori is `dimension-ordered routing`, the direct analogue of mesh routing:

- 2-D torus: `XY routing` - fix the destination's X-coordinate first by traversing along the X-axis, then the Y-coordinate
- 3-D torus: `XYZ routing` - X first, then Y, then Z
- general `n`-D torus: dimensions fixed in a predetermined order `1, 2, ..., n`

Key differences from mesh routing:

- In each dimension `i`, choose the `shorter direction` around the cycle: forward (`+`) or backward (`-`), whichever gives offset `m_i`
- The routing is still shortest-path: total hop count equals the Manhattan distance `m`

Like `e-cube` routing on the hypercube, the fixed dimension order makes XY(Z) routing `deadlock-free` - no cyclic wait among dimensions can form. To produce vertex-disjoint parallel paths (for packet splitting), use `different dimension orderings` across paths, following the construction of Lemma 24.

### Example parameters and practical importance

Tori are `commercially successful`:

- IBM BlueGene used `3-D tori`
- BlueWaters used `3-D tori`
- Fujitsu built a `6-D torus`

You can build tori of any dimension - limited only by engineering creativity for the 3-D physical layout (racks form 2-D grids on the floor, stacked vertically - mapping higher-dimensional tori onto 3-D space requires cable routing tricks).

Among equal-sized meshes, tori, and hypercubes, the `torus strikes the best trade-off`: diameter smaller than the mesh but larger than the hypercube; density and bisection width likewise intermediate. Numerical example for `N = 256`:

```
                M(8,8,4)    K(8,8,4)    Q_8
diameter        17          10          8
|E|             640         768         1024
bw_e            32          64          128
```

Finally, `optimal algorithms exist for many fundamental problems on tori`, and tori interact well with dimension-ordered collective communication patterns - XY(Z) routing also drives broadcast, reduction, and all-to-all primitives.

### Summary of `K(z_1, ..., z_n)` parameters

Compact summary:

- Vertices: `Π z_i` tuples `[a_1, ..., a_n]` with `0 ≤ a_i < z_i`
- Edges: `n · Π z_i`, wraparound via modular arithmetic in each dimension
- Regular: yes, `2n`-regular (so sparse when `n` is constant - as typically assumed)
- Diameter: `Σ ⌊z_i / 2⌋` - roughly half the mesh's diameter
- Connectivity: optimal, `κ = λ = δ = 2n`
- Bisection width: `2 · bw_e(M(z_1, ..., z_n))` - double the mesh's
- Vertex-symmetric: yes (Theorem 22, automorphisms are translations)
- Hamiltonian: always
- Bipartite: iff all `z_i` are even (Theorem 23); balanced in that case
- Hierarchical recursivity: `not` h.r. to same-dimension sub-tori, but is h.r. to `lower-dimension` sub-tori; constructor is Cartesian product of cycles
- Special cases: `K(z)` = cycle; `K(2, ..., 2) ≡ Q_n`
- Vertex-disjoint paths between `a, b` at Manhattan distance `m`: `2n` paths, distributed in three length classes (Lemma 24)
- Routing: dimension-ordered (XY / XYZ), choose shorter direction around each cycle, deadlock-free

### Potential exam questions

1. Give the formal definition of the `n`-dimensional torus `K(z_1, ..., z_n)`: vertex set, edge set, and key parameters (`|V|`, `|E|`, diameter, degree, bisection width).
2. Express the torus as a Cartesian product. What are the special cases `K(z)`, `K(k, k, ..., k)`, and `K(2, 2, ..., 2)`?
3. Compare meshes and tori: how do diameter, average distance, connectivity, bisection width, regularity, and vertex symmetry differ?
4. Why is the torus regular while the mesh is not? How does regularity relate to vertex symmetry (via Theorem 7)?
5. State and prove Theorem 21: the 1-D torus `K(z)` is vertex-symmetric. Construct the cyclic-shift automorphism `f_{a,b}(x) = x ⊕_z (b ⊖_z a)` and verify adjacency preservation.
6. Derive Theorem 22 (vertex symmetry of the `n`-D torus) as a corollary of Theorems 5, 7, and 21. What do the automorphisms look like?
7. State Theorem 23: when is `K(z_1, ..., z_n)` bipartite? What 2-coloring works? Why are meshes always bipartite but tori only sometimes?
8. When is `K(z_1, ..., z_n)` Hamiltonian? Contrast with the mesh, which is Hamiltonian only if at least one side has even length.
9. Is the torus hierarchically recursive? Explain why it cannot be decomposed into same-dimension sub-tori, only into lower-dimension sub-tori.
10. State Lemma 24 on vertex-disjoint paths in tori. Define `Manhattan distance` `m_i` and total `m`. How many vertex-disjoint paths exist between two vertices, and what are their length classes? Why does the count `2n` match `κ`?
11. Describe `XY routing` in a 2-D torus `K(z_1, z_2)`. Given `a = [1, 3]` and `b = [5, 1]` in `K(8, 8)`, write out the XY-routed path.
12. Why is dimension-ordered routing in a torus deadlock-free?
13. How does one choose between "forward" and "backward" along each cycle in torus routing? When are the two directions the same length?
14. List commercial parallel computers that use torus-based topologies and state their dimensions (e.g., IBM BlueGene, BlueWaters, Fujitsu).
15. Compare `M(8, 8, 4)`, `K(8, 8, 4)`, and `Q_8` for `N = 256` in terms of diameter, edge count, and bisection width. Which topology strikes the best cost/latency trade-off?