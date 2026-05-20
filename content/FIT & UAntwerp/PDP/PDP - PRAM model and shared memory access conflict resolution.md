
> [!tldr] First 5 minutes of hell
> PRAM (Parallel RAM) is a abstraction computational model for a parallel computer. It abstracts:
> - all $p$ RAM processors are synchronized with the same clock
> - each $p_i$ accesses the shared memory in constant $O(1)$ time (not taking synchronization and communication overhead into account)
> - three types of operations:
> 	- global read
> 	- local computation
> 	- global write
> - two time models:
> 	- unit time model (each operation takes 1 time unit)
> 	- global time model (local computation takes 1 time unit, R/W take $d>1$ time unit, still constant)
> - how are conflicts handled?
> 	- EREW
> 	- CREW
> 	- CRCW (three options: priority, arbitrary, common)


- = Parallel Random Access Machine
- a different distinction:
	- a RAM in PRAM means Random Access Machine (as in Von Neumann's architecture)
	- a "normal" RAM (as in e.g. DRAM) is Random Access Memory
- a PRAM is an abstraction model on how to access memory in a parallel computer (with multiple processors)
	- the key abstraction: a processor can access any shared memory (SM) cell in $O(1)$ time (unless conflict occurs)
		- ignoring physical constraints (latency, technology etc.)
	- there is a big difference between a local operation and an operation with remote access to the shared memory (it could be 100x slower)
	- this is the most comfortable model to implement (we just care about splitting the work onto multiple processors and not caring about the memory locality) - so this PRAM model is often the starting point in designing our parallel algorithm
### 3 types of operations
- the idea is having a cycle of operations in a loop: all processors read (R), all compute locally (L) and all write back (W)
	- simultaneously and synchronously
- the notation `⟨RLW⟩^k` denotes `k` iterations of the sequence READ, LOCAL, WRITE

- operations
	- Global read from a shared memory cell (R)
	- Local computation (L) - on local registers/local memory
	- Global write operation to a shared memory cell (W)
### Timing models
- when designing the algorithm in PRAM, we can follow two timing models
	- unit time model: each operation R, L, or W takes time 1
	- global time model: L takes time 1 and R/W take constant time `d > 1`
		- this is more realistic (because there is a big difference between local operation in the CPU (L) and remote shared memory access (R, W), so the constant `d` approximates the delays)
### Shared access memory conflict handling
- EREW (Exclusive Read, Exclusive Write) - most restrictive model
	- two processors cannot access a single memory cell at some time (not for R, not for W)
	- it's my responsibility is to ensure that the restrictions are respected
	- it is the most restrictive, but surprisingly, quite good for many use-cases
	- example use-cases:
		- algorithms, where at every parallel step, each shared memory is touched by at most one CPU
		- e.g. element-wise operations (each CPU reads own elements and writes own result)
- CREW (Concurrent Read, Exclusive Write)
	- concurrent reading is not forbidden (it is relaxed), but writing is exclusive
	- use-case: matrix multiplication (same reading of rows, but writing is exlusive)
- CRCW (Concurrent Read, Concurrent Write)
	- multiple approaches to resolve the conflict:
		- Priority CRCW: priority queue of the request (the CPU with higher priority is "stronger" and gets priority)
		- Arbitrary CRCW: random processor can write, the code must handle this uncertainty (the code must produce correct results regardless of the writer) - again many use-cases perform well with this approach
		- Common CRCW - it's possible to write simultaneously, if the value to be written is the same (it's my responsibility as a programmer is to ensure that)
			- if not, the machine will have undefined behavior

- any algorithm designed for EREW runs correctly on CREW and CRCW (it gets more permissive)
	- this doesn't work the other way
### Connection to real architectures
- PRAM model maps to shared-memory parallel systems:
	- UMA - [[PDP - 1. lecture - final#Memory organization]]
		- one shared memory for all CPUs, each one of them has access to the whole address space
	- SMP = symmetric multiprocessor (several CPUs sharing the same shared memory), usually servers
### Potential exam questions
1. Define the PRAM model. What are its components, and what are the three types of operations processors can execute?
2. Explain the difference between the unit time model and the global time model in PRAM. Why is the global time model more realistic?
3. What does "RAM" stand for in PRAM, and why is this distinction important?
4. Describe the three PRAM submodels (EREW, CREW, CRCW) and their restrictions on shared memory access.
5. For each of the three CRCW variants (Priority, Arbitrary, Common), explain the write conflict resolution mechanism and state who bears responsibility for correctness (hardware vs. programmer).
6. Give an example of a situation where EREW-PRAM is sufficient. Why might this restrictive model still be practical?
7. In Common-CRCW-PRAM, what happens if two processors attempt to write different values to the same cell? Whose responsibility is it to prevent this?
8. Rank the PRAM submodels by computational power. Can an algorithm designed for CREW run unmodified on an EREW machine? Why or why not?
9. How does the PRAM model relate to real shared-memory architectures (UMA, CC-NUMA)?
10. What is the key abstraction/idealization of PRAM, and what physical constraints does it ignore?