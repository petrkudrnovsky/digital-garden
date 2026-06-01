
> [!tldr] First 5 minutes in hell
> I have a $\sqrt{p}\times \sqrt{p}$ grid of processes and each process holds one block of A, one block of B and one block of C. The goal is to compute C by multiplying A and B, block-by-block without needing more memory then it initially started with.
> - it could be scalable, the naive approach needs to load the full A row and full B column to perform the multiplication, which is infeasible for large matrices (to fit the data onto one computing node)
> 	- I am usually going distributed because I don't have that much memory in a single node, and the naive approach then defeats it's purpose
> 	- here the memory is exactly 3 blocks per process, nothing more
> 
> The flow:
> - blocks of A are flowing across rows, blocks of B are flowing across columns and at each iteration, each process multiplies it's current block A with B and saves the result to C
> - each submatrix of A and submatrix of B visits each process at exactly the right time, when the process needs them
> - no process stores more then initially allocated
> - this flow is called "systolic" (as the submatrices flow through the matrix of processes like a blood in the heart)
> 
> Algorithm:
> - starts with checkerboard mapping of A, B, C matrices on a virtual 2D torus $K(\sqrt{p}, \sqrt{p})$ - there are cyclic shifts (torus is ideal)
> - two phases:
> 	- initial skew:
> 		- rotate all submatrices $A$ in row $i$ by $i$ positions
> 			- row 0 will not rotate
> 			- row 1: each matrix will rotate by 1 position
> 			- row 2: by two positions etc.
> 		- rotate all submatrices $B$ in a column $j$ by $j$ positions
> 		- this way, at the start of the second phase, each process will hold a correct pair of $P_{i,j}$ - they can be multiplied together
> 	- iterations:
> 		- each processor multiplies it's submatrices A and B and add the result to C
> 		- rotate all matrices by one position (A's along rows, B's along columns)
> 
> Correctness:
> - this algorithm guarantees that at every tick, the A and B blocks held by $P_{i,j}$ share a middle index $m$ (the column index of A matches the row index of B)
> 	- every value of $m$ from $0$ to $\sqrt{p-1}$ shows up exactly once
> 
> Properties:
> - memory-optimal
> - time complexity - $\sqrt{p}$ - startup latency, communication (shifts) + local multiplications
> - scaling is $N$ (which is the same as the naive approach, but the naive approach is memory-inefficient)
> 
> Comparison to the Fox's algorithm:
> - works in the similar sense, needs 4 matrices per process, but the matrix A never moves (only the matrix B), simplifying the cleanup
> - asymptotic time complexity is similar to the Cannon's

![[Pasted image 20260525112120.png]]

## Problem definition

Given two dense $(\sqrt{N} \times \sqrt{N})$-matrices $A$ and $B$, compute the product $$ C = A \times B. $$ We use the standard "three-loop" definition: an output element $c_{i,k}$ is computed as the dot product of row $i$ of $A$ and column $k$ of $B$: $$ c_{i,k} = \sum_{j=0}^{\sqrt{N}-1} a_{i,j} , b_{j,k}. $$ Strassen-type recursive divide-and-conquer algorithms are out of scope - only the cubic standard algorithm is considered. The matrices $A$, $B$, $C$ are checkerboard-block-mapped on a virtual 2-D mesh $M(\sqrt{p}, \sqrt{p})$, i.e., each process $P_{i,j}$ initially holds one $(\sqrt{N/p} \times \sqrt{N/p})$-submatrix $A_{i,j}$, one $B_{i,j}$, and produces one $C_{i,j}$.

## Motivation: why a naive standard algorithm is not enough

The naive standard MMM algorithm (`StandardMMM`, the prior algorithm in the same lecture):

- Ph.1: Each row of processes performs an AAG (all-to-all gather) of its $A$ submatrices - so every process in row $i$ ends up with the full row of $A$ submatrices.
- Ph.2: Each column of processes performs an AAG of its $B$ submatrices - every process in column $k$ ends up with the full column of $B$ submatrices.
- Ph.3: Each $P_{i,k}$ locally computes $C_{i,k} = \sum_{j=1}^{\sqrt{p}} A_{i,j} B_{j,k}$. Assuming SF 2-D mesh, its complexity is $$ T(N, p) = O!\left(\frac{N}{p}\sqrt{p} + \frac{N}{p}\sqrt{N}\right), \qquad E(N, p) = \Theta!\left(\frac{\sqrt{N}}{\sqrt{N} + \sqrt{p}}\right), $$ with optimal scalability $\psi_2(N) = N$. But: it is memory-inefficient. It requires globally $\sqrt{p}$ times more memory than the sequential algorithm, because each process accumulates a full stripe of $A$ and a full stripe of $B$ before doing the multiplication. For very large matrices - which is the only reason to use distributed memory in the first place - this is fatal. As $p$ grows, the per-process memory exceeds any fixed capacity.

> Two reasons exist to go distributed: either the sequential time complexity is unacceptable, or the matrices do not fit in a single memory. The standard algorithm helps with the first but worsens the second. This motivates an algorithm that achieves the same asymptotic time complexity while remaining memory optimal - i.e., requiring per process only the capacity to store one $A$ submatrix, one $B$ submatrix, and one $C$ submatrix. Such algorithms are called systolic.

## The systolic principle

A systolic algorithm reshuffles the submatrices of $A$ and $B$ through the processes in such a way that:

- At every step, every process holds exactly one $A$ submatrix and one $B$ submatrix that it can immediately multiply and accumulate into its $C$ submatrix.
- Each submatrix of $A$ and each submatrix of $B$ visits each process exactly when that process needs it.
- No process ever needs to store more than its initially allocated capacity (one $A$, one $B$, one $C$ submatrix). The data thus "flow" through the system in rhythm, much like blood circulates in a body - hence the name systolic.

## Cannon's algorithm: structure

Starting state: checkerboard mapping of $A, B, C$ on a virtual 2-D torus $K(\sqrt{p}, \sqrt{p})$ (the algorithm naturally requires a torus because of the cyclic shifts; a mesh can simulate this but the torus is the ideal topology). The algorithm has two prologue phases (initial skew) followed by a main loop of $\sqrt{p}$ iterations. Prologue:

- Ph.1: For all $i = 0, \ldots, \sqrt{p}-1$ in parallel, rotate the submatrices of $A$ in row $i$ by $i$ positions leftward (cyclic shift).
- Ph.2: For all $j = 0, \ldots, \sqrt{p}-1$ in parallel, rotate the submatrices of $B$ in column $j$ by $j$ positions upward (cyclic shift). After these two rotations, every process $P_{i,j}$ holds a pair $(A_{i, (i+j) \bmod \sqrt{p}}, B_{(i+j) \bmod \sqrt{p}, j})$. These have matching middle index $(i+j) \bmod \sqrt{p}$, so they can be multiplied together correctly. Main loop (Ph.3):
- Repeat $\sqrt{p}$ times:
    - For all processors in $K(\sqrt{p}, \sqrt{p})$ in parallel: multiply the currently held $A$ and $B$ submatrices and add the result to $C$.
    - For all $i = 0, \ldots, \sqrt{p}-1$ in parallel: rotate the submatrices of $A$ in row $i$ by one position leftward.
    - For all $j = 0, \ldots, \sqrt{p}-1$ in parallel: rotate the submatrices of $B$ in column $j$ by one position upward. After $\sqrt{p}$ iterations of the main loop, each process has accumulated all $\sqrt{p}$ partial products needed for its assigned $C_{i,j}$, and the algorithm is complete.

> Cannon's algorithm is very regular: after the asymmetric prologue, every iteration looks the same - multiply, shift A leftward by 1, shift B upward by 1.

## Correctness

The key invariant: after Phase 1 and Phase 2 of the prologue, process $P_{i,j}$ holds $A_{i, (i+j) \bmod \sqrt{p}}$ and $B_{(i+j) \bmod \sqrt{p}, j}$ - a matching pair with the same middle index. Reason: before Phase 1, $P_{i,j}$ held $A_{i,j}$. Rotating row $i$ leftward by $i$ positions moves $A_{i, m}$ to the process $P_{i, m-i \bmod \sqrt{p}}$. So $P_{i,j}$ receives $A_{i, (j+i) \bmod \sqrt{p}}$. Symmetrically, before Phase 2, $P_{i,j}$ held $B_{i,j}$. Rotating column $j$ upward by $j$ positions moves $B_{m, j}$ to $P_{m-j \bmod \sqrt{p}, j}$, so $P_{i,j}$ receives $B_{(i+j) \bmod \sqrt{p}, j}$. The two middle indices match. After each iteration of the main loop, both $A$ and $B$ submatrices held by $P_{i,j}$ have their middle index incremented by $1 \pmod{\sqrt{p}}$ - because $A$ moves leftward by 1 (column index of $A$ submatrix decreases by 1 in the row stream, which means $P_{i,j}$ now sees the $A$ submatrix that previously had middle index $(i+j+1) \bmod \sqrt{p}$), and $B$ moves upward by 1 (row index of $B$ submatrix decreases by 1 in the column stream, which means $P_{i,j}$ now sees the $B$ submatrix whose middle index is $(i+j+1) \bmod \sqrt{p}$). So across $\sqrt{p}$ iterations, the middle index runs through all values $0, 1, \ldots, \sqrt{p}-1$ exactly once. The local accumulation $$ C_{i,j} \leftarrow C_{i,j} + A_{i, \text{mid}} \cdot B_{\text{mid}, j} $$ therefore visits every $\text{mid}$ exactly once, giving the correct mathematical result $$ C_{i,j} = \sum_{\text{mid}=0}^{\sqrt{p}-1} A_{i, \text{mid}} \cdot B_{\text{mid}, j}. $$ This is exactly the block formulation of matrix multiplication.

## Properties

Time complexity. On an all-port WH hypercube $Q_{\log p}$: $$ T_{\mathrm{Cannon}}(N, p) \doteq t_s \sqrt{p} + \frac{N}{\sqrt{p}} t_m + O!\left(\frac{\sqrt{N}^3}{p}\right). $$ The terms are:

- $t_s \sqrt{p}$ - startup latency: $\sqrt{p}$ communication steps, each with constant setup.
- $\dfrac{N}{\sqrt{p}} t_m$ - data transfer: $\sqrt{p}$ shifts of an $\frac{N}{p}$-sized block each (the per-step submatrix has $N/p$ elements, summed over $\sqrt{p}$ steps gives $N/\sqrt{p}$).
- $O!\left(\dfrac{\sqrt{N}^3}{p}\right)$ - the local arithmetic: $\sqrt{p}$ submatrix multiplications, each costing $(N/p)^{3/2} = \sqrt{N}^3 / p^{3/2}$, summed gives $\sqrt{N}^3/p$. Scalability: $$ \psi_2(N) = N $$ which is optimal scalability (same as the naive standard MMM). Memory optimality. Within the course of the algorithm, there is no replication or redundancy of data. Every process holds exactly one $A$ submatrix, one $B$ submatrix, and one $C$ submatrix at all times. The total distributed memory used is therefore $3 \times$ the size of the input (the three matrices) - the minimum possible. This contrasts sharply with the naive standard algorithm, which would require $\sqrt{p}$ times more. Regularity. Apart from the asymmetric prologue, the main loop consists of identical iterations: multiply + shift left + shift up. The shifts are unit cyclic shifts in row and column dimensions of the torus, which are the most basic and most efficiently supported communication patterns on 2-D tori. MPI provides direct support via `MPI_Sendrecv_replace` combined with `MPI_Cart_shift` on a Cartesian (toroidal) communicator. Ideal topology. Cannon's algorithm is naturally a 2-D torus algorithm, because the cyclic shifts wrap around. On a mesh (without wraparound) the shifts can be simulated, but the torus is the ideal topology.

## Comparison with Fox's algorithm (Broadcast-Multiply-Roll)

Fox's algorithm (sometimes called BMR) is an alternative systolic-style algorithm. Its key differences:

- It does not perform the initial skewing of $A$ and $B$. The initial checkerboard mapping is kept as is.
- In iteration $k$, the diagonal $A$ submatrices $A_{i,(i+k) \bmod \sqrt{p}}$ are broadcast within each row.
- Each process then multiplies the received $A$ with its local $B$, adds to $C$, and then rotates $B$ upward by 1 in its column. This relaxes memory optimality: each process needs space for 4 submatrices (its local $A$, an incoming broadcast $A$ buffer, its rotating $B$, and its accumulating $C$) instead of 3. The advantage is simpler restoration of the original mapping at the end, because the $A$ submatrices never move - they are only copied via broadcast. The asymptotic time complexity and scalability are similar to Cannon's.

> Cannon vs Fox: Cannon is memory-optimal (3 submatrices per process) but more involved to restore to the original mapping. Fox needs 4 submatrices per process but the $A$ matrix never moves, simplifying cleanup.

## Potential exam questions

- Define the dense MMM problem and state which definition of matrix multiplication is used (the standard cubic one, not Strassen).
- Why is the naive `StandardMMM` (AAG of rows of $A$ and columns of $B$, then local product) unsuitable for very large matrices? State the memory overhead it imposes in terms of $\sqrt{p}$.
- Define what a systolic algorithm is. What invariant about per-process memory must such an algorithm preserve?
- Describe the initial mapping for Cannon's algorithm. Why is the natural topology a 2-D torus $K(\sqrt{p}, \sqrt{p})$ rather than a mesh $M(\sqrt{p}, \sqrt{p})$?
- Write out the four phases of Cannon's algorithm: the two prologue rotations and the main loop. State exactly how many times each iteration is repeated, and what its three steps are.
- After the prologue (Ph.1 + Ph.2) of Cannon's algorithm, what pair of submatrices does process $P_{i,j}$ hold? Justify why this pair has the correct matching middle index for matrix multiplication.
- Prove the correctness of Cannon's algorithm: show that after $\sqrt{p}$ iterations of the main loop, process $P_{i,j}$ has accumulated the correct value $C_{i,j} = \sum_{m=0}^{\sqrt{p}-1} A_{i,m} B_{m,j}$.
- State the time complexity of Cannon's algorithm on an all-port WH hypercube $Q_{\log p}$. Identify and explain the three terms (startup latency, data transfer, local arithmetic).
- What is the asymptotic scalability function $\psi_2(N)$ of Cannon's algorithm? How does it compare to the naive `StandardMMM`?
- State and justify the memory optimality property of Cannon's algorithm. How much per-process memory does the algorithm require, and how does this compare to the naive `StandardMMM` and to Fox's algorithm?
- Why is Cannon's algorithm called regular? Identify the structural difference between the prologue and the main loop, and explain why this matters for implementation.
- Compare Cannon's algorithm to Fox's Broadcast-Multiply-Roll algorithm. State at least three differences: per-process memory requirement, structure of the initial mapping, and the cost of restoring the original mapping at the end.
- Explain the relationship between Cannon's algorithm and the cyclic shift permutation on a 2-D torus. Which MPI functions naturally support the communication primitives the algorithm needs?