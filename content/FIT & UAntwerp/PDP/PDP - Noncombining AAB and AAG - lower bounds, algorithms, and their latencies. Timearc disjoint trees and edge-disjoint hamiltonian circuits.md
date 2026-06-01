### Scope of the question

This question is about all-to-all broadcast (AAB), which is functionally identical to all-to-all gather (AAG): every node holds one packet of size $\mu$ and at the end every node holds all $|V(G)|$ packets. The MPI call is `MPI_Allgather`. The question focuses on the noncombining model, in which every packet must be transferred individually from its source to each destination - packets travelling the same direction cannot be merged into a larger communication unit. Two algorithmic techniques are required: time-arc-disjoint trees (TADTs) and link-disjoint Hamiltonian circuits.

### The noncombining model and the inversion of bottlenecks

In the combining model (e.g. for OAS in question 37), packets travelling the same direction can be merged into one bigger unit and sent with a single startup latency. In the noncombining model, every packet is transferred individually, regardless of where it is going. This changes which side of the communication is the bottleneck:

- in OAB the sender is the bottleneck (it has one packet and pushes it out)
- in noncombining AAB the receiver is the bottleneck, because each piece must be received separately on a single port and cannot be bundled This inversion is the key insight behind the lower bound: it does not matter how clever the sending schedule is, every node must individually receive $|V(G)| - 1$ packets through at most $k$ ports per round.

### Lemma 8: lower bounds for noncombining AAB on $k$-port SF networks

For a $k$-port SF full-duplex noncombining network $G$: $$ \rho_{AAB,k}(G) = \left\lceil \frac{|V(G)| - 1}{k} \right\rceil $$ $$ \tau_{AAB,k}(G, \mu) = \rho_{AAB,k}(G)(t_s + \mu t_m) $$ Proof:

- $\rho_{AAB,k}(G)$: every node has to receive $|V(G)| - 1$ distinct packets and in one round can receive at most $k$ packets (one per input port). The bound follows.
- $\tau_{AAB,k}(G, \mu)$: one round cannot be smaller than the reception of one packet from a neighbor, i.e. $t_s + \mu t_m$. For $k = 1$ (1-port full-duplex), this gives $\rho_{AAB,1}(G) = |V(G)| - 1$ rounds. For 4-port 2D tori ($k = 4$), the bound is $\lceil (|V(G)| - 1)/4 \rceil$.

> The noncombining lower bound depends only on $|V(G)|$ and $k$, not on the diameter or any other structural property. This is because the bottleneck is local: each node has its receive capacity, and the bound is reached when every port of every node is used productively in every round.

### TADTs: setup and definition

The TADT framework constructs an optimal AAB algorithm in vertex-symmetric orthogonal topologies. The setup is:

- every node $u \in V(G)$ is the root of an OAB spanning tree $B(u)$
- all nodes $u$ start their broadcasts in their $B(u)$ simultaneously
- all broadcasts proceed synchronously in lock-step Definition 9 (Time-arc-disjoint trees):
- An arc of $B(u)$ is at level $i$ iff the broadcast packet traverses this arc in round $i$.
- The height $h(B(u))$ is the highest arc label in $B(u)$.
- Two trees $B(u)$ and $B(v)$ are mutually time-arc-disjoint (TADT) iff for every $i$ with $1 \leq i \leq \min(h(B(u)), h(B(v)))$, their sets of level-$i$ arcs are disjoint in $G$. Corollary 10:
- in round $i$, only the arcs at level $i$ are active in all the trees
- if all OAB trees are pairwise TADTs, the AAB packet transfer on full-duplex links is contention-free The intuition: each of the $|V(G)|$ trees has $|V(G)| - 1$ arcs, giving $|V(G)|(|V(G)| - 1)$ arc-uses across all rounds. Only in fully connected graphs could one activate all of them in a single round. The TADT discipline says: activate only the level-$i$ arcs from every tree in round $i$, and require those activations to be link-disjoint across the network. If this holds, the synchronized AAB is automatically contention-free.

### Lemma 11: TADTs for vertex-symmetric orthogonal topologies

In a vertex-symmetric orthogonal topology, all TADTs are isomorphic translations of a single generic spanning tree $B(_)$. The translation preserves arc time levels. So the problem reduces to designing one generic tree and translating it to each node. The lemma: any two translations of a generic $B(_)$ in a vertex-symmetric orthogonal topology $G$ are TADTs $\iff$ all the arcs at each level $i$ in $B(_)$ are of distinct directions. Proof by contradiction: suppose $B(_)$ has two arcs $e_1$ and $e_2$ at the same level $i$ and of the same direction. Consider any instance $B(u)$ and its translation $B_{e_1 \to e_2}(u)$ obtained by translating $B(u)$ by the vector that maps $e_1$ to $e_2$. By the definition of translation, $e_2$ of the original $B(u)$ is then mapped to the same physical link of $G$ as $e_1$ of the translated copy. In round $i$, both copies want to use that link, producing a collision. Conversely, if all arcs at each level have distinct directions, the translation argument cannot produce any collision, because translating an arc of one direction can never land it on top of an arc of a different direction at the same level. The practical corollary: if at every level (except possibly the last one) the generic tree $B(*)$ uses arcs of all distinct directions available in $G$, the resulting AAB is round-optimal.

### TADTs in 2D and 3D tori

In a 2D torus the 4 arc directions are N, E, W, S. The generic $B(*)$ must use one of each direction at every internal level. Square torus $K(z, z)$ with $z$ odd has a trivial elegant solution:

- $B(*)$ consists of 4 rotations of a 2D snake of size $(z^2 - 1)/4$
- each snake covers a quarter of the torus
- the four snakes tile the torus and the 4 rotations are pairwise of distinct directions at every level The size $(z^2 - 1)/4$ is the number of non-source nodes per quarter; the height of $B(_)$ is therefore $(z^2 - 1)/4$, matching the lower bound $(z^2 - 1)/4$ for $k = 4$. With odd $z$, division by 4 works cleanly. General rectangular $K(z_1, z_2)$ is more complicated but follows the same principle. For the 3D torus $K(z, z, z)$ with $z$ odd: the cube decomposes into 6 pyramids (one per face), and $B(_)$ is 6 rotations of a 3D snake covering one pyramid. The 6 directions ${+X, -X, +Y, -Y, +Z, -Z}$ are matched by the 6 pyramids of the cube decomposition. The TADT approach works on meshes as well as tori, despite meshes not being vertex-symmetric - the distinct-direction rule still applies, but the construction of $B(*)$ becomes harder than for tori.

### Link-disjoint Hamiltonian cycles: the alternative for 2D tori

An alternative AAB algorithm for 2D tori uses link-disjoint Hamiltonian cycles instead of TADTs.

```
Algorithm ParallelHamilCycleAAB(G):
(1) Construct two link-disjoint Hamiltonian cycles H_1 and H_2 in G.
(2) Each node i splits its packet p_i into half-packets p_{i,1} and p_{i,2}.
(3) Node i sends p_{i,1} along H_1 and p_{i,2} along H_2, in both directions.
(4) Every node i in every subsequent round:
    (4.1) receives and stores 4 half-packets;
    (4.2) forwards them along their Hamiltonian cycles;
    (4.3) if both half-packets p_{j,1} and p_{j,2} for some j have been received
          (they arrive in different rounds since the distance of i and j along H_1
          differs from their distance along H_2), they are merged back into p_j.
```

The construction depends on the existence of two link-disjoint Hamiltonian cycles. For 2D tori this is well-known: any $K(z_1, z_2)$ admits an edge-disjoint decomposition into two Hamiltonian cycles, because the torus has degree 4 and each cycle uses degree 2 at every node. The reason for splitting the packet into halves: each half-packet travels at most half a Hamiltonian cycle (i.e., $|V|/2$ hops) before reaching every node along that cycle. Sending in both directions along each cycle gives each half-packet a path length of at most $|V|/4$. So a packet of total size $\mu$ is transmitted as two half-packets of size $\mu/2$, each traveling along $|V|/2$ rounds.

### Lemma 12: latency of the Hamiltonian-cycle AAB

For a 2D torus $K(z_1, z_2)$: $$ r_{AAB}(K(z_1, z_2)) \approx \frac{z_1 z_2}{2} $$ (each half-packet travels at most one half of a cycle, in both directions; the factor 2 comes from the bidirectional traversal) $$ t_{AAB}(K(z_1, z_2), \mu) = r_{AAB}(K(z_1, z_2)) \left( t_s + \frac{\mu}{2} t_m \right) \approx \frac{z_1 z_2}{2} t_s + \frac{z_1 z_2}{4} \mu t_m $$ The data-volume term $\frac{z_1 z_2}{4} \mu t_m$ matches what the TADT approach achieves: each half-packet, of size $\mu/2$, traverses half a cycle. In the TADT solution, each tree of $B(*)$ has depth $(z_1 z_2 - 1)/4$ (a quarter of total nodes for 4-port 2D tori), and the packet size is $\mu$, giving $(z_1 z_2 - 1)/4 \cdot \mu t_m \approx z_1 z_2/4 \cdot \mu t_m$. So the message-volume contributions are the same.

### TADTs vs Hamiltonian cycles: comparison

The two approaches have the same data-volume complexity but different round complexities:

- TADT solution: round count $(z_1 z_2 - 1)/4$, round-optimal
- Hamiltonian-cycle solution: round count $z_1 z_2 / 2$, not round-optimal (off by a factor of 2) For large $\mu$, the $\mu t_m$ term dominates and the two approaches perform similarly. For small $\mu$, the startup-dominated round count makes TADT the clear winner (twice as fast). Generality:
- TADT works on both 2D tori and 2D meshes
- Hamiltonian-cycle decomposition exists only for graphs of even degree, so it works on tori (degree 4) but not on meshes (boundary nodes have lower degree). Therefore the Hamiltonian-cycle approach is not applicable to 2D meshes. A multi-dimensional generalization of the Hamiltonian-cycle approach exists: an $n$-dimensional torus has even degree $2n$ and theoretically decomposes into $n$ pairwise edge-disjoint Hamiltonian cycles. Each packet is then split into $n$ pieces of size $\mu/n$, each sent along its own cycle in both directions. However, the algorithmic complexity of constructing $n$ pairwise link-disjoint Hamiltonian cycles increases significantly with $n$.

> The TADT and link-disjoint Hamiltonian-cycle solutions achieve the same data-volume complexity, but TADT is round-optimal while the Hamiltonian-cycle approach is not. TADT also works on meshes; Hamiltonian cycles do not.

### Potential exam questions

- Define AAB and AAG and explain why they are functionally identical. What is the corresponding MPI call?
- Define the noncombining model. How does it differ from the combining model in terms of which side of the communication becomes the bottleneck?
- State and prove Lemma 8 (the lower bounds for noncombining AAB on $k$-port SF full-duplex networks). Explain why the bound depends on $|V(G)|$ and $k$ but not on the diameter.
- Define a time-arc-disjoint tree (TADT). State Corollary 10 and explain why it makes synchronized AAB contention-free.
- State Lemma 11 (the TADT condition for vertex-symmetric orthogonal topologies) and prove it by contradiction. Why is the direction-distinctness rule both necessary and sufficient?
- Construct the generic $B(*)$ for the square torus $K(z, z)$ with $z$ odd. Verify that the four snake rotations cover the whole torus and use one arc of each direction at every level.
- Describe the 3D analogue of the snake construction for $K(z, z, z)$ with $z$ odd. How does the cube decompose into 6 pyramids and how do the 6 rotations of a 3D snake map onto them?
- Describe the link-disjoint Hamiltonian-cycle AAB algorithm. Explain the role of splitting each packet into two halves and sending them in both directions along each cycle.
- State Lemma 12 (the latency of the Hamiltonian-cycle AAB on a 2D torus) and prove that the round count is $\approx z_1 z_2 / 2$.
- Compare the TADT and Hamiltonian-cycle solutions for AAB on 2D tori. Show that they have the same data-volume cost but different round counts. For which regime of $\mu$ does each win?
- Why is the Hamiltonian-cycle approach not applicable to 2D meshes while the TADT approach is? What is the corresponding constraint on the topology?
- Discuss the multi-dimensional generalization of the Hamiltonian-cycle approach to $n$-dimensional tori. What constraint on the degree is required, and what is the asymptotic cost of constructing the cycles?