### Scope of the question

One-to-all scatter (OAS) is the personalized counterpart of OAB: a single source $s$ holds $N - 1$ distinct packets (one for each other node, each of size $\mu$ for simplicity), and at the end every other node holds exactly one of those packets. Reversing the direction of every transfer gives the all-to-one gather (AOG), which has identical latency. The corresponding MPI call is `MPI_Scatter`; the reverse is `MPI_Gather`. The complexity of OAS is closely tied to AAB and AAG, because in all three operations a distinguished role (the source, or every node simultaneously) must distribute or receive $N - 1$ distinct packets:

- AOG: the destination receives $N - 1$ distinct packets
- OAS: the source distributes $N - 1$ distinct packets
- AAB/AAG: every node receives $N - 1$ distinct packets This question focuses on the combining model, in which packets travelling the same direction can be merged into one bigger communication unit and sent with a single startup latency.

### The combining model

In the combining model, packets transferred in the same direction can be bundled and sent with a single startup. The intuition: if multiple destinations lie in the same direction on the same outgoing port, their packets are concatenated and pushed out as one larger payload, charging $t_s$ only once. A useful analogy: think of FedEx pooling all parcels destined for the same continent onto one plane (single startup), then splitting them onto local trucks at the destination (subsequent recursive scatters). At each step, the bundle is smaller than at the previous step, because some of the original packets have been delivered. The combining capability dramatically changes the per-round cost structure compared to the noncombining model:

- in noncombining the receiver is bottlenecked by receiving one packet at a time (lower bound is round count $\lceil (|V| - 1)/k \rceil$, see question 38)
- in combining the source is bottlenecked by pushing out the total data volume, but uses only logarithmically many rounds

### Lemma 7: lower bounds for combining OAS

Let $G$ be a $k$-port network with $N$ nodes, and consider a combining OAS of packets of size $\mu$ from source $s \in V(G)$. Then: $$ \rho_{OAS,k}(G, s) = \rho_{OAB,k}(G, s) $$ $$ \gamma_{OAS,k}(G, s) = \gamma_{OAB,k}(G, s) = \mathrm{exc}(s, G) $$ $$ \tau_{OAS,k}(G, \mu, s) = \rho_{OAS,k}(G, s) t_s + \gamma_{OAS,k}(G, s) t_d + \left\lceil \frac{|V(G)| - 1}{k} \right\rceil \mu t_m $$ Proof:

- $\rho$: combining OAS is the same as OAB except that the size of the packet decreases gradually. The number of rounds is therefore bounded below by the OAB rounds lower bound (whether SF or WH, depending on the technology).
- $\gamma$: the traffic structure is the same as OAB - the source must reach the farthest node, so $\mathrm{exc}(s, G)$ rounds is the minimum distance cost.
- $\tau$: the source must transmit a total of $(|V(G)| - 1) \mu$ data through its $k$ outgoing ports, giving the $\lceil (|V(G)| - 1)/k \rceil \mu t_m$ data-volume term. The other two terms ($\rho t_s$ for startups and $\gamma t_d$ for cumulative WH cutting-through cost) follow the same structure as OAB.

> Combining OAS has the same round count and the same traffic as OAB. The only difference is that the per-round payload decreases as the recursion proceeds, while in OAB it stays at $\mu$ in every round.

### Combining OAS on 1-port hypercubes

The hypercube is the most illustrative case. The SBT-based algorithm (the same spanning binomial tree from OAB) is optimal for combining OAS:

```
Algorithm CombiningOAS_Hypercube(Q_n, s):
For phase i = 1, 2, ..., n:
    The source and every node informed so far simultaneously
    sends the appropriate bundle of personalized packets
    across the dimension-i edge to its partner in Q_n.
```

At each step the payload along an edge drops to one half of what was sent in the previous step, because half the packets in the bundle reach their final-destination subcube and stay there. The latency is: $$ t_{OAS}(Q_n, \mu) = \sum_{i=1}^{n} (t_s + \mu \cdot 2^{n-i} t_m) = t_s n + \mu t_m (2^n - 1) $$ The interpretation:

- $n$ rounds, each paying one startup $t_s$, giving the $t_s n$ term
- the data volumes per round are $\mu \cdot 2^{n-1}, \mu \cdot 2^{n-2}, \ldots, \mu \cdot 2^0$ (halving each step), summing to $\mu (2^n - 1)$, giving the $\mu t_m (2^n - 1)$ term The first round sends $\mu \cdot 2^{n-1}$ bytes (half of all packets to the partner subcube), the second round sends $\mu \cdot 2^{n-2}$ per active sender, and so on. By comparing with Lemma 7 we confirm optimality:
- $\rho = n = \log_2 |V(Q_n)|$, matching the OAB lower bound
- the total data volume $\mu (2^n - 1) = \mu (|V| - 1)$ matches $\lceil (|V| - 1)/k \rceil \mu t_m$ for $k = 1$ A noteworthy observation: WH switching makes no difference at the algorithmic level here, because the SBT routing in the hypercube uses single-hop dimension flips. The store-and-forward vs wormhole distinction is invisible in this particular case. The 3-round example in $Q_3$ from the slide:
- Round 1: source sends a bundle of 4 packets across dimension 0 (or 2, depending on labeling), covering the 4 destinations in the partner subcube
- Round 2: source and round-1 partner simultaneously send bundles of 2 packets across the next dimension
- Round 3: all 4 currently-active nodes send individual packets across the last dimension

### Combining WH OAS on meshes and tori

RDA applies as in OAB, except with a different tradeoff between path lengths and message sizes when the divided size is odd. Recall from question 34 that the OAB 1D-mesh RDA splits the mesh in half and the source keeps the smaller part of an odd split, because the message size $\mu$ stays constant and minimizing the source's remaining work is what matters. For combining OAS the tradeoff is opposite. The slide makes this explicit with two options for an odd split:

- option (a): $t_s + \lfloor z/2 \rfloor t_d + \lceil z/2 \rceil \mu t_m$
- option (b): $t_s + \lceil z/2 \rceil t_d + \lfloor z/2 \rfloor \mu t_m$ Option (a) corresponds to "source keeps the larger half" (the smaller half is given away, which is the smaller payload to push out). Option (b) corresponds to "source keeps the smaller half" (the larger payload is sent across). The cost difference between the two options:
- in $t_d$ (the WH cutting-through cost): one extra hop, $t_d$
- in $\mu t_m$ (the data volume cost): one extra packet's worth, $\mu t_m$ For reasonably large $\mu$, the $\mu t_m$ savings dominate over the $t_d$ extra hop. Therefore the OAS RDA should give away the smaller half and keep the larger half:

```
Phase 1.1 (OAS RDA): Source s splits the mesh into 2 halves.
If odd size, source keeps the GREATER part.
```

This is exactly the reverse of the OAB RDA rule (where the source keeps the smaller part). The same principle applies in higher-dimensional meshes via the Cartesian product construction. The latency of combining OAS on a 1D WH mesh $M(z)$ with $s$ at the boundary is: $$ t_{OAS}(M(z), \mu, 0) = t_s \lceil \log z \rceil + t_d (z - 1 + f(z)) + \mu t_m (z - 1) $$ where $0 \leq f(z) \leq \log z$ is a small additive correction accounting for the recursive split structure. The three terms correspond to:

- $t_s \lceil \log z \rceil$: $\lceil \log z \rceil$ startups, one per recursive halving round (this is the OAB rounds bound)
- $t_d (z - 1 + f(z))$: cumulative WH cutting-through cost; the $z - 1$ term is the eccentricity contribution and $f(z)$ is the recursion overhead
- $\mu t_m (z - 1)$: total data volume term; the source pushes out $(z - 1)\mu$ bytes total, which is $(|V| - 1)\mu$ for $k = 1$, matching Lemma 7 For the more general non-boundary source case, the eccentricity term changes but the structure is the same.

> In broadcast RDA, you keep the smaller half because the path-length cost dominates and you want to minimize remaining work for yourself. In scatter RDA the situation is opposite: you send an amount of data proportional to the destination subset size, so giving away the smaller half saves you more on the dominant data-volume term.

### OAS vs OAB: the structural connection

The combining OAS algorithm has exactly the same broadcast tree structure as OAB. The differences are entirely in what is sent at each step:

- OAB: every round, every active sender sends a packet of constant size $\mu$ along its edge in the tree
- combining OAS: every round, every active sender sends a bundle of personalized packets, the size of which halves at each level of the recursion This isomorphism is what makes the OAS lower bound formula so similar to the OAB lower bound, and what justifies the "the only difference is that the size gradually decreases" intuition. The same OAB tree (SBT for hypercube, DOST for mesh/torus) is used in both algorithms. The combining capability is what allows the tree-based OAS to be efficient: without combining, the source would have to send each of the $N - 1$ packets individually, paying $N - 1$ startups, which is dramatically worse than the $\log N$ startups of the combining algorithm.

### AOG by reversal

Because AOG (all-to-one gather, `MPI_Gather`) is the reverse of OAS, all the algorithms and latency bounds above apply unchanged to AOG. The only difference is the direction of data flow: in AOG every non-destination node sends its packet up the tree toward the destination, with packets being concatenated (combined) at each intermediate node.

### Potential exam questions

- Define the OAS operation and the combining model. How does combining differ from noncombining in terms of per-startup payload?
- State Lemma 7 (the lower bounds for combining OAS) and prove each of $\rho$, $\gamma$, and the data-volume term in $\tau$.
- Explain why "combining OAS is the same as OAB except that the message size decreases gradually". Make the structural correspondence between the two algorithms precise.
- Describe the SBT-based combining OAS algorithm on a 1-port hypercube $Q_n$. Compute the data volume per round and prove that it halves at each step.
- Derive the latency $t_{OAS}(Q_n, \mu) = t_s n + \mu t_m (2^n - 1)$. Identify the two terms and confirm optimality against Lemma 7.
- Why does the SF vs WH distinction make no difference for combining OAS on the hypercube? In which other topology does this also hold, and where does it stop holding?
- State the OAS RDA splitting rule for an odd-size 1D mesh. Justify why the source should keep the greater part, in contrast to the OAB RDA rule. Quantify the difference in latency between the two options.
- Derive the latency of combining OAS on a 1D WH mesh $M(z)$ with source at the boundary. Identify the three terms and explain their physical meaning.
- Compare the combining OAS algorithm with the corresponding combining AOG (gather) algorithm. Why does reversing the direction of data flow not change the latency?
- Suppose you have a 1-port network where combining is not supported. Estimate the resulting OAS latency by treating each of the $N - 1$ packets as a separate OAB target. By how much does combining improve the round count?
- Explain why on a multi-dimensional mesh the Cartesian product construction extends the 1D OAS algorithm. Where does the OAS "keep the greater part" rule apply within this construction?