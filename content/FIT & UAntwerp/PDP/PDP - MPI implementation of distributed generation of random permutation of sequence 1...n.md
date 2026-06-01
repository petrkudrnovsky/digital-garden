
> [!tldr] First 5 minutes in hell
> We want to permute $n$ number with $p$ processes. The array large enough not to fit in memory of the single machine.
> 
> ParaPerm algorithm:
> - each process owns $n/p$ numbers (identity permutation at first)
> - each $P_i$ sends each of its numbers to a randomly chosen process (could be sent to itself)
> - each $P_i$ randomly permutes the received numbers
> - all processes in parallel redistribute mutually the numbers so that each one of them owns exactly $n/p$ numbers
> 
> Naive implementation and why is it not effective?
> - go number by number, select a random receiver process and send this number to that process
> - why is it inefficient?
> 	- a lot of traffic (one message per sent number)
> 	- unpredictable distribution of numbers across processes (with the random generation)
> 	- all processes are senders and receivers at the same time (a risk of a deadlock) - we can use non-blocking operations and testing of message receptions e.g. `MPI_Wait` (adds overhead)
> 
> There are 3 phases of the improved parallel solution:
> 
> Phase 1: scatter numbers to random destinations
> - each process rolls dice for each number to decide to which process it should be sent
> 	- it has two arrays of length $n/p$ (one of the actual numbers and the other for the process ranks of the destination for the current $i$-th number)
> - all processes do the `MPI_Alltoallv` synchronized exchange
> 	- after it, the counts of the numbers in all processes can be different (due to randomization, fixed in 3rd phase)
> 	- requriements of the `MPI_Alltoallv`: 4 arrays of length $p$ per process:
> 		- `sendcounts[j]` - how many numbers this process sends to process $j$
> 			- basically a histogram of destinations (how many to send where)
> 		- `sdispls[j]` - the offset in `sendbuf` where data for process $j$ starts
> 			- the offsets are calculated by exclusive prefix sum (previous offset + the previous sendcount = a next offset)
> 			- the array of data to be sent must be sorted to consecutive blocks according the counts and offsets (done by a Counting sort)
> 		- `recvcounts[j]` - how many numbers this process receives from process $j$
> 			- cannot be calculated locally, I need concatenation of all `sendcounts[j]` from all processes
> 				- a small `MPI_Alltoall` (uniform-count-variant) is needed to exchange sendcounts, so each process learns its recvcounts
> 			- the sum of recvcounts is the size of incoming data -> we need to allocate a buffer for it
> 		- `rdispls[j]` - the offset in `recvbuf` where data from process $j$ is stored
> 
> Phase 2: shuffle locally
> - each process independently shuffles it's own array (using standard random shuffle algorithms)
> 	- the data from other processes could come in the structured form
> 	- could be multithreaded by OpenMP
> 
> Phase 3: rebalance so every process has exactly $n/p$ elements
> - all processes have a rank, they run a parallel prefix sum to globally acknowledge the counts of numbers in each process
> 	- so they know how many numbers to push to the left or pull from the right process (they exchange only with their neighbors)
> 	- there is a virtual 1D mesh (a line) and only neighbors are communicating
> 	- where the local count exceeds $n/p$, the excess is shipped to a neighbor
> 
> Complexity: dominated by large $p$ and the operation `MPI_Alltoallv` - most of the data are transferred through the system
> - in most topologies, the AAS operation has a asymptotic quadratic communication complexity


## Problem definition

Generate a random permutation of the numbers $0, 1, \ldots, n-1$ for a very large $n$, distributed across $p$ MPI processes. For simplicity, assume $p \mid n$. Initial state: the numbers are already distributed so that process $P_i$ owns exactly $n/p$ consecutive numbers $$ i \cdot n/p, \; i \cdot n/p + 1, \; \ldots, \; (i+1) \cdot n/p - 1. $$ This is the identity permutation, sliced contiguously across the processes. The goal is to produce a random permutation of these numbers, again distributed so that each process ends with exactly $n/p$ numbers. The motivation for distributing the task is that $n$ is so large the array cannot fit in a single computer's main memory; no process ever sees the full array.

## Algorithm `ParaPerm` (high-level)

Three phases:

- Ph.1: Each $P_i$ sends each of its $n/p$ numbers to a randomly chosen destination process (AAS - all-to-all scatter), including possibly itself.
- Ph.2: Each $P_i$ locally randomly permutes the numbers it received.
- Ph.3: All processes in parallel redistribute the numbers mutually so that each ends with exactly $n/p$ numbers (final balancing). The conceptual idea is the inverse of sorting: take sorted data and produce random order. The challenge is that each process picks destinations independently, so the load after Ph.1 is imbalanced, hence the explicit balancing step.

## Phase 1: naive implementation and why it fails

The naive version: each process loops over its local numbers, calls `rand(0, p-1)` to pick a destination, and issues `MPI_Send` immediately. Receivers wait in a separate loop.

```c
long first = r * n/p;
long last  = (r + 1) * n/p - 1;
for (long i = first; i <= last; i++) {
    int dest = rand(0, p-1);
    MPI_Send(&i, 1, MPI_LONG, dest, 0, MPI_COMM_WORLD);
}
... // receiving numbers from other processes
```

Three drawbacks:

- All processes are simultaneously senders and receivers, so a deadlock can arise. Could be patched with nonblocking calls plus message-reception testing, but that adds overhead.
- No process knows how many messages will arrive in total from any given source, so distributed termination detection would have to be added - itself a nontrivial algorithm.
- Communication is extremely inefficient: $n$ messages in total (and $n \gg p$), each carrying a single number with full setup latency. The better approach: each process first records, for every local number, the rank of its randomly chosen destination, packs together the numbers going to the same destination, and then all processes execute a single combined AAS via `MPI_Alltoallv`.

> The general lesson: any efficient distributed algorithm must batch its communication into collective operations. One `MPI_Alltoallv` replaces $n$ point-to-point messages with $p^2$ predictable transfers handled by an optimized library routine.

## Phase 1 improved: tracking destinations

Each process maintains two arrays of length $n/p$:

- `numbers[i] = i + first` - the local numbers
- `dests[i] = rand(0, p-1)` - the randomly chosen destination for `numbers[i]`

```c
long first = r * n/p;
std::vector<long> numbers(n/p);
std::vector<int>  dests(n/p);
#pragma omp parallel for
for (long i = 0; i < n/p; i++) {
    numbers[i] = i + first;
    dests[i]   = rand(0, p-1);
}
```

Because each process sends different counts to different destinations and symmetrically receives different counts from different sources, the right MPI primitive is `MPI_Alltoallv` (the variable-count version of all-to-all).

## `MPI_Alltoallv` requirements

The function needs four arrays of length $p$ per process:

- `sendcounts[j]` - how many numbers this process sends to process $j$
- `sdispls[j]` - the offset in `sendbuf` where data for process $j$ starts
- `recvcounts[j]` - how many numbers this process receives from process $j$
- `rdispls[j]` - the offset in `recvbuf` where data from process $j$ is stored
- The send-side data must be packed contiguously by destination in `sendbuf`. So before calling `MPI_Alltoallv` each process must sort the pair `(numbers, dests)` by `dests` (essentially a Counting Sort using `dests` as the key) to physically pack numbers per destination.

## Computing `sendcounts` and `sdispls` (locally)

Each process can compute these locally:

```c
std::vector<int> sendcounts(p, 0), sdispls(p, 0);
// histogram of destinations - core of Counting Sort
for (long i = 0; i < n/p; i++)
    sendcounts[dests[i]]++;
// exclusive prefix sum
for (long i = 1; i < p; i++)
    sdispls[i] = sdispls[i - 1] + sendcounts[i - 1];
// now array `numbers` can be packed using `dests` as keys
```

The arrays are of size $p$, so the computation is extremely fast even sequentially. OpenMP parallelization is possible but typically slows things down due to overhead on small arrays.

## Computing `recvcounts` and `rdispls` (requires communication)

The values of `recvcounts` CANNOT be computed locally. The content of `recvcounts[i]` on a given process equals the concatenation of `sendcounts[j]` from all other processes destined for it. To obtain this information, an `MPI_Alltoall` (the uniform-count variant) is needed:

```c
std::vector<int> recvcounts(p), rdispls(p, 0);
MPI_Alltoall(&sendcounts[0], 1, MPI_INT,
             &recvcounts[0], 1, MPI_INT, MPI_COMM_WORLD);
for (long i = 1; i < p; i++)   // exclusive prefix sum
    rdispls[i] = rdispls[i - 1] + recvcounts[i - 1];
```

Meta-pattern: a small `MPI_Alltoall` (one `int` per peer) sets up the metadata for the large `MPI_Alltoallv`. The small one is cheap; the large one is where the real data movement happens.

## The all-to-all scatter itself

The total number of incoming numbers is the sum of `recvcounts`. A receive buffer of that size is allocated. Then:

```c
long total = 0;
for (long i = 0; i < p; i++) total += recvcounts[i];
std::vector<long> numbers_2(total);
MPI_Alltoallv(&numbers[0],   &sendcounts[0], &sdispls[0], MPI_LONG,
              &numbers_2[0], &recvcounts[0], &rdispls[0], MPI_LONG,
              MPI_COMM_WORLD);
```

Thanks to the random destination generation, the size of `numbers_2` is never equal to $n/p$, so the AAS operation cannot be executed in-place. Input and output buffers must be separate.

## Complete Phase 1 code

```c
int p, r;
MPI_Comm_size(MPI_COMM_WORLD, &p);
MPI_Comm_rank(MPI_COMM_WORLD, &r);

std::vector<long> numbers(n/p);
std::vector<int>  dests(n/p);
#pragma omp parallel for
for (long i = 0; i < n/p; i++) {
    numbers[i] = i + r * n/p;
    dests[i]   = rand(0, p-1);
}

std::vector<int> sendcounts(p, 0), sdispls(p, 0);
for (long i = 0; i < n/p; i++)
    sendcounts[dests[i]]++;
for (long i = 1; i < p; i++)
    sdispls[i] = sdispls[i - 1] + sendcounts[i - 1];

// sort `numbers` using `dests` as keys (Counting Sort), so numbers are packed by destination
sort(&numbers[0], n/p);

std::vector<int> recvcounts(p), rdispls(p, 0);
MPI_Alltoall(&sendcounts[0], 1, MPI_INT,
             &recvcounts[0], 1, MPI_INT, MPI_COMM_WORLD);
for (long i = 1; i < p; i++)
    rdispls[i] = rdispls[i - 1] + recvcounts[i - 1];

long total = 0;
for (long i = 0; i < p; i++) total += recvcounts[i];
std::vector<long> numbers_2(total);

MPI_Alltoallv(&numbers[0],   &sendcounts[0], &sdispls[0], MPI_LONG,
              &numbers_2[0], &recvcounts[0], &rdispls[0], MPI_LONG,
              MPI_COMM_WORLD);
```

## Phase 2: local random permutation

Each process locally permutes the elements of `numbers_2` using any standard random shuffle (e.g., Fisher-Yates). This phase is:

- Independent across processes - no communication.
- Easily multithreaded with OpenMP. This step is necessary because after Phase 1 the numbers received by a process may still be in a structured order (e.g., the first numbers received from $P_0$ come first, then numbers from $P_1$, etc., and within each block the original sorted order is preserved). Without Phase 2, the local sub-arrays would not be properly randomized internally.

## Phase 3: final balancing

After Phase 2, each process holds a random number of elements (not exactly $n/p$). The balancing step moves numbers between neighboring processes along a virtual 1-D mesh ordered by rank so that every process ends with exactly $n/p$ numbers. This phase can be efficiently solved using a parallel scan (parallel prefix sum) on the local counts, so each process knows exactly how many numbers should be sent to the left or right (or received from left or right). Communication takes place only between neighbors in the virtual 1-D mesh. Where the local count exceeds $n/p$, the excess is shipped to a neighbor; where it is below $n/p$, missing numbers are pulled from a neighbor.

## Overall complexity and dominating term

The total execution time is dominated, for larger $p$, by the time complexity of `MPI_Alltoallv` in Phase 1, where most of the input data is transferred across the system. The AAS operation has asymptotic quadratic communication complexity in most topologies.

> Phase 1 (the AAS via `MPI_Alltoallv`) is the bottleneck. Phases 2 and 3 are essentially constant per process and contribute negligibly to the total time at scale.

## Experimental evaluation (Hopper supercomputer, NERSC)

Configuration: 1 MPI process per CPU core, MPI-only (no OpenMP threading in the measurement). Weak scaling: $n = p \cdot 2^{24}$, so each process always holds $2^{24}$ initial numbers. Scaling range: $p = 8$ up to $p = 131{,}072$, permutation sizes from $2^{27}$ to $2^{41}$. Observed behavior:

- Phase 2 (local random permutation): constant in $p$, around 3 seconds.
- Phase 3 (balancing): constant in $p$ at low scale, around 0.5 seconds (drops to a steady value above $p \approx 32$).
- Phase 1 local sorting (Counting Sort): constant in $p$, around 4 seconds.
- Phase 1 total: starts around 8 seconds at $p = 8$ and grows steadily, reaching nearly 256 seconds at $p = 131{,}072$. The growth of Phase 1 reflects the quadratic communication complexity of `MPI_Alltoallv`. At small $p$, local work dominates; at large $p$, the AAS dominates. The crossover lies somewhere around $p = 1000$ on this machine.

> This experimentally validates the theoretical complexity: AAS really is asymptotically quadratic in practice, and it really does dominate at scale.

## Summary of key MPI primitives used

- `MPI_Alltoall` (uniform-count): small metadata exchange, one `int` per peer, used to communicate `sendcounts` so each process learns its `recvcounts`.
- `MPI_Alltoallv` (variable-count): the main data redistribution in Phase 1.
- Local nearest-neighbor send/receive along a virtual 1-D mesh in Phase 3, driven by a parallel prefix sum on local counts.

## Potential exam questions

- Define the problem of distributed parallel generation of a random permutation of $0, \ldots, n-1$ on $p$ MPI processes. State the input distribution and the required output distribution.
- Describe the three-phase algorithm `ParaPerm`. What does each phase accomplish and what type of MPI communication does each require?
- Explain why the naive Phase 1 implementation (one `MPI_Send` per number with a random destination) is unacceptable. Give all three drawbacks.
- Explain why `MPI_Alltoallv` is needed in Phase 1 rather than `MPI_Alltoall`. What is the difference between the two?
- What four parameter arrays does `MPI_Alltoallv` need, and what does each describe? Which of them can be computed purely locally by each process, and which one requires inter-process communication to construct?
- Describe how `sendcounts` and `sdispls` are computed locally. Why is this computation usually faster sequentially than with OpenMP?
- Explain how `recvcounts` is obtained. Why can it not be computed locally, and which MPI primitive is used to obtain it?
- Why can the `MPI_Alltoallv` in Phase 1 not be performed in-place? What property of the random destination generation forces a separate output buffer?
- What is the purpose of Phase 2 (local random permutation), given that Phase 1 already randomly distributes numbers? Why is Phase 1 alone insufficient?
- Describe Phase 3 (final balancing) in detail. How is a parallel prefix sum used to drive the rebalancing? On what virtual topology does communication take place, and between which processes?
- Which phase dominates the total execution time at scale, and why? State the asymptotic communication complexity of the AAS operation in most topologies.
- Sketch the experimental results obtained on Hopper. What is observed about the relative behavior of Phases 1, 2, and 3 as $p$ grows under weak scaling $n = p \cdot 2^{24}$, and how does this validate the theoretical complexity analysis?
- Why does the algorithm use Counting Sort to pack the local `numbers` array by destination before calling `MPI_Alltoallv`? Could any other sorting algorithm be used, and would it matter?
- Write pseudocode (or close-to-MPI C code) for the complete Phase 1 of the algorithm.