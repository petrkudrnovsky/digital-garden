Topic: the binary hypercube `Q_n`, its formal definition and key graph-theoretic parameters, structural properties (recursivity, vertex symmetry, bisection, bipartiteness, hamiltonicity, vertex-disjoint paths), and standard `e-cube` shortest-path routing.

### Definition of the binary hypercube `Q_n`

The `n-dimensional binary hypercube` `Q_n` is the graph with:

```
V(Q_n) = {0, 1}^n = { x_{n-1} x_{n-2} ... x_0 ; x_i ∈ {0, 1} }
E(Q_n) = { <x, neg_i(x)> ;  x ∈ V(Q_n),  0 ≤ i ≤ n-1 }
```

where `neg_i(x)` denotes the `n`-bit string obtained from `x` by flipping bit `i`. So vertices are `n`-bit binary addresses, and two vertices are adjacent iff they differ in exactly one bit.

Key parameters:

```
|V(Q_n)| = 2^n
|E(Q_n)| = n · 2^{n-1}
diam(Q_n) = n
deg(Q_n) = {n}          (n-regular)
bw_e(Q_n) = 2^{n-1} = N/2
```

The figure of `Q_4` appears as two copies of `Q_3` (addresses `0xxx` and `1xxx`) joined by a perfect matching of "dimension-3" edges.

### Hamming distance

The `Hamming distance` `ϱ(u, v)` is the number of bit positions in which the two `n`-bit addresses `u` and `v` differ. It coincides with the graph distance:

```
dist_{Q_n}(u, v) = ϱ(u, v).
```

This identification underlies essentially all properties of `Q_n`.

### Properties I - recursivity, Boolean structure, connectivity, bisection, bipartiteness, hamiltonicity

`Q_n is regular with logarithmic diameter ⇒ dense topology.` Since `deg = n` grows with `n`, `Q_n` is dense (not sparse). Nevertheless its `diam = n = log_2 N` is the optimal logarithmic diameter predicted by Theorem 15.

`Hierarchically recursive` via Cartesian product:

```
Q_n ≡ Q_p × Q_{n-p} ≡ Q_p × Q_q × Q_{n-p-q} = Q_1^n
```

for any `0 < p, q < n`. Subcubes are addressed by strings `s_{n-1} ... s_1 s_0` with `s_i ∈ {0, 1, *}` (where `*` is the "don't care" symbol). These correspond exactly to `terms in Boolean algebra` (minterms/maxterms when all bits are fixed, more general terms with `*`).

`Optimal connectivity`:

```
κ(Q_n) = λ(Q_n) = δ(Q_n) = n.
```

The minimum number of vertices (or edges) whose removal disconnects `Q_n` equals its regular degree.

`Largest possible bisection width`:

```
bw_e(Q_n) = N/2 = 2^{n-1}.
```

This is the upper bound for any graph on `N` vertices, and makes `Q_n` ideal for binary divide-and-conquer algorithms. Cutting `Q_n` into two `Q_{n-1}`'s along any single dimension removes exactly `2^{n-1}` edges, because each vertex on one side has exactly one image on the other side.

`Balanced bipartite graph`. `Parity` (the XOR of all `n` bits, or equivalently the count of 1-bits mod 2) provides a valid 2-coloring: every edge flips exactly one bit and therefore flips the parity, so adjacent vertices have opposite colors. Both color classes have size `2^{n-1}`.

`Hamiltonian graph`. Any `n-bit Gray code` is a Hamiltonian circuit in `Q_n` - e.g., the binary reflected Gray code. In fact `Q_n` has many Hamiltonian cycles.

### Properties II - vertex symmetry (Theorem 16)

Theorem 16: `Q_n` is vertex-symmetric, with `2^n × n!` different automorphisms.

Proof sketch. Vertex symmetry follows from Theorem 7(1), since `Q_1` is trivially vertex-symmetric and `Q_n ≡ Q_1^n`. The full count comes from composing two independent families:

1. `Dimension permutations` `κ(π)`. For a permutation `π : {0, ..., n-1} → {0, ..., n-1}`,

```
κ(π)(x_{n-1} ... x_0) = x_{π(n-1)} ... x_{π(0)}.
```

This preserves Hamming distance (just relabels bit positions), hence adjacency. There are `n!` such permutations. 2. `Translations` `τ_{u,v}`. For any `u, v ∈ V(Q_n)`,

```
τ_{u,v}(x) = x XOR (u XOR v).
```

XOR with a fixed vector preserves Hamming differences (hence adjacency) and is trivially a bijection. There are `2^n` such translations.

Composing these yields `2^n × n!` automorphisms in total.

> The hypercube has much more symmetry than is needed by the vertex-symmetry definition.

### Properties III - automorphisms between a given pair (Corollary 17)

Corollary 17: for any `u, v ∈ V(Q_n)`, there exist `n!` automorphisms `f_{u, v}` such that `f_{u, v}(u) = v`.

Constructive proof. Pick any permutation `π` of the `n` dimensions (out of `n!` choices). Compose with a translation that sends `π(u)` to `v`:

```
f(x) = π(x) XOR (v XOR π(u)).
```

Substituting `x = u`:

```
f(u) = π(u) XOR v XOR π(u) = v.
```

Since `π` was arbitrary out of `n!` choices, there are `n!` such automorphisms.

### Properties IV - recursivity and `e-cube` routing

Hierarchical recursivity visually: `Q_6 ≡ Q_3 × Q_3` can be drawn as a `Q_3` each of whose vertices is itself a `Q_3`.

The hypercube is a highly cyclic graph: smallest cycles are 4-cycles, then 6-cycles, 8-cycles, and so on - always `even-length` (because `Q_n` is bipartite). This cyclicity provides routing redundancy.

The standard shortest-path routing in `Q_n` is called `e-cube routing` (dimension-ordered routing):

- Bits in the `n`-bit addresses are tested always `from the right to the left` (from LSB to MSB)
- At each step, if the current bit differs between the current node and the destination, traverse the edge in that dimension; otherwise skip to the next bit

Why bit-ordered routing? A routing scheme must be `deadlock-free`. Because `Q_n` is highly cyclic, routing must follow a fixed dimension order to break cycles; testing bits always in the same order (LSB to MSB, or equivalently high-to-low) prevents deadlock.

Pseudocode for `e-cube` routing from source `s` to destination `d`:

```
current = s
for i = 0, 1, ..., n-1:
    if bit_i(current) ≠ bit_i(d):
        current = neg_i(current)    // traverse dimension-i edge
return current == d
```

The path length equals `ϱ(s, d) = dist(s, d)`, i.e., `e-cube` is shortest-path. Total algorithm steps: at most `n`.

Optimal algorithms exist for all `collective communication operations` on `Q_n` - broadcast, gather, scatter, all-to-all, reduction, prefix sum, etc. - all hitting the theoretical lower bounds.

### Properties V - distance distribution (Lemmas 18, 19)

Lemma 18: the number of vertices at distance `i` from a given vertex in `Q_n` is `C(n, i)`. Consequently the average distance in `Q_n` is approximately `ceil(n/2)`:

```
dist(Q_n)  ≈  n/2.
```

Proof. The number of `n`-bit strings differing from a given vertex in exactly `i` bits equals the number of ways to choose `i` bit positions out of `n`, which is `C(n, i)`. Pascal's triangle is symmetric, `C(n, i) = C(n, n-i)`, so the distance distribution is symmetric around `n/2`.

Lemma 19: between two vertices at distance `k` in `Q_n`, there are exactly `k!` distinct shortest paths.

Proof. A shortest path from `u` to `v` with `ϱ(u, v) = k` must invert exactly the `k` bits in which they differ - once each. The order in which these `k` bits are inverted is an arbitrary permutation of those `k` coordinates. Hence `k!` distinct shortest paths.

### Properties VI - vertex-disjoint paths (Lemma 20)

Lemma 20: if `u, v ∈ V(Q_n)` with `ϱ(u, v) = k`, then there exist `n` vertex-disjoint paths `P(u, v)` among which:

- `k` are of length `k`
- `n - k` are of length `k + 2`

Construction of the `k` short paths. Build the first path by inverting the `k` differing bits in some fixed order. For the remaining `k - 1` short paths, use `k` different rotations of the initial permutation. The key invariant: on any two different paths, the first `1 ≤ i ≤ k - 1` inversions must use a different subset of `i` dimensions out of the `k` differing coordinates - guaranteed by rotation.

Construction of the `n - k` longer paths. Pick any bit `j` in which `u` and `v` `do not differ`. Start by inverting bit `j` (step aside into a disjoint sub-cube), traverse the `k` differing bits in some order (length `k` inside that sub-cube), then invert bit `j` again (step back). Total length `k + 2`. There are `n - k` such "side trip" dimensions, giving `n - k` longer disjoint paths.

Practical significance: `Q_n` has `n` parallel routes between any pair of vertices. A large packet can be split into `n` chunks and sent in parallel, achieving up to `n`-fold throughput over a single-route transmission.

### Drawbacks and today's importance

Two main `drawbacks` of the hypercube:

- `Logarithmic degree`: `deg = n` grows with system size, so a single router component can only support a fixed maximum dimension. Arbitrarily large hypercubes cannot be built from a single router type.
- `Scalability only by powers of two`: sizes `N = 2^n`.

Consequence: real supercomputers use only hypercubes of `lower dimensions`. Example: `Salomon` at IT4I (SGI) uses `Q_7` (routers with 7 ports), organized into "M-cells" that are 7-dimensional hypercubes.

Nevertheless, the hypercube remains the `testbed for feasibility of parallel solutions` in the distributed-memory model - analogous to PRAM in the shared-memory world:

> If you are not able to find a good solution on a hypercube topology, it is probably difficult to solve in general in a distributed manner.

Because of its density, `Q_n` simulates efficiently almost any other topology.

### Summary table (key formulas)

- `|V(Q_n)| = 2^n`
- `|E(Q_n)| = n · 2^{n-1}`
- `deg(Q_n) = {n}` (n-regular)
- `diam(Q_n) = n` (logarithmic in `N`, optimal for dense topology with this degree)
- `dist(Q_n) ≈ n/2`
- `κ(Q_n) = λ(Q_n) = n` (optimal connectivity)
- `bw_e(Q_n) = 2^{n-1} = N/2` (maximum possible)
- Bipartite, balanced; 2-coloring = parity
- Hamiltonian (any `n`-bit Gray code is a Hamiltonian cycle)
- Vertex-symmetric with `2^n · n!` automorphisms; `n!` automorphisms per ordered vertex pair
- Number of vertices at distance `i`: `C(n, i)`
- Number of shortest paths between vertices at distance `k`: `k!`
- Between any `u, v` with `ϱ(u, v) = k`: `n` vertex-disjoint paths (`k` of length `k`, `n - k` of length `k + 2`)

### Potential exam questions

1. Give the formal definition of the `n`-dimensional binary hypercube `Q_n`. Write down the vertex set, edge set, and the parameters `|V|, |E|, diam, deg, bw_e`.
2. Draw `Q_3` and `Q_4`. Label all vertices with their binary addresses. Identify the two sub-`Q_3`'s inside `Q_4` and the "dimension-3" matching between them.
3. Express `Q_n` as a Cartesian product. Use this to argue hierarchical recursivity: what is `Q_n` in terms of `Q_p` and `Q_{n-p}`?
4. Define the Hamming distance and explain why `dist_{Q_n}(u, v) = ϱ(u, v)`. What is the average distance in `Q_n`, and how does it follow from Pascal's triangle symmetry?
5. Prove that `bw_e(Q_n) = N/2 = 2^{n-1}`. Why does this make `Q_n` "ideal for binary divide-and-conquer algorithms"?
6. Prove that `Q_n` is balanced bipartite. What is the natural 2-coloring, and why does every edge connect vertices of opposite color?
7. Prove that `Q_n` is Hamiltonian. What Hamiltonian cycle is provided by an `n`-bit Gray code?
8. State and outline the proof of Theorem 16: `Q_n` is vertex-symmetric with `2^n · n!` automorphisms. Describe the two independent families `κ(π)` (dimension permutations) and `τ_{u,v}` (translations).
9. State and prove Corollary 17: for any `u, v ∈ V(Q_n)`, there are `n!` automorphisms `f_{u,v}` with `f_{u,v}(u) = v`. Write down the explicit formula `f(x) = π(x) XOR (v XOR π(u))` and verify it.
10. Describe `e-cube routing` in `Q_n`. Why is the bit order fixed (LSB to MSB)? Why is it deadlock-free despite the high cyclicity of `Q_n`?
11. Prove Lemma 18: the number of vertices at distance `i` from a given vertex is `C(n, i)`. Deduce the average distance in `Q_n`.
12. Prove Lemma 19: there are exactly `k!` shortest paths between two vertices at Hamming distance `k`.
13. State and prove Lemma 20 (vertex-disjoint paths). Construct `n` vertex-disjoint paths between `u` and `v` with `ϱ(u, v) = k`: describe both the `k` short paths (length `k`) and the `n - k` longer ones (length `k + 2`).
14. What are the two main drawbacks of the hypercube? Why do real supercomputers use only low-dimensional hypercubes? Give an example of a machine that uses a hypercube topology.
15. Why is the hypercube still considered the "testbed" for distributed-memory parallel algorithms? What does the lecturer's analogy to PRAM mean?