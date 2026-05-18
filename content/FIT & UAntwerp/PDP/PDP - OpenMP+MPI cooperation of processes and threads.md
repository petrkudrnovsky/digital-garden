# Exam Q17: OpenMP+MPI cooperation of processes and threads

Hybrid parallel programming combining MPI (distributed-memory, inter-node) with OpenMP (shared-memory, intra-node) on clusters whose nodes contain one or more multicore CPUs.

### Motivation: hybrid hardware

Computing nodes of clusters/supercomputers are almost exclusively composed of one or more multicore CPUs. They therefore have **hybrid architectures with both distributed and shared memory**: distributed across nodes, shared within a node. Pure MPI or pure OpenMP each address only one half of this hierarchy.

Three ways to compose parallel programs on such machines:

1. **MPI Only model**: on each core/CPU/computing node, one or several MPI processes are running that are **NOT** forked into threads. The shared memory within computing nodes is not utilized. Even two processes on adjacent cores in the same CPU communicate via message passing - unnecessary overhead.
2. **MPI + OpenMP hybrid model**: on each computing node/CPU, one or several MPI processes are running and those **DO fork** using OpenMP into multiple threads running on cores.
3. **Hybrid models with 1 OpenMP thread/core provide higher performance than the MPI Only model with 1 process/core for most applications.**

### Typical hybrid architectures

Two standard mappings:

1. **1 MPI process per computing node**: the process forks into multiple threads corresponding to all cores of the node.
2. **1 MPI process per CPU (= socket)**: the process forks into multiple threads corresponding to the cores of the CPU. **Better access to data ⇒ often higher performance**, because each MPI process and its threads stay within one NUMA domain (one socket), avoiding cross-socket memory traffic.

### Initialization: `MPI_Init_thread`

A hybrid program **must not** use plain `MPI_Init`. It must use `MPI_Init_thread`, which negotiates the level of cooperation between MPI and OpenMP threads.

```c
int MPI_Init_thread(int *argc, char ***argv, int required, int *provided);
```

- `required`: the level of thread support the program needs.
- `provided`: the level the MPI library is actually able to provide. In general, various MPI libraries support only some variants. The four levels form a **totally ordered set** (typically integers 0, 1, 2, 3) and can be compared with `<`.

### The four levels of thread cooperation

1. `MPI_THREAD_SINGLE`: MPI Only model. Processes are not forked into threads.
2. `MPI_THREAD_FUNNELED`: multithreaded processes are allowed with the limitation that **only the master thread can call MPI functions**. **1-port model** (only one narrow place where MPI can be used; only one communication active at a time per node).
3. `MPI_THREAD_SERIALIZED`: multithreaded processes are allowed with the limitation that **at a given time only one thread can call MPI functions** (it requires critical sections - any thread may call MPI, but they must be serialized by the user). **1-port model.**
4. `MPI_THREAD_MULTIPLE`: multithreaded processes where **all threads can call MPI functions without any constraints**. **All-port model** (multiple physical ports usable simultaneously).

> The four values form a totally ordered set: `SINGLE < FUNNELED < SERIALIZED < MULTIPLE`. After calling `MPI_Init_thread`, always compare `provided < required` and abort if support is insufficient.

### Canonical hybrid example (`mpi_omp_test.cpp`)

Program requires at least `MPI_THREAD_FUNNELED`. All threads then write out their ranks within their processes in parallel.

```c
int main(int argc, char* argv[]) {
    int provided, required = MPI_THREAD_FUNNELED;
    MPI_Init_thread(&argc, &argv, required, &provided);
    if (provided < required)
        throw std::runtime_error(
            "The MPI library does not provide required threading support");
    int proc_num;
    MPI_Comm_rank(MPI_COMM_WORLD, &proc_num);   // master only - before parallel region
    #pragma omp parallel
    std::cout << "Process " << proc_num
              << " Thread " << omp_get_thread_num() << std::endl;
    MPI_Finalize();
}
```

The only MPI call inside the program (`MPI_Comm_rank`) is made by the master **before** entering the parallel region, so threads themselves never call MPI - this is exactly what `MPI_THREAD_FUNNELED` permits.

### Compilation and execution

Compilation chains the OpenMP switch through the MPI compiler wrapper:

```
OMPI_CXX=g++ mpic++ -fopenmp -o mpi_omp_test mpi_omp_test.cpp
```

Execution combines the OpenMP environment variable with the MPI launcher:

```
OMP_NUM_THREADS=2 mpirun -np 2 ./mpi_omp_test
```

Sample output (order non-deterministic):

```
Process 0 thread 0
Process 0 thread 1
Process 1 thread 0
Process 1 thread 1
```

**Threads within the frame of MPI processes are ranked locally from 0**, independently per process. There is no global thread rank.

### Communication semantics in the hybrid model

- MPI processes do not share memory; threads within one MPI process do.
- Inter-node and inter-process communication: MPI message passing.
- Intra-process (between threads of one MPI process): shared-memory reads/writes, OpenMP synchronization.
- Whether and how threads may issue MPI calls is governed entirely by the level returned in `provided`.

### Application example: Magic Square hybrid skeleton

The combinatorial state-space search Magic Square problem from Lecture 3 (originally OpenMP master-slave with threads) is recast in the hybrid model:

- The **MPI master process** (rank 0) plays the role of the master thread: it generates `EnoughStates` initial subspaces by BFS and distributes them to slave MPI processes via `TAG_WORK` / `TAG_TERMINATE`.
- Each **MPI slave process** is itself a multi-threaded OpenMP program: on receiving a subspace via `TAG_WORK`, it runs the existing `OpenMPMagicSquare5` routine (OpenMP BFS parallel version) on its assigned subproblem and reports completion via `TAG_DONE`.
- Final results are aggregated with `MPI_Reduce` before slaves terminate.

This is the prototypical hybrid pattern: MPI handles coarse-grained work distribution between nodes; OpenMP handles fine-grained parallelism inside each node.

### Why hybrid usually outperforms MPI-only

- Avoids redundant MPI buffers and communication overhead between cores that share physical memory.
- Reduces the number of MPI processes ⇒ less metadata, fewer connections, smaller collective trees.
- Improves cache and NUMA locality, especially in the per-socket mapping.
- Lets the programmer match the hardware hierarchy: MPI for the inter-node distributed-memory layer, OpenMP for the intra-node shared-memory layer.

### Summary table of cooperation levels

- `MPI_THREAD_SINGLE` - no threads at all (degenerates to MPI Only).
- `MPI_THREAD_FUNNELED` - multithreaded process, only master calls MPI (1-port).
- `MPI_THREAD_SERIALIZED` - multithreaded process, any thread may call MPI but not concurrently (1-port, user-managed critical section).
- `MPI_THREAD_MULTIPLE` - multithreaded process, any thread calls MPI freely and concurrently (all-port).

# Potential exam questions

1. Explain the three models of composing parallel programs on hybrid clusters (MPI Only, MPI+OpenMP hybrid). Why does the hybrid model usually outperform MPI Only?
2. What are the two typical hybrid architectures (1 MPI process per node vs. 1 per socket)? Why does the per-socket variant often give higher performance?
3. State the signature of `MPI_Init_thread`. What is the meaning of `required` vs. `provided`, and how should the program react if `provided < required`?
4. Enumerate the four levels of MPI thread cooperation and explain each precisely. Which are 1-port and which is all-port, and why?
5. Why must hybrid programs use `MPI_Init_thread` instead of `MPI_Init`?
6. In `MPI_THREAD_SERIALIZED`, what is the user's responsibility when multiple threads need to call MPI?
7. Write a minimal hybrid program that requires at least `MPI_THREAD_FUNNELED` and has each thread print its process rank and thread number. Show the compilation and execution commands.
8. How are threads ranked inside MPI processes in the hybrid model? Is there a global thread rank?
9. Show how the Magic Square master-slave problem is mapped to the hybrid MPI+OpenMP model. What role does each MPI process play and what does OpenMP do inside a slave?
10. Why is `MPI_THREAD_MULTIPLE` called the "all-port" model while `FUNNELED` and `SERIALIZED` are "1-port"? Relate this to the underlying communication hardware.
11. What is the relationship between `OMP_NUM_THREADS` and `mpirun -np` when launching a hybrid program, and how does each affect the total number of cores used?
12. Suppose a cluster has 1008 nodes, each with two 12-core sockets. Compare the maximum core/memory utilisation under MPI Only, "1 MPI process per node" hybrid, and "1 MPI process per socket" hybrid.