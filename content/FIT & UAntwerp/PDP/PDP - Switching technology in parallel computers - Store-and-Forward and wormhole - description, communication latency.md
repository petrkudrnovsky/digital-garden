### Scope of the question

This question asks about how a single message is physically moved from a source node to a destination node along the interconnection network of a parallel computer. The two canonical switching techniques are store-and-forward (SF) and wormhole (WH). The answer must cover (1) the underlying computing node architecture that sets the context, (2) the communication performance metrics used in the latency formulas, (3) the description and latency of SF, (4) the description and latency of WH, and (5) the trade-offs between them.

### Computing node architecture

Each node of a parallel computer consists of a processor, local memory, possibly other functional units, and a router. The router is the component that handles all communication. Internally the router is built from a crossbar switch, routing and arbitration logic, and a set of link controllers (LC). It connects to the local processor and memory through internal injection channels (memory $\to$ router) and ejection channels (router $\to$ memory). It connects to neighboring routers through external input and output channels. Each channel has an associated buffer. The external channels define the structure of the interconnection network. Routers are classified by the number of channels that can be used in parallel:

- 1-port routers: at most one channel can be used at any moment
- $k$-port routers: up to $k$ channels can be used in parallel
- all-port routers: every channel can be used in parallel The port count appears as a parameter $k$ in every communication latency formula and bounds.

### Communication performance metrics

The latency formulas for both switching techniques are built from the following primitive quantities:

- $w$ - channel width: the number of bits a physical channel can carry in parallel
- $t_m$ - channel latency: inter-router latency of one phit (physical unit of transfer), in $[s/B]$
- $t_s$ - startup latency: software and hardware overhead at both source and destination for initializing the DMA transfer between memory and router, in $[s]$
- $t_r$ - routing latency: intra-router latency to make the routing decision during path construction, in $[s]$
- $t_w$ - switching latency: intra-router latency to switch a phit from an input buffer to an output buffer, in $[s/B]$ Two derived notions are then used:
- Network latency = conflict-free transmission time without the startup latency
- Communication latency = network latency + startup latency The key distinction is that $t_m$ is between routers, while $t_r$ and $t_w$ are inside a router. The packet size is denoted $\mu$ and the path length (number of hops) is denoted $\delta$.

### Store-and-forward (SF) switching: description

SF switching is also called packet switching. The key idea is that an entire packet must be received and stored before the router decides where to send it next. The mechanics are:

- A message is split into fixed-size packets
- A packet consists of flits, with a header flit at the front
- Each channel has input and output buffers sized for a whole packet
- Each packet is routed individually from source to destination
- One step (one hop) = transfer of one packet from an output buffer to the next input buffer
- The router only makes its routing decision after the whole packet has been stored in its input buffer The execution of one hop on the path therefore decomposes into: routing decision ($t_r$), then internal switching of all $\mu$ phits from input to output buffer ($\mu t_w$), then inter-router transfer of all $\mu$ phits ($\mu t_m$).

### SF: communication latency

A packet of size $\mu$ transmitted over distance $\delta$ takes time $$ t_{SF}(\mu, \delta) = t_s + \delta \left( t_r + (t_w + t_m) \mu \right). $$ The interpretation:

- $t_s$ is paid once, at the start, for DMA initialization on the source and destination
- the per-hop cost $t_r + (t_w + t_m)\mu$ is paid $\delta$ times - one routing decision plus full packet transfer at each router along the path The communication latency is proportional to the product $\delta \cdot \mu$. This product is the defining feature of SF and has the following consequences:
- designers must seek minimal routing and use low-distance networks
- SF is advantageous for short and frequent messages, because at any moment at most one channel from the whole path is busy (the packet sits in one buffer at a time), leaving the rest of the path available for other traffic
- the requirement to buffer the whole packet makes routers either more expensive and slower, or forces a hard limit on the packet size Simplified formula used in the rest of the course: $$ t_{SF}(\mu, \delta) \approx t_s + \delta \mu t_m. $$

### Wormhole (WH) switching: description

WH switching is also called cut-through switching. The key idea is that a packet does not need to be stored as a whole at each router - flits are forwarded as soon as the path is open. The mechanics are:

- Packets are split into flits
- Routers have small buffers for just one flit, not for whole packets
- The incoming header flit is cut through to the next router as soon as the routing decision has been made and the output channel is free
- Every subsequent flit is also cut through to the next router as soon as the output channel is free
- In the absence of conflicts, the packet is effectively pipelined through successive routers as a loose chain of flits
- The buffers along the chosen path are blocked for other communication requests
- If a header flit cannot proceed because the required output channel is busy, the whole chain of flits stalls and the flit buffers along the path are frozen The header flit acts as a worm that builds the path hop by hop; the body of the packet follows in pipeline fashion. WH switching is therefore contention-prone and deadlock-prone, but it allows routers to be simple, small, cheap, and fast.

> Because WH blocks entire paths on stalls, designing collective communication algorithms over WH networks requires careful deadlock-free path planning. The price of WH's speed is that a single stalled header can freeze a long chain of routers.

### WH: communication latency

A packet of size $\mu$ transmitted over distance $\delta$ takes time $$ t_{WH}(\mu, \delta) = t_s + \delta (t_r + t_w + t_m) + \max(t_w, t_m) \mu. $$ The two parts have distinct meanings:

- $\delta (t_r + t_w + t_m)$ is the cutting-through phase of the header flit. At each of the $\delta$ routers it makes a routing decision ($t_r$), switches internally ($t_w$, for a single flit), and crosses to the next router ($t_m$, for a single flit)
- $\max(t_w, t_m) \mu$ is the cost of consuming the rest of the packet at the destination. With both input and output flit buffers in the router, intra-router switching and inter-router transfer can overlap, so the bottleneck is the slower of the two. With only one-sided buffering, this term becomes $(t_w + t_m) \mu$ instead The crucial point is that WH latency is a sum of a distance-dependent term and a message-size-dependent term, not a product. WH is therefore almost insensitive to distance. Simplified formula used in the rest of the course: $$ t_{WH}(\mu, \delta) \approx t_s + \delta t_d + \mu t_m, \quad \text{where} \quad t_d = t_r + t_w + t_m. $$

### SF vs WH: side-by-side comparison

The defining algebraic difference is product versus sum:

- SF: $t_{SF} \approx t_s + \delta \mu t_m$ - distance and packet size are multiplied
- WH: $t_{WH} \approx t_s + \delta t_d + \mu t_m$ - distance and packet size are added This leads to opposite design profiles:
- SF: needs big router buffers (whole packets), few channels busy along the path at once, latency hurts long messages on long paths, but congestion behavior is benign - only one router on the path is active at a time, so the rest of the network stays free for other communication
- WH: needs only one-flit buffers per channel, the entire path is held while a packet streams through, latency is essentially distance-insensitive, but congestion and deadlock are serious problems because a stalled header freezes the whole pipeline In short: SF tolerates contention but pays for distance, WH ignores distance but pays for contention.

### Potential exam questions

- State and derive the formula for the communication latency of store-and-forward switching as a function of packet size $\mu$ and distance $\delta$. Explain the meaning of each term.
- State and derive the formula for the communication latency of wormhole switching. Why is WH said to be almost insensitive to distance?
- Compare SF and WH switching with respect to: (a) buffer requirements at routers, (b) sensitivity to path length, (c) sensitivity to contention. Justify each comparison with the latency formula.
- Define the parameters $t_s$, $t_m$, $t_r$, $t_w$, $t_d$, $w$, $\mu$, $\delta$. Which are intra-router and which are inter-router?
- Why does SF allow other traffic to share the path while a packet is in transit, while WH does not?
- Explain what happens in WH switching when a header flit cannot proceed because the next output channel is busy. What are the consequences for the rest of the packet and for other communication requests?
- Derive the WH latency formula in the case where routers have only one-sided flit buffering. Why does the $\max(t_w, t_m)$ become $t_w + t_m$ in that case?
- Why does the WH latency contain a $\max(t_w, t_m)$ term while the SF latency contains a $(t_w + t_m)$ term inside the per-hop cost?
- Under which workload (message-size and message-frequency profile) does SF outperform WH, and under which does WH outperform SF? Justify using the latency formulas.
- Define network latency and communication latency. Where does the startup latency $t_s$ enter the formulas, and why is it paid only once per message rather than once per hop?