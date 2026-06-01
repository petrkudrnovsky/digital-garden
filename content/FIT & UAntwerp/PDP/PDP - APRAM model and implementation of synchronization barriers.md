
> [!tldr] First 5 minutes of hell
> APRAM (Asynchronous PRAM) is also a abstract computational model for parallel computers. Apart from same-clock-synchronous PRAM, here each one of the $p$ processors has it's own clock ("own speed"). 
> 
> The operations are the same as in PRAM (global read, local computation and global write). 
> 
> APRAM computation = a sequence of global phases in which Ps work asynchronously and which are separated by barrier synchronizations. 
> - two processors cannot access the same memory cell within one global phase (if at least one performs a write operation into it - concurrent reads are allowed)
> - conflicting writes must be separated by barriers
> - barrier needs to be in between the producer (a processor writing something) and a consumer (a processor reading it) 
> 	- this way the value will always be written before it is read
> 
> Barrier synchronization takes $B(p)$ time, where $B(p)$ is non-decreasing function (with more Ps, the delay in the computation increases or stays the same).
> 
> Barrier implementations (having incoming and outgoing phase):
> - central counter
> 	- the counter is initiated to 0 and each incoming processor must increase it
> 	- $B(p) = \Theta(dp)$ - the increments are linear, since they are mutually exclusive
> 	- once the counter is equal to the number of processors, all are woken up and release (in the outgoing phase)
> - binary reduction tree
> 	- $B(p)=\Theta(d\ log(p))$  - because of the logarithmic organization of the processors (the tree is "pre-allocated" and the processors sit in the leaves of the binary tree), so both reduction and activation take logarithmic time instead of linear time
> - trade-off:
> 	- central counter is significantly slow for large $p$, but memory allocation is only $O(1)$ - only one atomic counter
> 	- binary reduction tree is more complex, is faster for large $p$ and takes up 

-  = Asynchronous Parallel Random Access Machine
- PRAM assumes that all processors execute synchronously under a global clock, but real systems (clusters, multi-core CPUs...) are asynchronous (each computational unit has it's own clock) - this is handled by APRAM (while still abstracting over a shared memory)
### Operations
- same operations as in [[PDP - PRAM model and shared memory access conflict resolution|PRAM model]] (R, L, W) on a shared memory
- the APRAM computation is defined as sequence of global phases, which are synchronized with barriers
	- within each single global phase, each processor independently executes its own sequence of R, L, and W operations at its own speed
	- as the processors within the phase run asynchronously, there is no guarantee that two processors will not write the same memory cell at the same time (different clocks, different speeds)
		- so the system in APRAM needs to be designed in a way that two CPUs cannot write into the same memory cell within one phase 
		- the conflicting writes must be prevented by separating them into different phases with barriers
### Barrier synchronization
A barrier is the sole synchronization mechanism in the APRAM model. It works as follows: each processor stops at a designated logical point in the program and waits until all `p` processors arrive at that point. Only then do all processors continue to the next global phase.

The criterion for barrier placement is **data dependence**: if one thread needs a result produced by another thread, a barrier must separate the producer phase from the consumer phase. Without the barrier, the consumer might read stale or partially written data.
- this is exactly the problem of concurrent writes

Every barrier introduces idle time - the fastest processor must wait for the slowest. Therefore minimizing the number of barriers while maintaining correctness is critical for APRAM performance.
### Performance
- L operation takes time 1
- R/W takes time $d>1$ 
- barrier synchronization takes time $B(p)$ 
	- $B(p)$ is a non-decreasing function of $p$
	- in practice: `B(p) = O(d log p)` or `B(p) = O(d log p / log d)`
	- assumptions: `2 ≤ d ≤ B(p) ≤ dp`
- the consecutive $k$ operations (Rs and Ws pipelined) have a additive complexity (not multiplicative)
	- complexity: $d+k-1$ 
	- instead of $k * d$, which would make logical sense ($k$ operations with the length of $d$)
	- why?
		- memory buses in the real world are pipelined, allowing the consecutive accesses to overlap in the pipeline, so each pipelined operation adds only 1 cycle instead of the full $d$ 
### Barrier implementation
##### 1. approach: central counter 
- a shared counter, which counts processes waiting idle on the barrier
- each process which arrives at the barrier increments the counter and if $counter < {Threshold}$, the process becomes idle (waiting)
	- when a $counter >= {Threshold}$, all idle processes are released
- $B(p) = \Theta(dp)$ (linear - each of the $p$ processes has to access the shared counter and increment it sequentially, and each access costs $d$)
	- $d$ - operation complexity
	- $p$ - number of processors/processes
- simple, but scales poorly
##### 2. approach: binary reduction tree
- we can make the barrier complexity logarthmic: with binary reduction trees (leaves = threads)
- reduction (incoming phase) and broadcast (outgoing phase) both take a logarithmic time
- after reaching the threshold, the processes are activated in the reverse order
- time complexity: $B(p)=\Theta(d\ log(p))$ 
	- accessing each level of the logarithmic-depth-tree costs $d$ 
	- trade-off for the better time complexity: it takes up more space ($O(p)$ memory and is more complex 
- how it works:
	- arriving processor sits in the first available leaf and signals his parent that he is there and is idle
	- the parent waits for both children to be full and then it also signals to it's parent that "he is full"
	- it goes like this all the way up to the root -> if root gets the signal that both of his direct children are "full", it signals that the barrier is full and could be released
	- $B(p)=\Theta(d\ log(p))$ 
		- the signal has to travel log(p) levels and d is the cost of the operation in the global time model
	- the outgoing phase:
		- the root flips the signal to release, signaling it to his children, those children signal it to its children (all the way up to leaves - which is logarithmic)
			- so this way it could be logarithmic (each flag of the inner node is written in a different memory location, so they could be switched and read in parallel)
		- "the root does not call 1000 processors one after another, it calls two inner nodes, which call their two inner nodes etc. and the number of calls is log_2(1000) is roughly 10 - huge difference"

- both barriers have incoming/outgoing flags
	- if the barrier is in the incoming phase, the processes keep coming and waiting on others (idling)
	- when the threshold is reached, the barrier switches to outgoing phase and releases all processes
### APRAM vs. PRAM comparison

|Property|PRAM|APRAM|
|---|---|---|
|Processor execution|Synchronous (global clock)|Asynchronous (no global clock)|
|Synchronization|Implicit (every step is synchronized)|Explicit (barrier synchronization)|
|Operation types|R, L, W|R, L, W (same)|
|LOCAL cost|1|1|
|R/W cost (unit model)|1|N/A (only global time model)|
|R/W cost (global model)|`d`|`d`|
|Pipelined consecutive R/W|Not modeled|`d + k - 1`|
|Barrier cost|0 (implicit)|`B(p)`|
|Access conflict within a step/phase|Defined by submodel (EREW/CREW/CRCW)|No conflicting W to same cell in same phase|
In PRAM, every step is implicitly synchronized - all processors execute the same type of operation (R, L, or W) at the same time. The cost of this synchronization is hidden. In APRAM, synchronization is explicit and has a measurable cost `B(p)`, making it a more realistic model for algorithm analysis on real machines.
### Practical relevance
The APRAM model maps naturally to shared-memory parallel programming with OpenMP, where:
- Threads execute asynchronously within parallel regions.
- Barriers are inserted explicitly (`#pragma omp barrier`) or implicitly (at the end of `for`/`sections` constructs).
- The programmer must ensure no data races within a parallel phase - this directly corresponds to the APRAM constraint that conflicting accesses to the same cell are forbidden within a single global phase.
### Potential exam questions
1. Define the APRAM model. How does it differ from PRAM in terms of processor execution and synchronization?
2. What are the three types of operations in APRAM? Are they the same as in PRAM?
3. What is the access constraint within a single APRAM global phase? Why is this constraint necessary given that processors are asynchronous?
4. Define barrier synchronization. What condition triggers the release of all processors?
5. What is the criterion for deciding where to place barriers in an APRAM program?
6. State the APRAM performance parameters: cost of LOCAL, single READ/WRITE, `k` consecutive READs/WRITEs, and barrier synchronization. Explain why consecutive accesses cost `d + k - 1` rather than `k × d`.
7. Describe the central counter barrier implementation. What is its time complexity and why?
8. Describe the binary reduction tree barrier implementation. Walk through both the incoming (reduction) and outgoing (broadcast) phases. What is its time complexity?
9. Compare the two barrier implementations in terms of time complexity, implementation complexity, and memory requirements.
10. Why does minimizing the number of barriers matter for APRAM performance? What is the trade-off?
11. What are the assumed bounds on `B(p)` in the APRAM model? What are the practical estimates?