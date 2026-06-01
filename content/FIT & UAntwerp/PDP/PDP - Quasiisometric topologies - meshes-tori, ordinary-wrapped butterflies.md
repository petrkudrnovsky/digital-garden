
> [!tldr] First 5 minutes of hell
> Definition of the Embedding:
> - place vertices of $G$ on vertices of $H$ and route edges of $G$ along paths(!) in $H$ (edge from $G$ is mapped on some path (multiple edges) of $H$)
> - how to measure the quality of the embedding?
> 	- maximal load of the host node (how many $G$ vertices are mapped to a single $H$-vertex)
> 	- maximal dilation (how long the longest edge-route is)
> 	- maximal edge congestion (how many $G$-edge routes pass through a single $H$-edge)
> 
> $G$ and $H$ are quasiisometric if we can embed one to the other with constant overhead 
> - one can simulate the other with a constant slowdown (one parallel step in one takes a constant number of parallel steps in the other)
> - **they are computationally equivalent** if they can simulate the other with constant slowdown
> - lemma: if they are quasiisometric, then they are computationally equivalent (not the other way!)
> 
> Theorem: meshes and tori are quasiisometric and therefore computationally equivalent. 
> - trivially, mesh is a subset of the torus
> 	- load = 1, dilation = 1, congestion = 1
> - the other way: with Cartesian product decomposition, embedding the 1D torus onto the 1D mesh (where load = 1 and dilation = congestion = 2) and building it back again (using the orthogonality and Cartesian product)
> 	- the cost of "faking" the wrap-around edges must be constant
> 	- zig-zag method combining the even and odd edges 
> 
> The ordinary butterfly and the wrapped butterfly are quasiisometric.
> - trivially, we can just merge the end-vertices of the ordinary butterfly (load = 2, dilation = 1) and get the cycles of the wrapped butterfly
> - the other way: a complicated constructive proof

# Conceptual foundation

### Quasiisometry and computational equivalence

Quasiisometry is a static, graph-theoretic equivalence between two interconnection networks based on the existence of mutual embeddings with bounded quality measures.

##### Definition 2 (Quasiisometric and computationally equivalent networks)

- $G$ and $H$ are **quasiisometric** if $G \overset{\text{emb}}{\longrightarrow} H$ and $H \overset{\text{emb}}{\longrightarrow} G$ both exist with **constant** embedding measures (load, expansion, dilation, congestion all bounded by constants independent of graph size).
- $H$ **simulates** $G$ with **slowdown** $h$ if one parallel step on $G$ can be simulated in $O(h)$ parallel steps on $H$.
- $G$ and $H$ are **computationally equivalent networks** if each can simulate the other with constant slowdown.

##### Lemma 3

Quasiisometric $G$ and $H$ are computationally equivalent, but not vice versa.

##### Why quasiisometry matters

Static embedding measures cannot capture dynamic behaviour of a parallel algorithm on the host network (e.g. a large dilation is not a problem if the corresponding route is used only scarcely). Quasiisometry is however robust with respect to any dynamic behaviour: if $G$ and $H$ are quasiisometric, then the asymptotic behaviour of any parallel algorithm differs by at most a constant multiplicative factor between the two networks. This is the strongest static argument for treating two topologies as "the same" for the purpose of parallel computation.

# Result 1: Meshes and tori are quasiisometric

### Theorem 6

Let $M = M(z_1, \ldots, z_n)$ be the $n$-dimensional mesh and $K = K(z_1, \ldots, z_n)$ the $n$-dimensional torus of identical dimensions. Then $M$ and $K$ are **quasiisometric** and therefore **computationally equivalent**.

### Proof - the easy direction

$M \subset K$ trivially (the torus has all the mesh edges plus the wraparound edges), so $K$ simulates $M$ with no slowdown. The identity embedding $M \overset{\text{emb}}{\longrightarrow} K$ has $\text{load} = 1$, $\text{dil} = 1$, $\text{ecng} = 1$.

### Proof - the hard direction: $K \overset{\text{emb}}{\longrightarrow} M$ with $\text{load} = 1$, $\text{dil} = \text{ecng} = 2$

The proof uses **Cartesian product decomposition**, exploiting the orthogonality of meshes and tori:

1. Decompose $M = M(z_1) \times \ldots \times M(z_n)$ and $K = K(z_1) \times \ldots \times K(z_n)$.
2. Embed each 1-D factor $K(z_i) \overset{\text{emb}}{\longrightarrow} M(z_i)$ with $\text{load} = 1$ and $\text{dil} = \text{ecng} = 2$.
3. Apply the Cartesian product to combine the per-dimension embeddings.

##### The 1-D zig-zag embedding $K(z) \overset{\text{emb}}{\longrightarrow} M(z)$

Number the $z$ vertices $0, 1, \ldots, z-1$. Place the torus vertices in the mesh in the **zig-zag order**: $$ 0 \to 2 \to 4 \to \ldots \to (z-1 \text{ or } z-2) \to \ldots \to 5 \to 3 \to 1 $$ i.e. hop over by 2 going right along the even-indexed vertices, then return on the alternate (odd-indexed) vertices. Every torus edge then maps to a mesh path of length at most 2, and every mesh link carries at most 2 torus-edge paths. The argument is identical for even and odd $z$.

##### Why orthogonality is key

In orthogonal topologies, embedding moves along one dimension have no effect on the others. The Cartesian product preserves the per-dimension bounds: the overall embedding still has $\text{load} = 1$, $\text{dil} = 2$, $\text{ecng} = 2$, regardless of the number $n$ of dimensions.

### Consequence for MPI

This is the theoretical justification for the `MPI_Cart_Create` design decision that lets the programmer freely declare each dimension as either a 1-D mesh or a 1-D torus: computationally, the choice does not matter, only constant factors.

# Result 2: Ordinary and wrapped butterflies are quasiisometric

### Lemma 11

The ordinary butterfly $oBF_n$ and the wrapped butterfly $wBF_n$ are **quasiisometric**.

### Proof - the easy direction: $oBF_n \overset{\text{emb}}{\longrightarrow} wBF_n$

This direction is trivial: merge the terminal vertices of each row of $oBF_n$ to obtain the row-cycles of $wBF_n$. This yields $\text{load} = 2$ and $\text{dil} = 1$.

### Proof - the hard direction: $wBF_n \overset{\text{emb}}{\longrightarrow} oBF_n$ with $\text{load} = 1$, $\text{dil} = 3$

This case is more involved than the mesh-torus case because the butterfly is **not orthogonal**: the column index of a butterfly fixes which hypercubic dimension is exercised at that stage, so permuting columns simultaneously permutes the hypercube dimensions. Each row of $wBF_n$ is a 1-D torus $K(n)$, and the cylinder structure means the first and last drawn columns are physically identical.

##### Step 1: Canonical path in $wBF_n$

By vertex symmetry of $wBF_n$, it does not matter which path we pick. Choose path $P$ from $u = (0, 11\ldots 11)$ to $v = (0, 00\ldots 00)$ traversing all $n$ stages, with bits of the row address inverted in the order $0, 1, \ldots, n-1$.

After embedding $wBF_n$ into $oBF_n$, every edge of $P$ must have dilation at most 3, even though the embedded walk has to detour through the last column of $oBF_n$ and return.

##### Step 2: Why the idempotent mapping fails

The naive **idempotent mapping** maps each column of $wBF_n$ to the same-indexed column of $oBF_n$. The last edge of $P$ would then have dilation $n + 1$ (i.e. logarithmic in the number of rows), because we must visit $oBF_n$'s last column $n$ and return to column $0$. This concentrates the entire wraparound cost into a single edge of length $\Theta(\log\text{size})$, violating the constant-dilation goal of quasiisometry. The fix has to spread that cost out so every step carries at most a constant share.

##### Step 3: Reformulating as a walk problem in $oBF_n$

We need a walk from $u = (0, 11\ldots 11)$ to $v = (0, 00\ldots 00)$ in $oBF_n$ such that:

1. we visit each column $1, \ldots, n-1$ exactly once,
2. column $n$ is just transient,
3. the distance between two neighbours on the walk is at most $3$.

This is analogous to the $K(n) \overset{\text{emb}}{\longrightarrow} M(n)$ zig-zag with $\text{dil} = 2$, but with two complications: we embed $K(n)$ into $M(n+1)$ instead of $M(n)$, and bit inversions are tightly coupled to column moves (not independent like in orthogonal topologies). Both endpoint column numbers and row addresses are fixed in advance.

##### Step 4: The bit-permutation construction

Each valid walk corresponds to a specific permutation of the $n$ bits in the row address giving the order in which they are inverted.

For **even $n$**, the two equivalent permutations are:

- (a) $1, 3, \ldots, n-3, n-1, n-2, \ldots, 0$
- (b) $0, 2, \ldots, n-2, n-1, n-3, \ldots, 1$

For **odd $n$**, the two equivalent permutations are:

- (a) $1, 3, \ldots, n-2, n-1, n-3, \ldots, 0$
- (b) $0, 2, \ldots, n-3, n-1, n-2, \ldots, 1$

Both possibilities are equivalent in terms of dilations: each yields exactly **1 edge of dilation 3**, **1 edge of dilation 1**, and the **rest have dilation 2**. For the running example $n = 4$, the chosen permutation is $1, 3, 2, 0$.

##### Step 5: Using row-symmetry of $wBF_n$

The key trick: instead of fighting the rigid column-to-dimension correspondence of butterflies, we exploit **row-symmetry** to relabel the source graph. A systematic permutation of bits in row addresses of $wBF_n$ is an automorphism (a bijection preserving adjacency), so we get the same $wBF_n$, just drawn differently. We rename all vertices of $wBF_n$ using this automorphism, so that idempotent column mapping into the destination is now valid.

##### Step 6: The final embedding

For $n = 4$ with permutation $1, 3, 2, 0$:

- column 1 of permuted $wBF_4$ maps to column 2 of $oBF_4$,
- column 2 of permuted $wBF_4$ maps to column 3 of $oBF_4$,
- column 3 of permuted $wBF_4$ maps to column 1 of $oBF_4$.

The per-edge dilations achieved along the embedded walk are lengths $2, 3, 2, 1$ in $oBF_4$, all bounded by the target constant $3$ independently of $n$. The construction yields $\text{load} = 1$ and $\text{dil} = 3$.

### Lessons learned about butterfly symmetry

The constructive proof reveals two structural lemmas about butterflies.

##### Lemma 12

$wBF_n$ has $n!$ automorphisms given by the permutations of bits in row addresses (with the standard layout).

##### Lemma 13

$oBF_n$ has $n!$ automorphisms given by the permutations of bits in row addresses.

Said otherwise, $oBF_n$ is **not vertex-symmetric**, but **row-symmetric**: for any two rows $r_1$ and $r_2$ of $oBF_n$, there is an automorphism sending $r_1$ to $r_2$. The row-symmetry is inherited from the hypercube on which the butterfly is built: the hypercube's dimension symmetry survives into the butterfly structure as a freedom to permute rows.

# Comparison of the two results

Both results establish quasiisometry between a "sparser" topology (mesh, ordinary butterfly) and its "richer" counterpart (torus, wrapped butterfly), with the easy direction being trivial inclusion and the hard direction requiring a constructive embedding that spreads a wraparound cost evenly across edges.

The mesh-torus proof is simple because meshes and tori are **orthogonal**: per-dimension embeddings combine cleanly via the Cartesian product. The butterfly proof is more involved because butterflies are **not orthogonal**: column index and hypercube dimension are tied together, so we cannot independently permute dimensions. The workaround is to exploit the rich row-symmetry of $wBF_n$ to relabel vertices first, making idempotent column mapping valid.

In both cases the achieved bounds are $\text{load} = 1$, $\text{dil} \in {2, 3}$, $\text{ecng}$ constant - small enough to be considered "constant overhead" for parallel computation.

# Potential exam questions

Given the lecturer's proof-heavy, definition-precise style, expect questions like:

1. Define quasiisometric networks. State Lemma 3 and explain why the converse (computational equivalence $\Rightarrow$ quasiisometry) does not hold.
2. Prove Theorem 6: meshes and tori of the same dimensions are quasiisometric. Give the explicit construction of $K(z) \overset{\text{emb}}{\longrightarrow} M(z)$ with $\text{dil} = 2$.
3. Why is the Cartesian product decomposition usable here? What property of meshes and tori makes the per-dimension argument compose?
4. State Lemma 11 and prove the easy direction $oBF_n \overset{\text{emb}}{\longrightarrow} wBF_n$. What are the embedding measures?
5. Sketch the proof of the hard direction $wBF_n \overset{\text{emb}}{\longrightarrow} oBF_n$ with $\text{load} = 1$ and $\text{dil} = 3$. Why does the idempotent mapping fail, and what would its dilation be?
6. For $n = 4$, give the bit-inversion permutation used in the construction and explain the corresponding mapping of columns of the permuted $wBF_4$ into columns of $oBF_4$.
7. State the two equivalent bit-inversion permutations for general even $n$ and odd $n$. How many edges of each dilation value does each permutation produce?
8. Why is the butterfly proof more difficult than the mesh-torus proof? What structural property is missing in butterflies?
9. State Lemma 12 and Lemma 13. What does "$oBF_n$ is row-symmetric but not vertex-symmetric" mean precisely, and how was this used in the embedding construction?
10. Compare and contrast the two quasiisometry proofs in this lecture. What is the common high-level strategy, and where does each proof exploit a specific structural feature of the topologies involved?