
> [!tldr] First 5 minutes of hell
> Dragonfly is a topology that takes into account the different prices and properties of cables (short, metallic, are cheap, long, optic fibres, are expensive). 
> 
> The structure has 3 levels:
> - a router (switch with many ports)
> - a group of routers (connected with metallic) - in one rack or a row of racks
> - a system of many groups (connected by optical cables) - across the data center
> 
> Three parameters of $DF(a, p, h)$:
> - $a$ = how many routers in each group
> - $p$ = how many computers are attached to each router
> - $h$ = how many ports each router has to other groups (optical cables)
> - each router then has $p$ ports for all connected computers and $a-1$ ports for other routers in the same group and $h$ ports for connection to other groups
> 	- router radix = $p+(a-1)+h$
> 
> Properties:
> - the total number of groups in the $DF(a,p,h)$  is at most $a\cdot h+1$ (each group has $a\cdot h$ links to other groups +1 (the one group itself))
> - the total number of routers is the total number of groups multiplied by $a$ ($a$ routers in each group)
> - the total number of compute nodes is the total number of routers mutliplied by $p$
> - each group behaves as a virtual router with a very high port count ($a\cdot h$)
> 
> Routing:
> - at most 3 router hops (+ 2 hops from the computer/node to the router):
> 	- hop1: firstly, send the message from the source router to the other router, which owns the optical cable to the destination group (exactly one hop, because it is a complete graph)
> 	- hop2: cross the optical cable to the other group
> 	- hop3: jump from the entry router to the destination router
> - if the direct path is busy (congestion with a lot of messages), use an indirect path 
> 	- randomly generate one intermediate router (from a different group) and send the message by a direct path to that router and the by the direct path from that intermediate router to the destination
> 		- cost: double latency
> 		- gain: predictable load balancing (randomization spreads the traffic)
> 	- this is called Valiant routing
> 
> The decision, which router in one group will be connected by the global (optical) link to which router is an important design question:
> - it affects e.g. bisection width

### Context and motivation

The Dragonfly topology was proposed in 2008 and has many subvariants; the lecture covered the canonical (basic) one. It addresses a fundamental hardware reality: two link technologies are available, with very different cost / distance characteristics. Metallic (coax / electric) cables are cheap but only practical over short distances (order of meters). Optical fiber is required for long-distance links between groups, racks, or rooms, but is more expensive. The Dragonfly is designed hierarchically to exploit both: dense cheap connectivity locally via metallic links, sparse but direct optical links between groups.

It is also the most relevant modern HPC topology - the design is now the dominant choice for high-end machines (e.g., HPE Slingshot is the most recent contribution in this family).

### Definition

The canonical Dragonfly is parameterised by three integers $(a, p, h)$ and is defined as a hierarchical graph with three levels: routers, groups, system.

- $a$ = number of routers per group (connected via the intra-group network)
- $p$ = number of compute nodes (leaves) attached to each router
- $h$ = number of global optical ports per router (links to other groups)

The construction:

1. Each router has $p$ ports to its compute nodes (leaves of the Dragonfly).
2. Each router has up to $a - 1$ ports for local connections to other routers within its group. The intra-group network can be a full graph (complete graph $K_a$) or another dense topology like a hypercube.
3. Each router has $h$ ports for global optical links to other groups.
4. The groups are interconnected so that each group has at least one direct optical link to every other group (i.e., the groups form a complete graph at the inter-group level).

A group acts as a virtual router with $a \cdot h$ external (global) ports.

### Counting formulas (canonical Dragonfly)

Total number of groups in the system: $$G_{a,p,h} = a \cdot h + 1$$

The "+1" comes from the requirement that each group has at least one direct optical link to every other group: each group exposes $a \cdot h$ global ports outward and needs to use each to reach a distinct other group, so there are at most $a \cdot h + 1$ groups in the whole system.

Total number of routers: $$S_{a,p,h} = G_{a,p,h} \cdot a = (a \cdot h + 1) \cdot a$$

Total number of compute nodes (system size): $$N_{a,p,h} = S_{a,p,h} \cdot p = G_{a,p,h} \cdot a \cdot p = (a \cdot h + 1) \cdot a \cdot p$$

Ports per router:

- $p$ ports to compute nodes (metallic, short)
- $a - 1$ ports for intra-group links (metallic)
- $h$ ports for global links (optical)
- Total metallic ports per router: $p + a - 1$
- Total optical ports per router: $h$

Total number of links in the system:

- Bidirectional metallic local links (intra-group, assuming complete intra-group graph): $G_{a,p,h} \cdot \dfrac{a(a-1)}{2}$
- Bidirectional global optical links: $G_{a,p,h} \cdot a \cdot h$ (counted from one side; halve if counting unordered pairs of groups)

Virtual-router perspective: from outside, each group behaves as a single high-radix virtual router with $a \cdot h$ external ports.

### Example: $DF(4, 2, 2)$

One group consists of $a = 4$ routers connected as a complete graph $K_4$ (each router has $a - 1 = 3$ local links to its peers). Each router has $h = 2$ optical ports and $p = 2$ compute nodes attached. The group exposes $a \cdot h = 8$ global ports - it behaves as a virtual router with 8 external ports.

For the full system $DF(4, 2, 2)$:

- $G = a \cdot h + 1 = 4 \cdot 2 + 1 = 9$ groups
- $S = 9 \cdot 4 = 36$ routers
- $N = 36 \cdot 2 = 72$ compute nodes
- $9 \cdot \dfrac{4 \cdot 3}{2} = 54$ metallic local links
- $9 \cdot 4 \cdot 2 = 72$ global optical link-endpoints (i.e., 36 bidirectional optical cables)

### Routing in $DF(a, p, h)$

Assume the intra-group network is a complete graph (canonical case).

Minimal direct path: between any two routers in $DF(a, p, h)$ there exists a minimal direct path of length at most 3:

1. At most 1 local link within the source group (to reach the router that has the global link to the destination group).
2. At most 1 global optical link to the destination group.
3. At most 1 local link within the destination group (to reach the router connected to the target compute node).

Hence the diameter (between routers) is at most 3, plus the one hop from compute node to its router on each end if measured node-to-node.

Valiant routing (indirect): if the direct path is busy or would cause a collision, an indirect path is used. It is implemented as a concatenation of two direct paths via a randomly chosen intermediate router:

1. From source, route directly to a random intermediate router (possibly in any group).
2. From the intermediate router, route directly to the destination.

This randomisation avoids deterministic conflicts at the cost of doubling the worst-case hop count (path length up to 6). Valiant routing works for any topology, with provable bounds on expected complexity. It is one of the key reasons Dragonfly is the mainstream modern HPC interconnect: it gracefully handles adversarial communication patterns.

### Design parameter: global link assignment

An important design parameter beyond $(a, p, h)$ is which specific router in one group is connected by a global link to which specific router in another group. The choice affects properties like the bisection width but does not change $G$, $S$, $N$, or the basic routing argument. The slides show $DF(4, 2, 2)$ with one possible global-link arrangement (the 9 groups arranged in a circle with global links forming a complex graph among them).

### Properties summary

Hierarchical: 3 levels (router / group / system).

Hybrid link technology: cheap metallic local + expensive optical global, matching physical-distance constraints.

Virtual router abstraction: each group behaves like a single high-radix router with $a \cdot h$ external ports, simplifying reasoning about inter-group routing.

Very low diameter (between routers): at most 3 hops on a direct path, with the canonical intra-group complete-graph design.

Scalability: the number of groups is bounded by $G_{a,p,h} = a \cdot h + 1$, the total size by $N = (a \cdot h + 1) \cdot a \cdot p$. To grow further one increases $a$, $p$, or $h$ (i.e., uses higher-radix routers).

Adaptive routing via Valiant: deterministic minimal-direct + randomized indirect Valiant fallback handles congestion.

Relationship to other modern topologies: Dragonfly is one of three families (Clos topologies, fat trees, Dragonfly) that dominate parallel HPC interconnects today. All three are based on high-radix routers and are often confused in the literature. HPE's Slingshot is the most recent contribution in the Dragonfly family.

### Potential exam questions

The questions below match the lecturer's proof-heavy, definition-precise style.

1. Define the canonical Dragonfly topology $DF(a, p, h)$. Give the meaning of each of the three parameters, the three levels of the hierarchy, and describe the structure of a single group.
2. Derive the formula for the maximum number of groups $G_{a,p,h} = a \cdot h + 1$. Justify the "+1".
3. Derive the formulas for the total number of routers $S_{a,p,h}$ and total number of compute nodes $N_{a,p,h}$ in $DF(a, p, h)$.
4. How many ports of each type (metallic for compute nodes, metallic intra-group, optical global) does a single router in $DF(a, p, h)$ have? How many total metallic local links and global optical links does the system have?
5. What does it mean that each group acts as a "virtual router"? How many external ports does this virtual router have?
6. For $DF(4, 2, 2)$: compute $G$, $S$, $N$, the number of metallic local links, and the number of global optical links. How many ports does each group expose as a virtual router?
7. Describe the minimal direct routing in $DF(a, p, h)$ when the intra-group network is a complete graph. What is the maximum number of hops between any two routers, and how does the path decompose?
8. What is Valiant routing? Why is it needed in addition to direct routing in $DF(a, p, h)$? What is its worst-case hop count compared to direct routing?
9. The slides note that the assignment of global links between specific routers in different groups is "an important design parameter". Which derived property does this assignment affect, and which derived properties does it not affect (i.e., $G$, $S$, $N$)?
10. Why is Dragonfly designed as a hierarchy with two link types (metallic local + optical global)? Relate the answer to physical/technological constraints on cable distances.
11. Why are Dragonfly topologies (together with Clos and fat trees) called "high-radix" interconnects? What hardware enabled their rise in modern HPC systems?
12. Compare Dragonfly to a flat fat tree of the same node count $N$. Discuss qualitatively the differences in cabling (optical vs metallic) and in routing.