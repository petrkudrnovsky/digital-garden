
> [!tldr] First 5 minutes in hell
> Clos topology is a way how to connect $N$ computers so that each can talk to others (in more efficient way than crossbar switch ($N$ computers in a matrix, having $N^2$ crosspoints - which does not scale)). 
> 
> $CL(m,n,r)$ is a indirect 3-degree topology to indirectly connect $N$ computers.
> - $n$ computers connected to a first-stage switch
> - $r$ number input/output routers
> - $m$ number of intermediate routers
> - $N=n\cdot r$
> ![[Pasted image 20260522173638.png]]
> 
> Variant 1: strictly non-blocking ($m\ge 2n-1$)
> - at any moment, computer A can connect to computer B without disturbing any existing connections
> - proof: existing connections can consume at most $(n-1)+(n-1)$ connections, so if we have more intermediate router than that, there will always be some free router to use
> 	- because at the input router A one of the input port is the calling computer and the other $n-1$ could occupy the rest of the intermediate routers (similarly on the other side)
> - with this, we are able to handle the worse-case request arrivals without ever moving a connection/cell
> 
> Variant 2: rearrangable ($m\ge n$)
> - we are able to connect each input to exactly one unique output at once (perfect mapping for $m=n$) collision-free
> 	- so for $m=n$, all permutations of inputs/outputs are possible, but they need to be determined globally with knowledge of all $N$ source-destination plans
> 	- but if a new request comes after the establishing of the routes, we might need to reorganize the connections
> 		- a new rearrangement surely exists (Hall's marriage theorem), but it may need to be rearranged globally
> - good for request batches


### Definition of $CL(m, n, r)$

The `3-stage Clos topology` $CL(m, n, r)$ is an `indirect 3-degree topology` implementing an $N \times N$ interconnection network for $$ N = r \cdot n. $$ It has three stages of switches/routers:

- `1st stage` (input stage): $r$ input routers, each of size $n \times m$ ($n$ inputs, $m$ outputs)
- `2nd stage` (intermediate/middle stage): $m$ intermediate routers, each of size $r \times r$ ($r$ inputs, $r$ outputs)
- `3rd stage` (output stage): $r$ output routers, each of size $m \times n$ ($m$ inputs, $n$ outputs)

Connectivity:

- Each input router has its $m$ outputs connected, one each, to the $m$ middle routers (output $k$ of input router $j$ connects to input $j$ of middle router $k$)
- Each middle router has its $r$ outputs connected, one each, to the $r$ output routers
- Computing nodes attach to the $n$ inputs of each input router and to the $n$ outputs of each output router

Therefore three parameters $(m, n, r)$ fully determine the topology; the port count $r$ on middle switches is derived (each middle switch connects to all $r$ input switches on its input side and all $r$ output switches on its output side). Example: $CL(5, 3, 4)$ has 4 input routers $3 \times 5$, 5 middle routers $4 \times 4$, and 4 output routers $5 \times 3$, implementing a $12 \times 12$ interconnection network.

> Clos topologies are `indirect` - only the boundary switches attach to computing nodes; the middle stage is pure routing.

### Variants: strictly nonblocking vs rearrangeable

Different choices of $(m, n, r)$ yield Clos networks with different properties. The two main variants are distinguished by the relationship between $m$ and $n$:

`Strictly nonblocking`: $m \geq 2n - 1$. Any new connection from any free input to any free output can always be made `on demand` without disturbing existing connections.

`Rearrangeable`: $m \geq n$. A full permutation of $rn$ inputs to $rn$ outputs can always be realized collision-free, but `only if the entire permutation is known in advance` - already-routed connections may need to be rerouted as new requests arrive.

The practical distinction:

- Strictly nonblocking handles `dynamic, on-demand traffic` (like a telephone exchange where calls arrive one at a time)
- Rearrangeable handles `static, preplanned permutation routing` (like a batch scheduler that computes all routes before they begin)

Strictly nonblocking requires roughly twice as many middle switches as rearrangeable.

### Strictly nonblocking 3-stage Clos (Theorem 28)

Theorem 28: for $m \geq 2n - 1$, $CL(m, n, r)$ is `strictly nonblocking` - i.e., it can collision-free connect any free input port with any free output port regardless of existing connections.

Proof (worst-case counting argument):

- Consider a request to route from a free input port on some input router $A$ to a free output port on some output router $B$
- The input router $A$ has $n$ input ports; the $n - 1$ remaining inputs might already be in use, each consuming one middle switch. So at most $n - 1$ middle switches are "blocked on the input side"
- Symmetrically, the output router $B$ has $n$ output ports; the $n - 1$ remaining outputs might already be in use, each consuming one middle switch. So at most $n - 1$ middle switches are "blocked on the output side"
- In the worst case, these two sets are disjoint, blocking at most $(n - 1) + (n - 1) = 2n - 2$ middle switches
- If $m \geq 2n - 1$, then at least one middle switch is free and connects both $A$ and $B$, allowing the new connection

Example: $CL(5, 3, 4)$ has $m = 5 = 2 \cdot 3 - 1 = 2n - 1$, so it is strictly nonblocking at the minimal cost.

### Rearrangeable 3-stage Clos (Theorem 29)

Theorem 29: the 3-stage $CL(m, n, r)$ is `rearrangeable` for $m \geq n$.

Proof (for $m = n$, the minimal case, using Hall's marriage theorem):

- Consider a full permutation of the $rn$ inputs to the $rn$ outputs (known in advance)
- The problem of constructing all collision-free connections is equivalent to `edge n-coloring` of an $n$-ary bipartite routing graph on $(r, r)$ vertices: one side represents input routers, the other output routers, and each desired connection is an edge colored by the middle switch that will carry it
- `Hall's marriage theorem` guarantees that a bipartite graph with equal degrees on both sides admits a `perfect matching`. Iteratively extracting perfect matchings $n$ times yields an edge $n$-coloring

Consequence: $n$ middle switches suffice to route any permutation, provided rerouting is allowed.

The lecturer referenced `AG2` (Algorithms and Graphs 2) where Hall's theorem is covered; the core statement is that for a bipartite graph $(A, B, E)$, a perfect matching saturating $A$ exists iff for every subset $S \subseteq A$, $|N(S)| \geq |S|$. Here the graph is $n$-regular on both sides, which satisfies the condition trivially.

### Summary of parameters and costs

Comparing the two variants for an $N \times N$ Clos network ($N = r \cdot n$):

For strictly nonblocking ($m = 2n - 1$):

- Middle switches: $2n - 1$
- Middle-switch ports: each $r \times r$
- Total middle-stage crosspoints: $(2n - 1) \cdot r^2$
- Handles `any arrival pattern` dynamically

For rearrangeable ($m = n$):

- Middle switches: $n$
- Middle-switch ports: each $r \times r$
- Total middle-stage crosspoints: $n \cdot r^2$
- Handles `preplanned permutations` only

A pure crossbar would use $N^2 = (r n)^2$ crosspoints. For large $N$, both Clos variants are dramatically cheaper than a crossbar while preserving the full input-to-output reachability.

### Multi-stage Clos topologies

The 3-stage Clos can be generalized `recursively` into a `multi-stage Clos topology`: the middle stage's $r \times r$ routers are themselves replaced by 3-stage Clos networks. This produces 5-stage, 7-stage, ... networks, each level reducing the individual switch port count at the cost of more stages.

Multi-stage Clos topologies allow arbitrarily large networks to be built from small, uniform router components.

### Historical and contemporary importance

`Original use (1950s)`: Charles Clos proposed the topology for `telephone exchanges`. Telephone calls arrive one at a time, so the strictly nonblocking variant was the natural fit - connecting any free caller to any free callee without disturbing existing calls.

`Modern resurgence`: with the `emergence of large data centers` and the `advent of high-radix routers` (low-cost routers with a large number of ports, made possible by modern silicon), Clos topologies have seen a strong come-back. Today they are used in `big data centers` to connect servers and racks using `fiber optic links` to `data center spines`.

The combination of:

- Cheap, high-port-count routers (economic enabler)
- Proven nonblocking / rearrangeable properties (algorithmic enabler)
- Recursive multi-stage construction (scalability enabler)

makes Clos the backbone of modern hyperscale networks.

Related contemporary topologies are `fat trees` and `Dragonfly` - all three are often interchangeably confused in the literature, but all are based on high-radix routers. The most recent variant is `HPE's Slingshot`.

### Summary

- $CL(m, n, r)$ is an `indirect 3-stage topology` with $r$ $n \times m$ input routers, $m$ $r \times r$ middle routers, $r$ $m \times n$ output routers, realizing an $N \times N$ network for $N = r n$
- `Theorem 28` (strictly nonblocking): $m \geq 2n - 1$ suffices for on-demand collision-free connection of any free input to any free output; proof by worst-case counting of at most $2n - 2$ blocked middle switches
- `Theorem 29` (rearrangeable): $m \geq n$ suffices for preplanned permutation routing; proof reduces to edge $n$-coloring of an $n$-regular bipartite graph via Hall's theorem
- Strictly nonblocking costs $\sim 2n - 1$ middle switches vs $n$ for rearrangeable - roughly double
- Recursive `multi-stage Clos` topologies replace middle $r \times r$ routers with 3-stage sub-Clos networks
- `Historical origin`: 1950s telephone exchanges
- `Modern resurgence`: high-radix routers enable Clos as the dominant topology in large data centers (along with fat trees and Dragonflies)

### Potential exam questions

1. Give the definition of the 3-stage Clos topology $CL(m, n, r)$. What are the sizes of the routers in each of the three stages? What is the total number of computing nodes?
2. Why is $CL(m, n, r)$ called an `indirect` topology? Which stages carry computing nodes?
3. For $CL(5, 3, 4)$: how many input, middle, output routers are there? What are their sizes? How many computing nodes does the network support?
4. Define `strictly nonblocking` and `rearrangeable` Clos networks. What is the practical difference between them?
5. State and prove Theorem 28: for $m \geq 2n - 1$, $CL(m, n, r)$ is strictly nonblocking. Walk through the worst-case counting argument showing that at most $2n - 2$ middle switches can be blocked.
6. State and prove Theorem 29: for $m \geq n$, $CL(m, n, r)$ is rearrangeable. How does the proof use Hall's marriage theorem? What is the equivalent edge-coloring problem?
7. Why does rearrangeable routing require the full permutation to be known in advance, whereas strictly nonblocking does not?
8. Compare the cost (number of middle switches) between strictly nonblocking and rearrangeable Clos networks realizing the same $N \times N$ interconnection network.
9. How does a Clos network compare to a pure $N \times N$ crossbar in terms of total crosspoint count? Why is Clos dramatically cheaper for large $N$?
10. Describe `multi-stage Clos topologies`. How does the recursive construction work, and why is it useful?
11. What is the historical origin of Clos topologies? In what technological context were they originally deployed?
12. Why have Clos topologies seen a resurgence in recent years? What technological advance enabled this?
13. Compare Clos topologies with fat trees and Dragonflies. What do all three have in common?