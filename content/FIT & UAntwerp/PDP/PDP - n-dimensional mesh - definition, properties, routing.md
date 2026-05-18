# Exam question 25: n-dimensional hypercube - definition, properties, routing

Topic: the binary hypercube `Q_n` as the canonical `orthogonal direct topology` based on Boolean algebra - its formal definition, structural properties (regularity, distance, connectivity, bisection, bipartiteness, Hamiltonicity, vertex symmetry), and routing algorithm (`e-cube`).

### Definition of `Q_n`

The `binary hypercube of dimension n`, denoted `Q_n`, is defined by:

```
V(Q_n) = {0, 1}^n = { x_{n-1} x_{n-2} ... x_0 ; x_i ∈ {0, 1} }
E(Q_n) = { <x, neg_i(x)> ; x ∈ V(Q_n),  0 ≤ i ≤ n - 1 }
```

where `neg_i(x)` denotes the `n`-bit string obtained from `x` by flipping bit `i`. In words: vertices are `n`-bit binary strings, and two vertices are adjacent iff they differ in exactly one bit position.

Key parameters:

```
|V(Q_n)|    = 2^n
|E(Q_n)|    = n · 2^{n-1}
diam(Q_n)   = n
deg(Q_n)    = {n}          (i.e., Q_n is n-regular)
bw_e(Q_n)   = 2^{n-1} = N/2
```

The `Hamming distance` `ϱ(u, v)` (number of bit positions where `u` and `v` differ) coincides with the graph distance in `Q_n`.

### Properties I - regularity, density, recursivity, subcubes

`Q_n` is `n-regular` and has `logarithmic diameter` (`diam = n = log_2 N`). Because the degree grows with `n`, `Q_n` is a `dense topology`, not sparse.

`Hierarchical recursivity` via Cartesian product:

```
Q_n ≡ Q_p × Q_{n-p}  ≡  Q_p × Q_q × Q_{n-p-q}  =  Q_1^n
```

(with `Q_1` being a single edge - two vertices labeled 0 and 1 connected by an edge).

`Subcubes` correspond to Boolean terms. A subcube is specified by a string `s_{n-1} ... s_1 s_0` where each `s_i ∈ {0, 1, *}` and `*` is the `don't-care` symbol. The subcube contains all vertices that match the fixed bits and are free on the starred positions. These are exactly the `terms in Boolean algebra` - min-terms, max-terms, and intermediate terms - now reinterpreted as sub-structures of the hypercube.

Example graphical recursivity: `Q_6 ≡ Q_3 × Q_3` - `Q_6` can be drawn as a `Q_3` whose vertices are themselves `Q_3`s.

### Properties II - connectivity and bisection

`Q_n` has `optimal connectivity`:

```
κ(Q_n) = λ(Q_n) = δ(Q_n) = n.
```

That is, vertex connectivity, edge connectivity, and minimum degree all coincide at `n`.

`Bisection width` is the `largest possible`:

```
bw_e(Q_n) = 2^{n-1} = N/2.
```

The recursive picture makes this obvious: `Q_n ≡ Q_{n-1} × Q_1`, and splitting along the `Q_1` factor cuts exactly `2^{n-1}` edges - every vertex on the left has exactly one image on the right. This maximum bisection makes `Q_n` `ideal for binary divide-and-conquer algorithms`.

### Properties III - bipartiteness and Hamiltonicity

`Q_n` is a `balanced bipartite graph`. The 2-coloring is given by `parity` (number of 1-bits modulo 2): every edge flips exactly one bit, hence flips parity, so endpoints of every edge have opposite parities. The two color classes have equal size `2^{n-1}`.

`Q_n` is `Hamiltonian`. Any `n`-bit `Gray code` is a Hamiltonian circuit in `Q_n`. Gray codes are sequences of all `2^n` strings in which consecutive entries differ in exactly one bit - the standard example is the `binary reflected Gray code`. Many Hamiltonian circuits exist; Gray codes are the most popularly known construction.

`Q_n` is also a highly cyclic graph: starting with 4-cycles (the smallest, since `Q_n` is bipartite so no odd cycles), then 6, 8, 10-cycles, always even. This cyclicity provides `routing redundancy` and supports deadlock avoidance.

### Properties IV - vertex symmetry (Theorem 16)

Theorem 16: `Q_n` is vertex-symmetric with `2^n × n!` different automorphisms.

Proof sketch. Vertex symmetry follows from Theorem 7 combined with `Q_n ≡ Q_1^n` (and `Q_1` is trivially vertex-symmetric). All automorphisms are constructed by composing two independent families:

1. `Dimension permutations`. For a permutation `π : {0, ..., n-1} → {0, ..., n-1}`, define

```
κ(π) : Q_n → Q_n,   κ(π)(x_{n-1} ... x_0) = x_{π(n-1)} ... x_{π(0)}.
```

There are `n!` such permutations. Each preserves Hamming distance, hence adjacency. 2. `Translations`. For any `u, v ∈ V(Q_n)`, define

```
τ_{u,v} : Q_n → Q_n,   τ_{u,v}(x) = x XOR (u XOR v),
```

where XOR is bitwise addition modulo 2. There are `2^n` such translations (one per translation vector). XOR with a fixed vector preserves Hamming differences, hence adjacency.

Composing yields `2^n × n!` automorphisms. `Q_n` has far more automorphisms than the definition of vertex symmetry requires.

Corollary 17: for any pair `u, v ∈ V(Q_n)`, there are `n!` automorphisms `f_{u,v}` with `f_{u,v}(u) = v`. Explicit formula:

```
f(x) = π(x) XOR (v XOR π(u))
```

for any chosen permutation `π` of `n` dimensions. Substituting `x = u`:

```
f(u) = π(u) XOR (v XOR π(u)) = v. ✓
```

### Properties V - distance distribution and shortest path count

Lemma 18: the number of vertices at distance `i` from a given vertex in `Q_n` is

```
C(n, i) = ( n choose i ).
```

Proof: the number of `n`-bit strings differing in exactly `i` bits from a given vertex equals the number of ways to choose `i` bit positions out of `n`. By the symmetry of Pascal's triangle (`C(n, i) = C(n, n-i)`), the distance distribution is symmetric about `n/2`.

The `average distance` is therefore:

```
dist(Q_n) ≈ ⌈n/2⌉.
```

Lemma 19: between two vertices in distance `k`, there are exactly `k!` different shortest paths. Proof: each shortest path between `u` and `v` corresponds to a permutation of the `k` bit-coordinates in which `u` and `v` differ - each order yields a distinct shortest path.

### Properties VI - vertex-disjoint paths (Lemma 20)

Lemma 20: if `u, v ∈ V(Q_n)` with `ϱ(u, v) = k`, then there exist `n` vertex-disjoint paths `P(u, v)` among which:

- `k` paths are of length `k`
- `n - k` paths are of length `k + 2`

Proof idea (constructive):

- Build the first path of length `k` by inverting the `k` differing bits in some order (any permutation works)
- For the other `k - 1` shortest paths, use `k` different rotations of this initial permutation. The key observation: for every intermediate stage `1 ≤ i ≤ k - 1`, different rotations invert different `subsets` of `i` dimensions out of the `k` differing ones - so intermediate vertices never coincide, guaranteeing vertex disjointness
- The remaining `n - k` paths of length `k + 2` are built by starting with a `side trip` through some bit `j` in which `u` and `v` `do not differ`: invert bit `j` (step aside into a disjoint subcube), traverse the `k` differing bits inside that subcube, then invert bit `j` again to return. This adds 2 extra edges - hence length `k + 2`

This matches the optimal connectivity `κ(Q_n) = n`.

### e-cube routing

The `standard shortest-path routing` algorithm in `Q_n` is called `e-cube routing`:

> Bits in `n`-bit addresses are tested always from the right to the left. At each step, the first differing bit (lowest-index mismatch) is flipped.

Equivalently: to route from source `u` to destination `v`, compute `d = u XOR v` (the Hamming difference vector) and flip the bits of `d` in a `fixed order` (e.g., from least significant to most significant). At step `i`, if bit `i` of `d` is set, traverse the edge along dimension `i`; otherwise skip.

This produces a shortest path of length `ϱ(u, v)` (using Lemma 18's reasoning) and is `deadlock-free` because all packets use dimensions in the same fixed order, so no cyclic wait among dimensions can develop. The fixed-order property is what makes `e-cube routing` more deadlock-resistant than arbitrary dimension-reordering schemes, despite `Q_n` being a highly cyclic graph with many shortest paths (`k!` per pair at distance `k`).

> The routing must be based on something more deadlock-proof, and it has a name: `e-cube` routing.

### Summary of `Q_n` parameters

Compact summary of the hypercube `Q_n`:

- Vertices: `2^n` binary `n`-tuples
- Edges: `n · 2^{n-1}`, connecting vertices that differ in exactly one bit
- Regular: yes, `n`-regular (dense topology)
- Diameter: `n` (logarithmic in `N`)
- Average distance: `≈ ⌈n/2⌉`
- Connectivity: optimal, `κ = λ = δ = n`
- Bisection width: `N/2` (maximum possible)
- Vertex-symmetric: yes, with `2^n · n!` automorphisms
- Hierarchically recursive: yes, `Q_n ≡ Q_p × Q_{n-p}`
- Balanced bipartite: yes (parity 2-coloring)
- Hamiltonian: yes (Gray codes give Hamiltonian circuits)
- Number of vertices at distance `i`: `C(n, i)`
- Number of shortest paths between vertices at distance `k`: `k!`
- Vertex-disjoint paths between vertices at distance `k`: `n` (matching optimal connectivity)
- Routing: `e-cube` (flip bits in fixed order, deadlock-free)

Optimal algorithms exist for `all collective communication operations` on `Q_n`.

### Importance and drawbacks

Two main `drawbacks` of the hypercube:

1. `Logarithmic degree` - supercomputers cannot be built arbitrarily large from one router component (router port count grows with dimension)
2. `Scalability only by powers of 2`

As a result, real supercomputers use only `low-dimensional hypercubes`. Example: Salomon at IT4I (manufactured by SGI) is built on `Q_7` - routers have 7 ports, and "M-Cells" are 7-dimensional cubes.

Despite these drawbacks, the hypercube is the `testbed for feasibility of distributed-memory parallel solutions`, analogous to PRAM in the shared-memory world.

> If you are not able to find a good solution on a hypercube topology, it is probably difficult to solve in general in a distributed manner.

Because of its density, `Q_n` can `efficiently simulate almost any other topology`.

### Potential exam questions

1. Give the formal definition of the `n`-dimensional binary hypercube `Q_n`: vertex set, edge set, and key parameters (`|V|`, `|E|`, diameter, degree, bisection width).
2. Is `Q_n` a sparse or dense topology? Justify your answer.
3. Prove that `Q_n` has `n · 2^{n-1}` edges.
4. State and prove Lemma 18: the number of vertices at distance `i` from a given vertex in `Q_n` is `C(n, i)`. What is the average distance?
5. Prove Lemma 19: there are exactly `k!` shortest paths between two vertices at distance `k` in `Q_n`.
6. State and sketch the proof of Lemma 20 (vertex-disjoint paths in `Q_n`). How many vertex-disjoint paths exist between two vertices at Hamming distance `k`, and what are their lengths?
7. State Theorem 16. How many automorphisms does `Q_n` have? Describe the two independent families (dimension permutations and translations) and prove each is an automorphism.
8. Prove Corollary 17: for any pair `u, v ∈ V(Q_n)`, there are `n!` automorphisms mapping `u` to `v`. Write out the explicit formula `f(x) = π(x) XOR (v XOR π(u))` and verify `f(u) = v`.
9. Show that `Q_n` is hierarchically recursive via Cartesian product. Give the decomposition `Q_n ≡ Q_p × Q_{n-p}` and draw `Q_6 ≡ Q_3 × Q_3`.
10. What is a `subcube` in `Q_n`? Explain the connection to Boolean algebra terms.
11. Show that `Q_n` has optimal connectivity `κ(Q_n) = λ(Q_n) = δ(Q_n) = n` and bisection width `N/2`. Why is the latter ideal for binary divide-and-conquer algorithms?
12. Prove that `Q_n` is balanced bipartite, using parity as a 2-coloring.
13. What is a Gray code? Explain why any `n`-bit Gray code is a Hamiltonian circuit in `Q_n`.
14. Describe `e-cube routing` in `Q_n`. Given source `0110` and destination `1101`, construct the e-cube path and compute its length.
15. Why is e-cube routing deadlock-free, even though `Q_n` is highly cyclic (it contains 4-, 6-, 8-cycles, etc.)?
16. Discuss the drawbacks of the hypercube as a real-world INPC. Why do supercomputers use only low-dimensional hypercubes? Give an example (Salomon / IT4I).
17. Why is `Q_n` considered the `testbed` for distributed-memory parallel algorithms, analogous to PRAM in the shared-memory world?