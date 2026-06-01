### Scope of the question

Multicast (MC) is a selective broadcast: instead of delivering a packet to every node in the network, the source delivers it only to a predefined subset $M \subset V(G) - {s}$ of nodes. In MPI, the user defines such a subset via an MPI communicator. Multicast is considerably more complex than OAB. The standard OAB algorithm is inefficient when $|M| \ll |V(G)|$ because it wastes communication on uninvolved nodes. On the other hand, no exact optimal algorithms exist for multicast in general graphs - in computer networks, heuristics are used (similar to those from protocol literature). The good news, and the focus of this question, is that an optimal solution does exist for 1-port WH 2D meshes, based on a clever virtualization technique using ascending dimension-ordered chains and XY routing.

### Lemma 5: lower bounds for $k$-port WH multicast

For a $k$-port network $G$ with WH switching and a multicast of a packet of size $\mu$ from source $s \in V(G) - M$ to a target set $M \subset V(G)$: $$ \eta^{WH}_{MC,k}(G, s) = |M| $$ $$ \rho^{WH}_{MC,k}(G, s) = \lceil \log_{k+1}(|M| + 1) \rceil $$ $$ \gamma^{WH}_{MC,k}(G, s) = \mathrm{exc}(s, G, M) $$ $$ \tau^{WH}_{MC,k}(G, \mu, s) = \rho^{WH}_{MC,k}(G, s)(t_s + \mu t_m) + \gamma^{WH}_{MC,k}(G, s) t_d $$ The structure is exactly like the WH OAB lower bound from Lemma 4, but with two adjustments:

- the work bound counts only the $|M|$ targeted receivers, not $|V(G)| - 1$
- the fan-out bound has $|M| + 1$ instead of $|V(G)|$ inside the logarithm, because including the source we need to reach $|M| + 1$ "informed" entities (source plus targets)
- the eccentricity $\mathrm{exc}(s, G, M)$ is the eccentricity of $s$ restricted to the target set $M$: the maximum distance from $s$ to any node in $M$, not to any node in the whole network The latency formula combines the per-round cost $(t_s + \mu t_m)$ paid $\rho$ times with the cumulative distance term $\gamma t_d$ from the WH cutting-through phase. The key takeaway: when $|M| \ll |V(G)|$, both $\rho$ and $\gamma$ can be much smaller than the OAB lower bounds, so an MC algorithm tuned to $M$ can be dramatically faster than running OAB and discarding the surplus.

> The fundamental MC question is: how do we exploit the structure of $M$ inside the network without wasting communication on the rest of $V(G)$? In 1-port WH 2D meshes, the answer is to project $M$ into a virtual 1D chain via lexicographic ordering, and reuse the 1D mesh RDA.

### Ascending dimension-ordered chain (ADOC)

Consider a 2D mesh $M(z_x, z_y)$ with XY routing (horizontal coordinate traversed first, then vertical) and node coordinates in $[X, Y]$ format. An ascending dimension-ordered chain (ADOC) is any sequence of lexicographically ordered nodes in the mesh. Coordinates are compared as two-digit "numbers": first by $X$, then by $Y$ to break ties. For example: $$ [0, 3] < [1, 1] < [1, 3] < [2, 4] < [3, 0] $$ The ADOC orders nodes column-wise (top down, left to right), turning any subset of the 2D mesh into a linear sequence. This is the projection that lets the 1D algorithm be reused.

### Lemma 6: link-disjointness of paths in an ADOC under XY routing

The ADOC ordering combined with XY routing has a remarkable property: for almost all pairings of ordered nodes, the paths between them are link-disjoint. Let $u < v < w < z$ be an ADOC in $M(z_x, z_y)$. Then:

- Case 1: paths $u \to v$ and $w \to z$ are link-disjoint
- Case 2: paths $v \to u$ and $z \to w$ are link-disjoint
- Case 3: paths $v \to u$ and $w \to z$ are link-disjoint
- Case 4: paths $u \to v$ and $u \to z$ are not link-disjoint; $u \to z$ has priority
- Case 5: paths $u \to z$ and $v \to w$ are not link-disjoint; $u \to z$ has priority The pattern is intuitive: as long as the two source-destination pairs are "nested or sequential" in the ADOC order, XY routing gives link-disjoint paths. Collisions occur only when one pair "contains" another (cases 4 and 5), and in both bad cases the longer-reaching path $u \to z$ takes priority. These bad cases must be scheduled in separate rounds, but the multicast algorithm avoids them by design. The proof of case 1 (the others are symmetric) proceeds by case analysis on how the four ADOC-ordered nodes can be placed in the 2D mesh. Traversing column-wise top down, left to right, they always appear in the order $u, v, w, z$. There are only a few placement types over at most 3 joint columns (figures (a)-(g) on slide 30), and in none of them does the collision occur. The full-duplexity of the links is doing important work in this argument: when paths look close to each other in the 2D drawing, the opposite-direction usage of the shared physical link prevents conflict.

### The optimal MC algorithm: virtual 1D RDA on the ADOC

Lemma 6 enables a beautiful reduction. The algorithm treats the ADOC of the target set $M$ (with the source $s$ inserted at its lexicographic position) as if it were a virtual 1D mesh, and applies the 1D mesh recursive doubling algorithm to it:

```
Algorithm MC(M(z_x, z_y), s, M):
(1) Form the ADOC of M union {s}, sorted lexicographically.
(2) Split the ADOC into lower and upper halves.
(3) If the source s is in the lower half, send the packet to the
    first node of the upper half.
    Otherwise, send it to the last node of the lower half.
(4) Repeat recursively for both halves in parallel.
```

The crucial observation is that the recursive doubling on the virtual 1D mesh only ever uses cases 1, 2, 3 of Lemma 6 (the safe pairwise communications). This is because at each level of recursion, the algorithm partitions the chain into disjoint halves and only communicates across the boundary; the source-destination pairs at the same recursion level are always sequential in the ADOC order, never nested. Therefore XY routing in the real 2D mesh delivers all these messages contention-free. The slide example shows an ADOC of 8 nodes (including the source) being broadcast to in 3 rounds.

### Why this is optimal

The number of rounds matches the 1-port WH multicast lower bound: $$ r_{MC}(M(z_x, z_y), s, M) = \lceil \log_2 (|M| + 1) \rceil = \rho^{WH}_{MC,1}(M(z_x, z_y), s) $$ because each recursive doubling step doubles the number of informed nodes (in the virtual 1D mesh), and we start from one informed node (the source) and end with $|M| + 1$ informed nodes (source plus all targets). The traffic bound is also achieved in the sense that all the long-distance hops happen in parallel during the WH cutting-through phase, and the cumulative distance is bounded by the eccentricity of $s$ in $M$.

### Latency

Applying the general WH multicast latency formula with $k = 1$: $$ t_{MC}(M(z_x, z_y), \mu, s) = \lceil \log_2 (|M| + 1) \rceil (t_s + \mu t_m) + \mathrm{exc}(s, M(z_x, z_y), M) t_d $$ The interpretation:

- $\lceil \log_2 (|M| + 1) \rceil$ rounds, each paying one startup and one full packet transmission
- the cumulative cutting-through cost is the eccentricity of $s$ restricted to the target set $M$, paid once For small $|M|$ (sparse multicast) both terms are small. The dependence on $|V(G)|$ is only through the eccentricity, not through the round count, which is exactly the property that makes this much better than running OAB and discarding the unwanted deliveries.

### What makes this work

Three structural pieces fit together to give an optimal algorithm where none existed for general graphs:

- the 2D mesh's XY routing imposes a canonical path between any two nodes, removing the routing ambiguity
- the lexicographic order on coordinates produces a total order under which XY-routed paths between consecutive elements stay link-disjoint (Lemma 6)
- the resulting virtual 1D structure admits the 1-port RDA from the 1D mesh case (the algorithm from question 34), which is already round-optimal The XY routing constraint, often viewed as a deadlock-freedom mechanism, here serves a second purpose: it provides exactly the structural rigidity needed to translate the lexicographic order into contention-free communication.

### Potential exam questions

- Define the multicast (MC) operation. Why is it considerably more complex than OAB, and why is an OAB-based solution inefficient when $|M| \ll |V(G)|$?
- State Lemma 5 (the lower bounds for $k$-port WH multicast). Compare each bound term-by-term with the corresponding OAB lower bound from Lemma 4 and explain the differences.
- Define an ascending dimension-ordered chain (ADOC) in a 2D mesh with XY routing. Give an example with at least 5 nodes.
- State Lemma 6 (the link-disjointness lemma for ADOCs). List all 5 cases and identify which give link-disjoint paths and which do not. In the colliding cases, which path has priority?
- Prove case 1 of Lemma 6 (the case $u \to v$ and $w \to z$). Where in the proof does the full-duplexity of the links matter?
- Describe the optimal multicast algorithm for 1-port WH 2D meshes. Explain how it reduces the 2D problem to a virtual 1D problem via the ADOC.
- Prove that the recursive doubling on the virtual 1D ADOC only ever uses cases 1, 2, and 3 of Lemma 6, never cases 4 or 5. Why is this enough to ensure contention-free communication in the real 2D mesh?
- Derive the latency of the optimal multicast algorithm and show that it matches the lower bound from Lemma 5 for $k = 1$.
- Why does the algorithm need the XY routing assumption? What goes wrong if arbitrary minimal routing is allowed in the 2D mesh?
- Compare the latency of the optimal MC algorithm with the latency of running OAB and discarding the unwanted deliveries on a $K(z, z)$ torus with $|M| = O(\sqrt{|V|})$. Quantify the gap.
- Why is the ADOC defined column-wise top down, left to right (and not row-wise)? Is the choice arbitrary, given that XY routing traverses X first?