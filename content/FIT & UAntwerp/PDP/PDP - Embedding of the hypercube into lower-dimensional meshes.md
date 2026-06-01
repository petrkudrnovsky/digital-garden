
> [!tldr] First 5 minutes of hell
> This is for the cases, where we have an algorithm running on a hypercube and in physical world, we have a mesh topology, so we need to embed the hypercube into the mesh. 
> 
> Dilation = the stretch of the edges. If two vertices are neighbors and then they end up far away, the dilation is high. We want to minimize it, low dilation means faster communication. 
> 
> For simplicity, we will do $Q_{2k} \overset{\text{emb}}{\longrightarrow} M(2^k, 2^k)$
> - there are $2^{2^k}$ vertices in both hypercube and the mesh (so it will be mapping of one node to one node (= load is 1))
> - the theoretical lower bound of the dilation is $2^k/k$
> - the mapping will be mapping of $2k$-bit addresses of the hypercube to the 2D mesh nodes, there are different mapping functions (we want the one with the smallest dilation)
> 
> Svoboda
> - map the upper half to x-axis of the mesh and the lower half to the y-axis of the mesh
> - e.g. 0110 -> 01 = 1; 10 = 2 => will be mapped to (1,2) in the mesh
> - dilation is $2^{k-1}$ - the worst enlargement of the hypercube edge
> 	- if we flip in the highest bit (in hypercube), we have flipped in the highest dimension (which is the furthest in the mesh)
> 	- flipping the $k-1$ bit of the upper half changes the y-axis location of $2^{k-1}$
> 
> Morton curve
> - individual coordinates of the hypercube are mapped recursively and alternately (odd-positions for x-axis, even-positions for the y-axis)
> - e.g. 1010 -> 11 = 3; 00 = 0 => will be mapped to (3, 0)
> - dilation is $2^{k-1}$ (same as Svoboda, but the recursive nesting better preserves the structure of the hypercube) so on average, the dilation is better
> 
> Karnaugh
> - is worse, the Gray code is optimized for something else
> - dilation is $2^{k}$

# Motivation

A great many parallel algorithms are designed for the hypercube topology because of its rich structure (logarithmic diameter, recursive decomposition, perfect symmetry). In practice however, the target hardware often has a 2-D (or higher-dimensional) **mesh** or **torus** topology. To run a hypercube algorithm on mesh hardware we need an **embedding** of the hypercube into the mesh - and we want it to be as good as possible in terms of dilation.

For simplicity, the lecture restricts attention to the embedding $$ Q_{2k} \overset{\text{emb}}{\longrightarrow} M(2^k, 2^k) $$ with $\text{load} = 1$, where $k \geq 2$ is an integer. Everything that follows generalises to hypercubes with odd dimension and to multidimensional meshes and tori.

# Lower bound on dilation

### Diameter argument (Theorem 4)

If $|V(G)| = |V(H)|$ and $\text{load}(\varphi, \xi) = 1$, then $$ \text{dil}(\varphi, \xi) \geq \left\lceil \frac{\text{diam}(H)}{\text{diam}(G)} \right\rceil. $$

### Corollary 7

For any embedding $(\varphi, \xi) = Q_{2k} \overset{\text{emb}}{\longrightarrow} M(2^k, 2^k)$ with $\text{load} = 1$: $$ \text{dil}(\varphi, \xi) \geq \frac{2^{k+1}}{2k} = \frac{2^k}{k}. $$

##### Derivation

The hypercube $Q_{2k}$ has $\text{diam}(Q_{2k}) = 2k$ (number of bits). The mesh $M(2^k, 2^k)$ has $\text{diam}(M(2^k, 2^k)) = 2(2^k - 1) \approx 2^{k+1}$ (the two Manhattan-distance traversals of a side of length $2^k - 1$). The ratio is $\lceil 2^{k+1} / (2k) \rceil = 2^k / k$.

This is asymptotically much larger than $1$, so no $\text{dil} = 1$ embedding can exist, regardless of how clever the mapping is. The question becomes: which simple, systematic mapping gets closest to this lower bound?

# General form of any such embedding

Every embedding $Q_{2k} \overset{\text{emb}}{\longrightarrow} M(2^k, 2^k)$ with $\text{load} = 1$ is a **bijection** of $2k$-bit hypercube addresses to mesh node coordinates: $$ \varphi(b_{2k-1} b_{2k-2} \ldots b_0) = [x_{k-1} \ldots x_0, ; y_{k-1} \ldots y_0]. $$ There are many such bijections. Three of them are simple and worth comparing: Svoboda's map, the Morton curve, and Karnaugh's map. The first two come from Boolean algebra ("map of a logical function") and so does the third.

# Svoboda's map: lexicographic indexing

##### Definition

The high $k$ bits give the $x$ coordinate, the low $k$ bits give the $y$ coordinate: $$ \varphi(b_{2k-1} b_{2k-2} \ldots b_0) = [b_{2k-1} \ldots b_k, ; b_{k-1} \ldots b_0]. $$ Equivalently: lexicographic row-wise / column-wise mapping. Indexing is **lexicographic** in both $x$ and $y$.

##### Dilation - Lemma 10

Embedding based on Svoboda's map has the same overall dilation as the Morton curve embedding: $2^{k-1}$.

##### Assessment

This is the "natural first attempt". It is simple but not the best regular solution for parallel computing, because its **local proximity** is inferior to Morton's: two hypercube-adjacent vertices differing in a low bit can land far apart in the mesh under Svoboda, since adjacent low-bit changes only affect the $y$ coordinate while leaving $x$ untouched, but the bigger structural locality is broken across the high-low boundary.

# Morton curve: recursive alternating indexing

##### Definition

Individual hypercubic coordinates are mapped recursively, alternating between $x$ and $y$ directions. Odd-indexed bits form the $x$ coordinate, even-indexed bits form the $y$ coordinate: $$ \varphi(b_{2k-1} b_{2k-2} \ldots b_0) = [b_{2k-1} b_{2k-3} \ldots b_1, ; b_{2k-2} b_{2k-4} \ldots b_0]. $$

##### Recursive interpretation

The hypercube dimensions are alternated in lexicographic order between $x$ and $y$: $X, Y, X, Y, \ldots$. Each recursion step places the four $Q_{n-2}$ subcubes of $Q_n$ into the four quadrants of the current square, then applies the same rule inside each quadrant. The four subcubes $Q_{n-2}$ of $Q_n$ are mapped into the 2-D mesh recursively in the so-called **Z-order fractal**.

##### Lemma 8 (Morton-curve distance)

In an embedding based on the Morton curve, the distance of images of two hypercube vertices differing in bit $i$, $0 \leq i \leq 2k-1$, is $$ \text{dist}_{M(2^k, 2^k)} \bigl( \varphi(u), ; \varphi(u \text{ XOR } 2^i) \bigr) = 2^{\lfloor i/2 \rfloor}. $$

##### Corollary 9 (Morton overall dilation)

The overall dilation of the embedding $Q_{2k} \overset{\text{emb}}{\longrightarrow} M(2^k, 2^k)$ based on the Morton curve is $$ 2^{\lfloor (2k-1)/2 \rfloor} = 2^{k-1}. $$

##### Assessment

Same worst-case dilation as Svoboda ($2^{k-1}$), but uniformly better average proximity. Vertices whose binary indices differ only in low bits are close in the 2-D layout because what is close in a lower-dimensional subcube stays close in the relevant quadrant recursively. The same construction is used in distributed databases to map Boolean coordinate spaces onto distributed nodes - the locality preservation is what makes both applications work.

# Karnaugh's map: Gray-code indexing

##### Definition

The hypercube address bits are split into $x$ and $y$ halves as in Svoboda's map, but the indexing along each axis uses **Gray code** instead of plain binary.

##### Dilation

Embedding $Q_{2k} \overset{\text{emb}}{\longrightarrow} M(2^k, 2^k)$ based on Karnaugh's map has dilation $2^k$, i.e. it is **worse by a factor of 2** compared to Svoboda and Morton.

##### Why it loses

Karnaugh's map is excellent for Boolean function minimisation because Gray indexing makes physically adjacent cells differ in exactly one bit. But this property scrambles hypercube-adjacency in the mesh layout: two hypercube-adjacent vertices (differing in one bit) may end up far apart under Gray-coded coordinates. The objective for process placement is **Manhattan-distance proximity** for hypercube-adjacent vertices, not visual readability for Boolean minimisation.

# Comparison summary

##### Worst-case dilation

- Lower bound (Corollary 7): $2^k / k$
- Svoboda's map: $2^{k-1}$
- Morton curve: $2^{k-1}$
- Karnaugh's map: $2^k$ (twice as bad)

Note that $2^{k-1}$ is still asymptotically larger than the lower bound $2^k / k$ by a factor of $k/2$, so none of these simple embeddings is optimal in absolute terms - but Svoboda and Morton are close enough to be considered acceptable in practice.

##### Local proximity (informal)

- Morton curve: best - preserves lexicographic proximity of hypercube vertices via the Z-order fractal
- Svoboda's map: worse - the high-low bit split breaks proximity across the boundary
- Karnaugh's map: worst - Gray coding actively breaks hypercube-adjacency in the mesh layout

##### When to use which

- For **parallel computation** on mesh hardware: use the **Morton curve** for best average performance.
- For **Boolean function minimisation** by hand or visual inspection: use **Karnaugh's map**.
- For **simple, easy-to-compute** addressing: use **Svoboda's map** - same worst-case dilation as Morton, no recursive computation.

# Generalisations

All of the above can be generalised to:

- hypercubes with **odd dimension** $Q_{2k+1}$ (asymmetric split: $k+1$ bits in one direction, $k$ in the other),
- **multidimensional meshes and tori** $M(2^{k_1}, \ldots, 2^{k_d})$ (split the $\sum k_i = n$ bits among $d$ coordinate groups, applying Morton-style alternation across $d$ directions instead of 2).

The lecturer chose the even-dimension square mesh case because it is the cleanest to present.

# Potential exam questions

Given the lecturer's proof-heavy, definition-precise style, expect questions like:

1. State the diameter argument (Theorem 4) and use it to derive the lower bound $\text{dil} \geq 2^k / k$ for $Q_{2k} \overset{\text{emb}}{\longrightarrow} M(2^k, 2^k)$ with $\text{load} = 1$ (Corollary 7).
2. Define Svoboda's map as a bijection on bit addresses. What is its dilation?
3. Define the Morton-curve embedding as a bijection on bit addresses. Describe its recursive structure and explain why it is called the Z-order fractal.
4. State Lemma 8 (Morton-curve distance) and derive Corollary 9 (overall dilation $2^{k-1}$).
5. Why does the Morton curve preserve local proximity better than Svoboda's map, even though both have the same worst-case dilation?
6. What is the dilation of the Karnaugh-map embedding and why is it worse than Svoboda and Morton?
7. Compare the three embeddings (Svoboda, Morton, Karnaugh) along the dimensions of worst-case dilation, local proximity, and ease of computation.
8. None of the three embeddings achieves the lower bound $2^k / k$. By what factor do Svoboda and Morton miss the bound? Is it known whether a better systematic embedding exists?
9. How does the Morton-curve construction generalise to (a) hypercubes of odd dimension, (b) multidimensional meshes $M(2^{k_1}, \ldots, 2^{k_d})$ with $d > 2$?
10. For $k = 2$ (so $Q_4 \overset{\text{emb}}{\longrightarrow} M(4, 4)$), explicitly write down the Morton-curve image of every hypercube vertex and verify Lemma 8 for at least one pair of adjacent vertices.