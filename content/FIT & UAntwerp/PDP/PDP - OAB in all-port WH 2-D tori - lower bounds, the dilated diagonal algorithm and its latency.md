### Scope of the question

This question asks about one-to-all broadcast (OAB) on a 2D torus $K(n, n)$ in the wormhole (WH) all-port model. Compared with the 1-port WH case from question 34, the all-port model is dramatically more demanding: each informed node has $2n$ ports (here $2n = 4$ in 2D) and must use all of them in parallel, in every round since it became informed, to reach $2n$ new uninformed nodes via pairwise link-disjoint paths. Achieving the logarithmic fan-out lower bound on all-port WH networks is therefore "much harder" than in the 1-port case. The canonical solution for 2D tori is the dilated diagonal algorithm.

### Lower bounds for all-port WH OAB

For an $n$-dimensional WH all-port mesh or torus with $N$ nodes, the rounds lower bound is: $$ \rho_{OAB}(G) = \lceil \log_{2n+1} N \rceil $$ This is a direct specialization of Lemma 4 from question 34 with $k = 2n$ ports (in an $n$-dimensional orthogonal topology each node has $2n$ neighbors, all of them usable in parallel in the all-port model). In 2D specifically: $$ \rho_{OAB}(K(n, n)) = \lceil \log_5 (n^2) \rceil $$ because each informed node can inform up to 4 new uninformed nodes per round, growing the informed set by a factor of 5. To achieve this bound, every node, once involved in the broadcasting, must in every subsequent round:

- find $2n$ uninformed nodes, and
- deliver the packet to them so that all $2n$ communication paths are pairwise link-disjoint The second condition is the hard part. In 2D this means quintupling the informed set each round (factor 5 = the original node plus 4 new ones), and $5^k$ grows fast. Combined with the WH blocking-prone nature - any link collision freezes the entire path - the route planning must be carefully designed. The general latency upper bound from Lemma 4 is: $$ \tau_{OAB}(G, \mu, s) = \rho_{OAB}(G)(t_s + \mu t_m) + \gamma_{OAB}(G, s) t_d $$ where in a vertex-symmetric topology like the torus, $\gamma$ is determined by the diameter.

> The hard part of all-port WH broadcast is constructing routes that grow the informed set by a factor of $2n + 1$ per round without any link collisions. In 2D tori the dilated diagonal algorithm is the canonical solution.

### Why tori have elegant solutions

Vertex symmetry of the torus is the key structural property that makes the construction tractable. Vertex symmetry means there is an automorphism mapping any vertex to any other, so the broadcast pattern from one source can be translated to any other source without changing its structure. In orthogonal tori the automorphism is just a coordinate translation, and the torus wrap-around makes all rows, columns, and diagonals "look the same". Two well-known constructions exploit this:

- 2-port 1D torus: recursive decomposition into thirds (each informed node sends to two new nodes, growing the informed set by a factor of 3 per round, reaching $\lceil \log_3 z \rceil$ rounds)
- 4-port 2D torus: recursive decomposition into fifths via the dilated diagonal algorithm The 2D algorithm achieves the lower bound $\lceil \log_5 (n^2) \rceil$ exactly when $n = 5^k$, and up to a small additive constant in general. The algorithm relies on XY routing (always traverse the X coordinate first, then the Y coordinate) - XY routing in a 2D torus is deadlock-free and is the standard assumption.

### The dilated diagonal algorithm: structure

Assume for simplicity a square torus $K(n, n)$. The algorithm proceeds in three phases:

```
Algorithm DilatedDiagonalOAB(K(n, n), s)
Phase 1 - Deliver 1 packet to each row (ceil(log_5 n) rounds):
  (a) Split K(n, n) into 5 horizontal strips of approximately equal width.
  (b) Send the packet from the source strip to all other 4 strips in 1 round
      using link-disjoint paths with XY routing.
  (c) Recurse in each horizontal strip.

Phase 2 - Align all packets rowwise to the main diagonal (1 round).

Phase 3 - Diagonal nodes inform all remaining nodes (ceil(log_5 n) rounds):
  (a) Split K(n, n) into 5 diagonal bands of approximately equal width
      so that the main diagonal is in the middle of the first band.
  (b) Each node on the main diagonal informs 4 nodes on middle diagonals
      of the other 4 bands so that all 4 paths are pairwise link-disjoint.
  (c) Recurse in each diagonal band.
```

The algorithm generalizes to arbitrary 2D tori (not just square or power-of-5) using dilated (zig-zag) diagonals, which is the source of the algorithm's name.

### Phase 1: filling one column with informed nodes

The torus is split into 5 horizontal strips of equal width. The source sits in one of these strips. In one round, the source sends the packet to 4 representative nodes in the other 4 strips, using XY-routed paths. Because the strips are stacked horizontally and the source uses different vertical directions to reach each of them, the four paths can be made link-disjoint with a careful choice of destinations. The recursion then applies the same procedure inside each strip, splitting it again into 5 sub-strips of equal width and broadcasting one packet to each. After $\lceil \log_5 n \rceil$ recursive rounds, every row of the torus contains exactly one informed node. The informed nodes after Phase 1 form an irregular pattern (one per row) rather than a clean column.

### Phase 2: aligning to the main diagonal

The single misaligned round of the entire algorithm. After Phase 1, each row contains one informed node at some column position determined by the recursion. Phase 2 performs a one-to-one shift within each row so that the informed node in row $i$ moves to column $i$ (or some fixed offset thereof), placing all informed nodes exactly on the main diagonal of the torus. This phase uses only one of the four available ports per informed node (a horizontal shift within the row), so it does not fully exploit the all-port capability. This is the source of the additive constant 1 that prevents the algorithm from exactly matching the lower bound in general.

### Phase 3: expanding from the diagonal to the whole torus

After Phase 2, every node on the main diagonal is informed. Phase 3 is structurally symmetric to Phase 1, but applied to diagonals rather than rows. The torus is split into 5 diagonal bands of approximately equal width, with the main diagonal in the middle of the first band. Because the torus wraps around in both dimensions, every diagonal in a 2D torus behaves like a "main diagonal" - this is again a consequence of vertex symmetry. The five diagonal bands are mutually disjoint, even though when drawn in the 2D plane they appear broken by the wrap-around (which is why the slide uses colors to highlight them). In each round, each yellow diagonal node sends to 4 nodes - one on the middle diagonal of each of the other 4 bands - using XY-routed paths. The XY routing constraint forces the paths to consist of one horizontal and one vertical leg, and the recursive splitting ensures that all 4 sends from each diagonal node are pairwise link-disjoint. The recursion then applies the same procedure inside each band. After $\lceil \log_5 n \rceil$ recursive rounds, every node in the torus is informed.

### Latency analysis

The total round count is: $$ r_{OAB}(K(n, n)) = \lceil \log_5 n \rceil + 1 + \lceil \log_5 n \rceil = 2 \lceil \log_5 n \rceil + 1 $$ This should be compared with the lower bound: $$ \rho_{OAB}(K(n, n)) = \lceil \log_5 (n^2) \rceil = \lceil 2 \log_5 n \rceil $$ The two match up to an additive constant of 1, which is the alignment round in Phase 2. For $n = 5^k$ the bound is matched exactly without the additive 1 because the recursion is clean. Using the general WH latency formula: $$ t_{OAB}(K(n, n), \mu) = r_{OAB}(K(n, n)) (t_s + \mu t_m) + \gamma_{OAB}(K(n, n)) t_d $$ where $\gamma_{OAB}(K(n, n)) = \mathrm{diam}(K(n, n)) = 2 \lfloor n/2 \rfloor$ in a vertex-symmetric torus. Substituting: $$ t_{OAB}(K(n, n), \mu) = (2 \lceil \log_5 n \rceil + 1)(t_s + \mu t_m) + 2 \lfloor n/2 \rfloor t_d $$ The interpretation:

- the $(2 \lceil \log_5 n \rceil + 1)$ factor is the round count, paid as one startup plus one packet transmission per round
- the $2 \lfloor n/2 \rfloor t_d$ term is the cumulative distance cost from the cutting-through phase, paid once across all rounds and equal to the diameter For short messages (small $\mu$), the dominant cost is the logarithmic startup term. For long messages, both terms grow but the $\mu t_m$ scales with rounds, not with diameter, which is exactly the WH advantage.

### Why XY routing matters

The algorithm uses XY routing (X coordinate first, then Y). This is essential for two reasons:

- XY routing is deadlock-free in 2D meshes and tori, so the WH blocking risk is controlled
- the link-disjointness arguments in Phases 1 and 3 rely on the fact that all paths follow this canonical decomposition; arbitrary routing would create unanalyzable collisions The combination "all-port + XY routing + recursive 5-way split" is what makes the algorithm provably correct.

### Generalization

The algorithm generalizes in two directions:

- Rectangular tori $K(n_1, n_2)$ with $n_1 \neq n_2$: use dilated (zig-zag) diagonals instead of true diagonals. The "main diagonal" becomes a zig-zag path that still cycles through all $n_1 n_2$ nodes due to the wrap-around. The 5-way split argument still applies.
- Higher-dimensional tori: the same recursive principle extends to factor $2n + 1$ splits in $n$ dimensions, but constructing the link-disjoint paths becomes considerably more delicate.

### Potential exam questions

- State the lower bound on the number of rounds for all-port WH OAB in an $n$-dimensional torus with $N$ nodes. Derive it from the general $k$-port fan-out bound.
- Why is achieving the all-port WH OAB lower bound much harder than achieving the 1-port WH OAB lower bound? What additional structural property must be satisfied in every round?
- State and explain the three phases of the dilated diagonal algorithm in a square torus $K(n, n)$.
- Why does Phase 2 (alignment to the main diagonal) prevent the algorithm from exactly achieving the lower bound in general? In which special case is the lower bound matched exactly without the additive 1?
- Derive the latency $t_{OAB}(K(n, n), \mu)$ of the dilated diagonal algorithm. Identify the two terms and their physical meaning.
- Why does the dilated diagonal algorithm use XY routing? What would go wrong with arbitrary minimal routing?
- Explain why the 5 diagonal bands in Phase 3 are mutually disjoint despite appearing broken when drawn in the 2D plane. Which property of the torus is responsible?
- How does the algorithm extend to rectangular (non-square) 2D tori? Define dilated diagonals.
- Compare the round complexity of all-port WH OAB on $K(n, n)$ ($N = n^2$ nodes) with all-port SF OAB on the same topology. Quantify the gap.
- The lower bound for all-port WH 2D torus is $\lceil \log_5 N \rceil$. Show that the dilated diagonal algorithm achieves $2 \lceil \log_5 n \rceil + 1$ rounds and explain why this matches the bound up to an additive constant.
- Why do tori "have elegant solutions" for all-port WH OAB while general graphs do not? What role does vertex symmetry play in the construction?