### Scope of the question

This question asks about one-to-all broadcast (OAB) in store-and-forward (SF) networks. OAB is the simplest collective: a single source node $s$ holds a packet of size $\mu$ and the goal is to deliver a copy to every other node. The question requires (1) the general lower bounds on rounds, work, traffic, and latency for $k$-port SF networks, (2) the algorithms that achieve them in the all-port and 1-port models, and (3) the corresponding latencies for the canonical topologies (hypercubes, meshes, tori).

### General framework

The complexity of a CCO in a graph $G$ is described by four quantities. Lower bounds are denoted by Greek letters and upper bounds (achieved by the best known algorithm) by Latin letters:

- number of parallel rounds: lower bound $\rho_{XXX}(G)$, upper bound $r_{XXX}(G)$
- communication latency in seconds: lower bound $\tau_{XXX}(G)$, upper bound $t_{XXX}(G)$
- communication work (total number of hops in SF): lower bound $\eta_{XXX}(G)$, upper bound $h_{XXX}(G)$
- sum of maximal path lengths over all rounds: $\gamma_{XXX}(G)$ In SF, one round is a set of parallel hops. Two efficiency conditions must usually hold to claim optimality:
- NODUP (no duplication): no node receives the same packet twice
- NOHO (no node hears its own information): a node never receives back what it has already sent

### Lemma 1: SF OAB lower bounds in a $k$-port network

Consider an OAB of a packet of size $\mu$ with source $s$ in a $k$-port SF network $G$. Then: $$ \eta^{SF}_{OAB,k}(G, s) = |V(G)| - 1 $$ $$ \rho^{SF}_{OAB,k}(G, s) = \begin{cases} \max(\mathrm{diam}(G), \log_{k+1}|V(G)|) & \text{if } G \text{ is vertex-symmetric} \ \max(\mathrm{exc}(s, G), \log_{k+1}|V(G)|) & \text{otherwise} \end{cases} $$ $$ \gamma^{SF}_{OAB,k}(G, s) = \begin{cases} \mathrm{diam}(G) & \text{if } G \text{ is vertex-symmetric} \ \mathrm{exc}(s, G) & \text{otherwise} \end{cases} $$ $$ \tau^{SF}_{OAB,k}(G, \mu, s) = \rho_{OAB,k}(G, s)(t_s + \mu t_m) + \gamma_{OAB,k}(G, s) t_d $$

### Proof of Lemma 1

The work bound is immediate: every non-source node must receive at least one copy of the packet, so at least $|V(G)| - 1$ hops are needed. The rounds bound combines two independent constraints:

- Fan-out constraint: the number of informed nodes can at most multiply by $k+1$ each round (each of the $k$ ports of each informed node delivers one new copy). Starting from 1 informed node we get the sequence $1, 1+k, (1+k)^2, \ldots, (1+k)^i$ after $i$ rounds. OAB cannot terminate before $(1+k)^i \geq |V(G)|$, giving the $\log_{k+1}|V(G)|$ term.
- Distance constraint: information propagates only to neighbors per round, so reaching the farthest node from $s$ takes at least $\mathrm{exc}(s, G)$ rounds. In a vertex-symmetric graph, all eccentricities equal the diameter, so this becomes $\mathrm{diam}(G)$. The maximum of the two terms is the binding bound. The traffic bound $\gamma$ follows because in each round we count the longest path used; the sum over all rounds cannot be less than the source eccentricity (the farthest node must eventually be reached). The latency bound follows by assigning costs to each round: each round pays one startup $t_s$ and transmits a packet of size $\mu$ along some path. The simplified $t_{SF} \approx t_s + \delta \mu t_m$ formula gives the displayed expression once $t_d = t_r + t_w + t_m$ is folded into $\gamma t_d$.

### Lemma 2: optimal OAB in all-port SF networks - flooding

Any all-port SF network $G$ has a trivial round-optimal OAB algorithm called flooding:

```
Algorithm FloodingOAB(G, s)
Source s: send the packet to all neighbors.
Other nodes: if (the packet arrived for the first time)
             then copy it and send it to all remaining neighbors
             else ignore it.
```

Flooding is round-optimal because it is essentially a breadth-first dissemination - each packet travels along a shortest path from the source. However if $G$ is cyclic some nodes may receive the same packet twice (violating NODUP) or receive back what they sent out (violating NOHO). The NODUP+NOHO fix is controlled flooding: restrict each node to forward only along the edges of a chosen shortest-path spanning tree rooted at $s$ (an OAB tree). Each non-source node then receives exactly one copy. Good explicit constructions exist for both hypercubic and orthogonal networks.

### OAB in SF hypercubes - the spanning binomial tree

The $n$-level spanning binomial tree $SBT_n$ is the canonical OAB tree of the hypercube $Q_n$, and it is recursive in structure. $SBT_n$ is optimal for both 1-port (using RDA, see below) and all-port (using controlled flooding) hypercubes. The number of nodes that receive the packet in round $i$ is:

- $2^{i-1}$ in the 1-port model (informed nodes double each round)
- $\binom{n}{i}$ in the all-port model (this is precisely why these trees are called binomial) The all-port forwarding rule is: each node forwards the packet only along dimensions strictly higher than the dimension on which it received it. This local rule produces the binomial spanning structure with no collisions. In both cases the communication work matches the optimum: $$ h_{OAB}(Q_n) = \sum_{i=1}^{n} 2^{i-1} = \sum_{i=1}^{n} \binom{n}{i} = 2^n - 1 = \eta_{OAB}(Q_n) $$ A striking point is that going from 1-port to all-port does not improve the round complexity of OAB in $Q_n$: the 1-port bound is $\lceil \log_2 |V| \rceil$ and the all-port bound is the diameter, and these coincide in the hypercube.

### Recursive doubling algorithm (RDA) in 1-port networks

RDA is the generic 1-port broadcast scheme. It works on any graph that can be recursively bisected:

```
Algorithm GenRecDoubl(G, s)
Partition G into two halves G_1 and G_2 of the same size so that
    the source s in G_1 has a neighbor s' in G_2.
Source s sends the packet to s'.
do_in_parallel { GenRecDoubl(G_1, s); GenRecDoubl(G_2, s') }
```

Each round doubles the number of informed nodes. On a 1-port complete graph the communication pattern of RDA is exactly the spanning binomial tree, which is the hypercubic broadcast pattern. This is why RDA on the hypercube is round-optimal: $r_{OAB}(Q_n) = \lceil \log_2 |V| \rceil$.

> RDA is the most important generic idea behind 1-port broadcast. Everything else - hypercubic SBT, mesh/torus DOST, multi-D mesh recursion - is a graph-specific realization of "split, inform a node in the other half, recurse".

### OAB optimality in 1-port SF networks: an NP-completeness caveat

In a 1-port network the best one can hope for is to double the informed set each round. But the problem of deciding whether a given 1-port SF network admits a round-optimal OAB with $r = \mathrm{diam}(G)$ is NP-complete in general. A concrete example of the gap: the complete binary tree $CBT_m$ has $r_{OAB}(CBT_m) = 3m - 1$ under the farthest-first heuristic (at each branching point, send first toward the neighbor leading to the larger subtree), while the diameter is only $\mathrm{diam}(CBT_m) = 2m$. The exact lower bound is therefore not achievable on $CBT_m$. This shows that even on very regular graphs the local choice between competing directions cascades and prevents reaching the diameter bound.

### OAB in SF meshes and tori - dimension-ordered spanning trees

For meshes and tori the controlled flooding uses dimension-ordered spanning trees (DOSTs), the natural generalization of hypercubic SBTs. Every node uses the dimensions in the same agreed order (e.g. $0, 1, 2, \ldots$), and DOSTs are minimal shortest-path spanning trees. The all-port forwarding rule:

```
Algorithm DOTMeshAllPortOAB(M(...), s)
Source s: Send the packet to all neighbors.
Remaining nodes:
    if (the packet arrived via the channel in dimension i, 1 <= i <= n)
    then forward it in dimension i further (if possible)
         and simultaneously send it to all neighbors in dimensions
         (i+1)^+, (i+1)^-, ..., (n-1)^+, (n-1)^-  (if they exist)
```

The rule "received from dimension $i$, forward further in $i$ and to all higher dimensions" requires no global coordination: the shared dimension ordering ensures contention-free and duplicate-free coverage of the mesh. The latency in the all-port case is: $$ t_{OAB}(M(\ldots), \mu, s) = (t_s + t_d + \mu t_m) \cdot \mathrm{exc}(M(\ldots), s) $$ The all-port DOST algorithm is optimal in all parameters (rounds, work, latency). The 1-port case uses the same tree structure but serializes the communication with neighbors: at a node with multiple uninformed neighbors, the sends to those neighbors are performed in successive rounds rather than in parallel. The 1-port DOST algorithm is therefore not round-optimal. When the 1-port node has a choice between two neighbors, it should apply the farthest-first heuristic (send first toward the neighbor representing the larger remaining subgraph), as in the $CBT_m$ example. For tori the source is effectively "always in the center" due to vertex symmetry, so a single DOST picture describes the broadcast from every possible source up to relabeling. For meshes the source position matters and the eccentricity $\mathrm{exc}(s, M)$ replaces the diameter in the latency.

### Summary of latencies

For the hypercube $Q_n$ (vertex-symmetric, $\mathrm{diam}(Q_n) = n$, $|V| = 2^n$):

- 1-port: $r_{OAB}(Q_n) = n = \lceil \log_2 |V| \rceil$, optimal
- all-port: $r_{OAB}(Q_n) = n = \mathrm{diam}(Q_n)$, optimal
- in both cases: work $= 2^n - 1$, latency $= n(t_s + \mu t_m) + n t_d$ For an $n$-dimensional mesh $M(z_1, \ldots, z_n)$ with source $s$:
- all-port DOST: $t_{OAB} = (t_s + t_d + \mu t_m) \cdot \mathrm{exc}(M, s)$, optimal in all parameters
- 1-port DOST: same tree but communication with neighbors is serialized, not round-optimal For a torus $K(z_1, \ldots, z_n)$ the source is always central by vertex symmetry, so $\mathrm{exc}$ is replaced by $\mathrm{diam}$ and the latency is the same up to that substitution.

### Potential exam questions

- State Lemma 1 (the SF OAB lower bounds) and prove it. Explain why the rounds bound is a maximum of two independent terms and what each term represents.
- Explain why the lower bound for $\rho$ differs for vertex-symmetric and non-vertex-symmetric graphs. Give a topology of each kind.
- Define the conditions NODUP and NOHO. Show that pure flooding violates them on a cyclic graph and explain how controlled flooding fixes both.
- State and explain the FloodingOAB algorithm. Prove that it is round-optimal in all-port SF networks.
- Describe the spanning binomial tree $SBT_n$ and prove that it gives an optimal OAB algorithm in both 1-port and all-port hypercubes. Why does increasing the port count from 1 to all not improve the round complexity in $Q_n$?
- Describe the recursive doubling algorithm (RDA) for 1-port broadcast. Why is it round-optimal on EREW PRAM and on the 1-port hypercube?
- Show that the problem of deciding whether a given 1-port SF network admits a round-optimal OAB with $r = \mathrm{diam}(G)$ is NP-complete. Illustrate with the complete binary tree $CBT_m$, computing $r_{OAB}(CBT_m)$ under the farthest-first strategy and comparing with $\mathrm{diam}(CBT_m)$.
- Define a dimension-ordered spanning tree (DOST). Write down the forwarding rule a non-source node applies in the all-port DOST OAB algorithm. Argue that this local rule alone produces a correct contention-free and duplicate-free broadcast.
- Derive the latency $t_{OAB}(M(\ldots), \mu, s) = (t_s + t_d + \mu t_m) \cdot \mathrm{exc}(M, s)$ for the all-port DOST algorithm in a multi-dimensional mesh.
- Why is the 1-port DOST algorithm not round-optimal in meshes? Where exactly does the loss come from, and what heuristic should a 1-port node apply when it has a choice of uninformed neighbors?
- Compare the work, rounds, and latency of OAB in $Q_n$ vs $M(z, z)$ (with $z^2 = 2^n$). Which performs better and why?