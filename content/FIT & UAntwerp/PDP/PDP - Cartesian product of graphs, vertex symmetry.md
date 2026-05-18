Topic: the Cartesian product as the canonical constructor of orthogonal interconnection topologies, and the notion of vertex symmetry - a structural property that makes a topology "look the same" from any vertex and simplifies parallel algorithm design.

### Cartesian product (Definition 4)

For graphs `G_1` and `G_2`, the `Cartesian product` `G = G_1 × G_2` is defined by:

```
V(G) = { [x, y] ; x ∈ V(G_1), y ∈ V(G_2) }
E(G) = { <[x_1, y], [x_2, y]> ; <x_1, x_2> ∈ E(G_1) }
     ∪ { <[x, y_1], [x, y_2]> ; <y_1, y_2> ∈ E(G_2) }
```

In words:

- The vertex set `V(G_1 × G_2)` is the Cartesian product of `V(G_1)` and `V(G_2)` (pairs)
- For edges: two vertices `[a, b]` and `[c, d]` are adjacent in `G` iff either they agree in the second coordinate (`b = d`) and are adjacent in `G_1`, or they agree in the first coordinate (`a = c`) and are adjacent in `G_2`

Equivalently (a useful mental picture): take `G_2`, and replace every vertex of `G_2` with a copy of `G_1`; then connect corresponding vertices across copies according to the edges of `G_2`. The roles of `G_1` and `G_2` can be swapped (see Theorem 5 below).

Example: triangle `K_3` times a 2-vertex path `P_2` yields a 3-prism (two triangles connected by three parallel edges).

### Commutativity and associativity (Theorem 5)

Theorem 5: the Cartesian product is `commutative` and `associative` up to isomorphism:

```
G_1 × G_2  ≡  G_2 × G_1
(G_1 × G_2) × G_3  ≡  G_1 × (G_2 × G_3)
```

Proof (constructive, for commutativity; associativity is similar). Define

```
f : G_1 × G_2  →  G_2 × G_1,   f([x, y]) = [y, x].
```

- `f` is trivially a bijection
- `f` preserves adjacency (shown for edges of `G_1`-type; the other type is symmetric):

```
<[x_1, y], [x_2, y]> ∈ E(G_1 × G_2)
  ⇔  <x_1, x_2> ∈ E(G_1)
  ⇔  <[y, x_1], [y, x_2]> ∈ E(G_2 × G_1)
  ⇔  <f([x_1, y]), f([x_2, y])> ∈ E(G_2 × G_1)
```

Hence `f` is an isomorphism.

Consequences:

- Parentheses can be dropped from longer Cartesian-product expressions
- Iterated product notation: `G × G = G^2`, `G × G × G = G^3`, etc.
- These properties are used extensively in the construction of orthogonal topologies

### Why the Cartesian product matters for INPCs

The Cartesian product is the `constructor of orthogonal direct topologies`:

- `Binary hypercube`: `Q_n ≡ Q_1 × Q_1 × ... × Q_1 = Q_1^n` (where `Q_1` is a single edge)
- `n-dimensional mesh`: `M(z_1, ..., z_n) ≡ M(z_1) × M(z_2) × ... × M(z_n)` (each factor a linear array)
- `n-dimensional torus`: `K(z_1, ..., z_n) ≡ K(z_1) × K(z_2) × ... × K(z_n)` (each factor a cycle)

Because Theorem 5 holds up to isomorphism, these decompositions can be freely reassociated, which is what allows the hierarchical-recursivity decompositions such as `Q_6 ≡ Q_3 × Q_3`.

### Vertex symmetry (Definition 6)

A graph `G` is `vertex-symmetric` if

```
∀ u_1, u_2 ∈ V(G)   ∃ automorphism f : G → G   such that   f(u_1) = u_2.
```

(An automorphism - often abbreviated a.m. - is a bijection `V(G) → V(G)` that preserves adjacency.)

Intuition: the graph "looks the same" from any vertex. If you stand at any vertex and look around, you see an identical local structure.

Note: the definition requires only `existence of at least one` such automorphism for each pair. Many vertex-symmetric graphs (like `Q_n`) have far more than one - as their "extra symmetry" is sometimes phrased, such graphs have much more than is needed by the definition.

### Theorem 7: Cartesian product preserves vertex symmetry; vertex symmetry implies regularity

Theorem 7:

1. If `G_1` and `G_2` are vertex-symmetric, then `G = G_1 × G_2` is also vertex-symmetric.
2. `G` vertex-symmetric ⇒ `G` regular.

Proof of (1), constructive. Since `G_1, G_2` are vertex-symmetric,

```
∀ u_1, v_1 ∈ V(G_1)   ∃ AM f^1_{u_1, v_1} : G_1 → G_1 ;   f^1_{u_1, v_1}(u_1) = v_1
∀ u_2, v_2 ∈ V(G_2)   ∃ AM f^2_{u_2, v_2} : G_2 → G_2 ;   f^2_{u_2, v_2}(u_2) = v_2
```

Define, for any `[u_1, u_2], [v_1, v_2] ∈ V(G_1 × G_2)`:

```
f_{u_1, u_2, v_1, v_2}([x_1, x_2])  =  [ f^1_{u_1, v_1}(x_1),  f^2_{u_2, v_2}(x_2) ].
```

It remains to prove this mapping is a bijection preserving adjacency:

- Bijection: follows componentwise from the bijectivity of `f^1` and `f^2`
- Adjacency: if `<[x_1, y], [x'_1, y]>` is an edge (`G_1`-type) then `<x_1, x'_1> ∈ E(G_1)`, so `<f^1(x_1), f^1(x'_1)> ∈ E(G_1)`, so `<[f^1(x_1), f^2(y)], [f^1(x'_1), f^2(y)]>` is an edge in `G_1 × G_2`. Analogously for `G_2`-type edges.

Finally `f([u_1, u_2]) = [v_1, v_2]`, establishing vertex symmetry.

Proof of (2) - regularity: trivial. In a vertex-symmetric graph, for any `u_1, u_2` there is an automorphism mapping one to the other. Since automorphisms preserve adjacency, they preserve degrees: `deg(u_1) = deg(f(u_1)) = deg(u_2)`. So all vertices have the same degree, i.e., `G` is regular.

Note the converse of (2) is false: `regularity does NOT imply vertex symmetry`. Many regular graphs lack the automorphism structure required for vertex symmetry.

### Distances in vertex-symmetric graphs (Lemma 9)

Lemma 9: for every vertex-symmetric `G`,

```
diam(G) = r(G).
```

Proof: in a vertex-symmetric graph every vertex has the same eccentricity. For any `u_1, u_2` there is an automorphism `f` with `f(u_1) = u_2`; automorphisms preserve distances, hence preserve eccentricity. Thus

```
r(G) = min_{u ∈ V(G)} exc(u) = max_{u ∈ V(G)} exc(u) = diam(G).
```

Practical consequence: in a vertex-symmetric topology, every source sees the same worst-case communication delay - there are no "bad" placements.

### Example 1: vertex symmetry of the hypercube `Q_n` (Theorem 16)

Theorem 16: `Q_n` is vertex-symmetric with `2^n × n!` different automorphisms.

Proof sketch. Since `Q_1` (a single edge) is trivially vertex-symmetric and `Q_n ≡ Q_1^n`, Theorem 7(1) immediately yields vertex symmetry of `Q_n`. The explicit `2^n × n!` automorphisms are obtained by composing two independent families:

1. `Dimension permutations` `κ(π)`. For a permutation `π : {0, ..., n-1} → {0, ..., n-1}`,

```
κ(π)(x_{n-1} ... x_0) = x_{π(n-1)} ... x_{π(0)}.
```

There are `n!` such permutations. Each preserves Hamming distance, hence adjacency. 2. `Translations` `τ_{u,v}`. For `u, v ∈ V(Q_n)`,

```
τ_{u,v}(x) = x XOR (u XOR v).
```

There are `2^n` such translations (one per choice of translation vector `u XOR v`). Each preserves adjacency, since XOR with a fixed vector preserves Hamming differences.

Composing them yields `2^n × n!` automorphisms.

Corollary 17: for any pair `u, v ∈ V(Q_n)`, there exist `n!` automorphisms `f_{u,v}` with `f_{u,v}(u) = v`. Constructive proof: pick any `π` (out of `n!`), then compose with the appropriate translation:

```
f(x) = π(x) XOR (v XOR π(u)).
```

Substituting `x = u` recovers `v`, and `f` is a bijection preserving adjacency.

> The vertex symmetry definition requires only one automorphism per pair - `Q_n` provides `n!` of them for every pair.

### Example 2: vertex symmetry of tori `K(z_1, ..., z_n)` (Theorems 21, 22)

Theorem 21: the 1-D torus `K(z)` (a cycle) is vertex-symmetric. Constructive proof: for any `a, b ∈ V(K(z))`, define the cyclic shift

```
f_{a,b}(x) = x ⊕_z (b ⊖_z a).
```

This is trivially a bijection; adjacency preservation follows from commutativity and associativity of modular arithmetic:

```
f_{a,b}(x ⊕_z 1) = (x ⊕_z 1) ⊕_z (b ⊖_z a) = f_{a,b}(x) ⊕_z 1.
```

Remark: besides cyclic shifts, `K(z)` also has mirror symmetry.

Theorem 22: `K(z_1, ..., z_n)` is vertex-symmetric, with automorphisms being `translations` (vector shifts). Proof: immediate corollary of `K(z_1, ..., z_n) ≡ K(z_1) × ... × K(z_n)` combined with Theorems 5, 7, and 21 - automorphisms are coordinate-wise cyclic shifts.

This is the strong consequence of Theorem 7(1): going from a 1-D cycle (which is trivially vertex-symmetric by rotation) to an `n`-D torus of arbitrary dimensions preserves vertex symmetry automatically. Contrast with `meshes` (linear arrays, no wraparound), which are `not` vertex-symmetric because corner vertices have lower degree than interior vertices (regularity fails, so by Theorem 7(2) vertex symmetry must fail too).

### Example 3: vertex symmetry of the wrapped butterfly `wBF_n` (Theorem 25)

Theorem 25: the wrapped butterfly `wBF_n` is vertex-symmetric.

`wBF_n` is `not` a Cartesian-product graph (the cycle index `k` is rigidly tied to hypercubic dimension `k`), so Theorem 7 does not apply directly. The automorphism must simultaneously rotate cycle indices and hypercubic dimensions - and then apply a translation to map the chosen source cycle to the chosen target cycle. For vertices `(i, x)` and `(j, y)` with `d = j ⊖_n i`,

```
f_{i, x, j, y}((k, z)) = (k ⊕_n d,  rot_d(z) XOR w),   where w = rot_d(x) XOR y.
```

This example shows the general principle: even when Theorem 7 does not apply, one can often `combine` structural operations (rotations, translations) to exhibit a vertex-symmetric structure.

### Why vertex symmetry matters for INPCs

Vertex symmetry is listed among the `desirable properties` of an INPC (requirements II):

- `Easier algorithm design`: since every vertex looks the same, "it does not matter where the computation starts" - the root of a broadcast, the source of a reduction, or the placement of a master process is irrelevant to structural cost
- `Lemma 9` tells us diameter = radius, so the worst-case communication delay is a property of the topology, not the source
- Vertex symmetry also implies regularity (Theorem 7(2)), which is a technological prerequisite: all routers have the same port count, so a single router component can be used throughout the machine

A non-vertex-symmetric topology (like a mesh) forces the algorithm designer to reason about "good" vs "bad" placements of processes, and the router hardware differs at the corners, edges, and interior.

### Summary

The Cartesian product is the fundamental construction operator for orthogonal topologies. Its commutativity and associativity (Theorem 5) permit free reassociation of product expressions, yielding both the definition (`Q_n = Q_1^n`, etc.) and the hierarchical-recursivity decompositions (`Q_6 = Q_3 × Q_3`) of hypercubes, meshes, and tori. Vertex symmetry (Definition 6) formalizes the "same from every vertex" intuition; it is preserved by Cartesian product (Theorem 7(1)) and implies regularity (Theorem 7(2)). For vertex-symmetric graphs, diameter equals radius (Lemma 9). Concrete applications include `Q_n` (`2^n × n!` automorphisms, Theorem 16), tori (translations, Theorem 22), and wrapped butterflies (which require a combined rotation-plus-translation argument since they are not pure Cartesian products, Theorem 25).

### Potential exam questions

1. State the formal definition of the Cartesian product of two graphs. Given small graphs `G_1` and `G_2`, draw `G_1 × G_2`.
2. State and prove (or sketch) Theorem 5: the Cartesian product is commutative and associative up to isomorphism. What isomorphism do you construct for commutativity?
3. What is the constructor of orthogonal direct topologies? Express the hypercube `Q_n`, mesh `M(z_1, ..., z_n)`, and torus `K(z_1, ..., z_n)` as Cartesian products.
4. Define vertex symmetry. Why is it a desirable property for an interconnection network?
5. State Theorem 7. Give the constructive proof that if `G_1` and `G_2` are vertex-symmetric then `G_1 × G_2` is vertex-symmetric - specifically, exhibit the automorphism that maps `[u_1, u_2]` to `[v_1, v_2]`.
6. Prove that vertex symmetry implies regularity. Does the converse hold? If not, give an intuitive counterexample.
7. State and prove Lemma 9: in a vertex-symmetric graph, `diam(G) = r(G)`. Why is this practically important for an INPC?
8. State Theorem 16: how many automorphisms does `Q_n` have, and how are they constructed? Describe the two independent families of automorphisms `κ(π)` and `τ_{u,v}`.
9. Prove Corollary 17: for any `u, v ∈ V(Q_n)`, there are `n!` automorphisms mapping `u` to `v`. Write down the explicit formula.
10. Prove Theorem 21: the 1-D torus `K(z)` is vertex-symmetric. Show that the cyclic shift `f_{a,b}(x) = x ⊕_z (b ⊖_z a)` is an automorphism.
11. Derive Theorem 22 (vertex symmetry of the `n`-D torus) as a corollary of Theorems 5, 7, and 21.
12. Why is the mesh `M(z_1, ..., z_n)` not vertex-symmetric, whereas the torus `K(z_1, ..., z_n)` is? Which property from Theorem 7 directly rules it out for the mesh?
13. The wrapped butterfly `wBF_n` is vertex-symmetric (Theorem 25), but the argument does not follow from Theorem 7. Why not, and how must the automorphism be constructed instead?