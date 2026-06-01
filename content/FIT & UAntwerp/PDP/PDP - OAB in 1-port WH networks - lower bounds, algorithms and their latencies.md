### Scope of the question

This question asks about one-to-all broadcast (OAB) in 1-port wormhole (WH) networks. The fundamental shift from SF to WH is that wormhole communication is almost distance-insensitive: in the latency formula $t_{WH}(\mu, \delta) \approx t_s + \delta t_d + \mu t_m$, the distance $\delta$ and the message size $\mu$ enter as a sum rather than as a product. As a result, the diameter and eccentricity lower bounds that dominated SF analysis no longer apply, and the only constraint on the number of rounds is the fan-out limit imposed by the port count. The question requires (1) the general lower bounds, (2) the 1-port RDA algorithm and its instantiation on hypercubes, 1D meshes and tori, multi-dimensional meshes, and (3) the resulting latencies.

### Lemma 4: WH OAB lower bounds in a $k$-port network

Since WH communication is distance-insensitive, the diameter and eccentricity lower bounds on the number of rounds do not apply. Consider a $k$-port interconnection network $G$ with WH switching and an OAB of a packet of size $\mu$ from source $s \in V(G)$. Then: $$ \eta^{WH}_{OAB,k}(G, s) = \eta^{SF}_{OAB,k}(G, s) = |V(G)| - 1 $$ $$ \rho^{WH}_{OAB,k}(G, s) = \lceil \log_{k+1} |V(G)| \rceil $$ $$ \gamma^{WH}_{OAB,k}(G, s) = \gamma^{SF}_{OAB,k}(G, s) = \mathrm{exc}(s, G) $$ $$ \tau^{WH}_{OAB,k}(G, \mu, s) = \rho^{WH}_{OAB,k}(G, s)(t_s + \mu t_m) + \gamma^{WH}_{OAB,k}(G, s) t_d $$ The work bound $\eta = |V(G)| - 1$ is unchanged from SF: every non-source node still needs to receive one copy. The traffic bound $\gamma$ remains the source eccentricity because the longest path used must eventually reach the farthest node. The rounds bound, however, becomes purely fan-out limited: in a $k$-port network the number of informed nodes can at most multiply by $k + 1$ per round, so $(k+1)^r \geq |V(G)|$ gives $r \geq \lceil \log_{k+1} |V(G)| \rceil$. In the 1-port case ($k = 1$) this reduces to $\lceil \log_2 |V(G)| \rceil$ - rounds simply double each step. The latency lower bound combines the two costs: $\rho$ rounds of startup and per-round message delivery $(t_s + \mu t_m)$, plus $\gamma t_d$ for the cumulative distance term that the cutting-through phase contributes.

> The key difference from SF: in 1-port WH the round bound is logarithmic in $|V(G)|$ regardless of the graph's diameter. WH can broadcast on a 1D line of $z$ nodes in $\lceil \log z \rceil$ rounds, not $z - 1$ rounds.

### Achievability in 1-port WH

The logarithmic round count $\lceil \log_2 |V(G)| \rceil$ is achievable on any 1-port WH network by simulating the hypercubic RDA SBT-based OAB scheme. The argument is structural: RDA only requires that the network can be recursively bisected into two halves of equal size with one cross-edge from source to its counterpart, and in WH this cross-edge can be realized by any path - distance does not matter. So RDA "embedded" in any 1-port WH graph achieves the logarithmic round bound. Achieving the corresponding bound $\lceil \log_{k+1} |V(G)| \rceil$ on $k$-port WH networks is much harder; for 1-port it is essentially free.

### Recursive doubling on 1-port WH networks: the generic idea

The generic RDA was introduced for SF and is reproduced unchanged for WH. The recipe is:

```
Algorithm GenRecDoubl(G, s)
Partition G into two subnetworks G_1 and G_2 of the same size so that
    the source s in V(G_1) has a neighbor s' in G_2.
Source s sends the packet to s'.
do_in_parallel { GenRecDoubl(G_1, s); GenRecDoubl(G_2, s') }
```

Each round doubles the number of informed nodes. The only WH-specific element is that the cross-edge $s \to s'$ may correspond to a long path in $G$ - this costs $t_d$ per hop in the cutting-through phase but no additional rounds.

### 1-port WH on 1D tori and meshes

The 1D case is the most striking demonstration of WH's power: on a 1D mesh of $z$ nodes, SF needs $z - 1$ rounds (because the diameter is $z - 1$), but WH needs only $\lceil \log z \rceil$ rounds.

```
Algorithm 1-DTorusRecDoublOAB(K(z), s)
Ph.1.1: Source s splits the K(z) torus into 2 halves (1D submeshes).
        If z is odd, s keeps the smaller part.
Ph.1.2: Source s sends the packet to its counterpart in the other submesh.
Ph.2:   Repeat Phase 1 recursively in both submeshes simultaneously.
```

The rule "keep the smaller part" matters when $z$ is odd: in broadcast you want to minimize your own remaining work, so giving away the larger half is better. This rule will be reversed for scatter (OAS), where the dominant cost is the volume of data sent, not the path length. The latency on a 1D torus $K(z)$ is: $$ t_{OAB}(K(z), \mu) = \sum_{i=1}^{\lceil \log z \rceil} \left( t_s + \left\lceil \frac{z}{2^i} \right\rceil t_d + \mu t_m \right) = (t_s + \mu t_m) \lceil \log z \rceil + t_d (z - 1) $$ The closed form has a clean interpretation: $\lceil \log z \rceil$ rounds each pay one startup and one message transmission cost, and the cumulative distance term sums to $z - 1$ (the diameter contribution from the $\gamma$ lower bound, which is achieved with equality). The latency on a 1D mesh $M(z)$ with source position $s$ is similar but uses the source eccentricity instead of the diameter: $$ t_{OAB}(M(z), \mu, s) = \lceil \log z \rceil (t_s + \mu t_m) + \max(z - s - 1, s) t_d $$ The $\max(z - s - 1, s)$ term is the eccentricity of $s$ in $M(z)$: the distance to whichever endpoint of the line is farther from $s$.

### 1-port WH on multi-dimensional meshes

For multi-dimensional meshes the algorithm combines the 1D recursive doubling with the Cartesian product construction:

- in phase $i$, the set of informed nodes forms a submesh $M(*^i, a_{i+1}, \ldots, a_n)$ where the first $i$ dimensions have been fully filled and the others still hold the source's coordinate
- intuitively: first inform a row, then once the row is full, inform the columns from each row node, then once the 2D layer is full, propagate into 3D, and so on This is "the hypercubic 1-port RDA algorithm + the Cartesian product constructor". Each dimension contributes $\lceil \log z_i \rceil$ rounds and a cumulative distance term proportional to its extent, giving (by dimensional decomposition): $$ t_{OAB}(M(z_1, \ldots, z_n), \mu, s) = \sum_{i=1}^{n} \lceil \log z_i \rceil (t_s + \mu t_m) + \sum_{i=1}^{n} \mathrm{exc}_i(s) \cdot t_d $$ where $\mathrm{exc}_i(s)$ is the eccentricity of $s$ along dimension $i$.

### Hypercube as the canonical case

The hypercube $Q_n$ is the most illustrative special case: it is precisely the topology on which the SBT (spanning binomial tree) embeds the RDA pattern perfectly. The 1-port WH RDA on $Q_n$ takes exactly $n = \log_2 |V(Q_n)|$ rounds, with each round corresponding to one dimension. The latency formula is the cleanest: $$ t_{OAB}(Q_n, \mu) = n(t_s + \mu t_m) + n t_d $$ The work is $h_{OAB}(Q_n) = 2^n - 1 = \eta_{OAB}(Q_n)$, matching the lower bound, and the round count $n$ matches both the diameter and the logarithmic fan-out bound. In WH this is round-optimal for the same reason as in SF; the difference is that WH achieves the logarithmic bound on much weaker topologies too (e.g. on 1D meshes).

### Practical observation

The fundamental practical takeaway from 1-port WH OAB is that for short messages (small $\mu$), the dominant cost is the round count $\lceil \log z \rceil (t_s + \mu t_m)$, which is small and grows slowly with network size. For long messages or sparse high-diameter topologies, the $\gamma t_d$ traffic term can dominate, but distance enters only linearly through $t_d$ and not multiplicatively with $\mu$. This is exactly why WH was the default switching technology in modern parallel computers before more sophisticated hybrid schemes appeared.

### Potential exam questions

- State Lemma 4 (the WH OAB lower bounds for $k$-port networks). Compare each bound term-by-term with the SF case from Lemma 1 and explain which bounds change, which stay the same, and why.
- Why does the diameter lower bound on rounds disappear in WH networks? Where does the source eccentricity $\mathrm{exc}(s, G)$ still enter the latency formula?
- Prove that on a 1-port WH network the logarithmic round bound $\lceil \log_2 |V(G)| \rceil$ is achievable on any connected graph. Explain why the corresponding all-port bound is much harder to achieve.
- State the recursive doubling algorithm GenRecDoubl. Prove that it doubles the number of informed nodes per round.
- Describe the 1-DTorusRecDoublOAB algorithm and derive its latency $t_{OAB}(K(z), \mu) = (t_s + \mu t_m) \lceil \log z \rceil + t_d (z - 1)$.
- In the odd-size case, the broadcast RDA keeps the smaller part while the scatter RDA keeps the larger part. Explain why these rules are opposite, in terms of which cost term dominates in each operation.
- Derive the latency $t_{OAB}(M(z), \mu, s)$ for a 1D mesh with source at arbitrary position $s$. Why does $\max(z - s - 1, s)$ replace the diameter?
- Explain how 1-port WH OAB on a multi-dimensional mesh $M(z_1, \ldots, z_n)$ is constructed from the 1D algorithm via the Cartesian product. Give the structure of the informed set after phase $i$.
- Compare the round count of 1-port OAB on a 1D mesh of $z$ nodes in SF and in WH. Quantify the gap and explain its source.
- Why does the 1-port WH RDA on the hypercube $Q_n$ achieve exactly $n$ rounds, matching both the diameter and the logarithmic fan-out bound? Is this a coincidence?
- Write the latency $t_{OAB}(Q_n, \mu)$ for 1-port WH and compare it with the corresponding SF formula. Which term differs and which is the same?