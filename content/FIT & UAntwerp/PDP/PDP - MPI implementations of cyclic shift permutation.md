# Exam Q22: MPI implementations of cyclic shift permutation

The cyclic shift permutation on a virtual ring of MPI processes: why the naive blocking implementation is broken, and four correct solutions of progressively increasing elegance. This is the paradigmatic worked example for understanding the interaction between communication modes, blocking semantics, and deadlock avoidance.

### The cyclic shift permutation

> Process `proc_num` sends data to process `(proc_num + 1) % num_procs`.

Equivalently, every process simultaneously sends one message to its "next" neighbour on a virtual ring and receives one message from its "previous" neighbour. Cyclic shift is one of the most common communication operations in distributed algorithms (it appears for example in the Cannon algorithm for matrix multiplication).

Throughout this note:

```c
int next = (proc_num + 1) % num_procs;
int prev = (proc_num + num_procs - 1) % num_procs;
```

### The naive (broken) implementation

```c
// all processes:
MPI_Send(&value_to_be_sent, 1, MPI_INT, next, 0, MPI_COMM_WORLD);
MPI_Recv(&value_to_be_received, 1, MPI_INT, prev, ...);
```

#### Why it can deadlock

Recall that `MPI_Send` runs in **standard mode**: the implementation may complete it either by transferring data to the destination (option 1, non-local) or by copying to a system buffer (option 2, local). The choice is up to the MPI library and **the programmer cannot assume which is taken**.

If the library picks option 1 for every process simultaneously, every process blocks waiting for its right-neighbour to start receiving - but every right-neighbour is itself blocked in `MPI_Send`, never reaching its `MPI_Recv`. Classical **dining-philosophers deadlock**: every philosopher picks up the left fork, none can pick up the right.

If `MPI_Send` is replaced by `MPI_Ssend` (synchronous mode), the deadlock is no longer probabilistic - it occurs **always**, because synchronous send is guaranteed non-local and unconditionally requires the receiver to engage first.

> The probability of deadlock with `MPI_Send` is not 100%, but it is also not 0% - which is exactly the worst possible situation for portable code. With `MPI_Ssend` it is always 100%.

The four following solutions all break the cycle of dependencies and are correct.

### Solution I - alternate even/odd send-then-receive

Even-rank processes send first then receive; odd-rank processes receive first then send. The cyclic dependency is broken because in each adjacent pair, one side is always reading while the other is always writing.

```c
bool even = (proc_num % 2 == 0);
if (even) {
    MPI_Send(&value_to_be_sent, 1, MPI_INT, next, 0, MPI_COMM_WORLD);
    MPI_Recv(&value_to_be_received, 1, MPI_INT, prev, ...);
} else {
    MPI_Recv(&value_to_be_received, 1, MPI_INT, prev, ...);
    MPI_Send(&value_to_be_sent, 1, MPI_INT, next, 0, MPI_COMM_WORLD);
}
```

> **Question (left as exercise by the lecturer):** does this work for an **odd** number of processes, where the first and last processes are both even-ranked?  
> The answer is yes - it does work even in that case, but verifying it requires careful case analysis.

### Solution II - buffered mode

`MPI_Bsend` is **guaranteed to return** (it is a local operation) provided the user-supplied buffer is large enough to hold the outgoing message. After `MPI_Bsend` returns, the receive on every process can proceed normally.

```c
int buf;
int bs = sizeof(int) + MPI_BSEND_OVERHEAD;
MPI_Buffer_attach(&buf, bs);
MPI_Bsend(&value_to_be_sent, 1, MPI_INT, next, 0, MPI_COMM_WORLD);
MPI_Recv(&value_to_be_received, 1, MPI_INT, prev, ...);
MPI_Buffer_detach(&buf, &bs);
```

If reception has not been initiated yet, the MPI library must store the data in the user-defined buffer. If allocation fails (e.g. insufficient memory), `MPI_Bsend` throws an error.

**Trade-off**: extra memory, because the send buffer and the explicit buffering buffer must coexist. For small payloads this is the cleanest fix; for large payloads it doubles memory requirements.

### Solution III - nonblocking send

`MPI_Isend` returns immediately, allowing the receive on the same process to proceed and break the cyclic dependency. Completion is verified later by `MPI_Wait`.

```c
MPI_Request request;
MPI_Isend(&value_to_be_sent, 1, MPI_INT, next, 0, MPI_COMM_WORLD, &request);
MPI_Recv(&value_to_be_received, 1, MPI_INT, prev, ...);
MPI_Wait(&request, MPI_STATUS_IGNORE);
```

Notes:

- The blocking operation here is (besides `MPI_Recv`) `MPI_Wait`. It can return only once the data reception by the destination has been initiated.
- The same code works **even if `MPI_Isend` is replaced with `MPI_Issend`** (nonblocking synchronous send). `MPI_Wait` then safely returns once the destination has initiated reception.
- Symmetrically, **nonblocking `MPI_Irecv` followed by blocking `MPI_Send`** also works.

This pattern generalises: nonblocking primitives are the right tool whenever cyclic data dependencies exist.

### Solution IV - `MPI_Sendrecv`

The combined send-and-receive operation: each process behaves as a 2-port node, simultaneously sending right and receiving left. **Practically the simplest and most efficient solution.**

```c
MPI_Sendrecv(&value_to_be_sent,     1, MPI_INT, next, 0,
             &value_to_be_received, 1, MPI_INT, prev, 0,
             MPI_COMM_WORLD, MPI_STATUS_IGNORE);
```

`MPI_Sendrecv` is the operation the cyclic shift was effectively designed for - any time you want to exchange data between paired or chained processes, this is the right primitive.

For the special case where the same buffer should hold the outgoing value before the call and the incoming value afterwards, `MPI_Sendrecv_replace` is even more concise.

### Comparison and choice

The same problem admits four correct solutions of varying complexity:

- **Even/odd alternation** - no extra memory, no extra primitives, but verbose and brittle (must reason about parity).
- **Buffered mode** - simple control flow, costs extra memory proportional to payload size.
- **Nonblocking send + blocking recv + wait** - flexible, generalises to complex patterns, slightly more bookkeeping.
- **`MPI_Sendrecv`** - one call, expresses intent clearly, typically the fastest and the recommended default.

The choice depends on programmer experience, payload size, and the architecture of the target cluster. **The fastest solution must often be found by experiments and profiling.**

This small example is paradigmatic of MPI programming generally: there are many ways to express the same communication, some more elegant, some more performant, some more portable, and the right choice typically requires both engineering judgement and benchmarking on the actual cluster.

# Potential exam questions

1. Define the cyclic shift permutation on a virtual ring of MPI processes. Why is it a fundamental communication primitive?
2. Show the naive `MPI_Send` + `MPI_Recv` implementation of cyclic shift. Explain precisely why it may deadlock under standard mode and always deadlocks under synchronous mode.
3. Why is "may deadlock" worse than "always deadlocks" from a portability perspective?
4. Describe the even/odd alternation solution. What is the concern when the number of processes is odd?
5. Describe the buffered-mode solution. What is the role of `MPI_Buffer_attach` / `MPI_Buffer_detach` and `MPI_BSEND_OVERHEAD`? What is the trade-off compared to other solutions?
6. Describe the nonblocking-send solution using `MPI_Isend` + `MPI_Recv` + `MPI_Wait`. Why does this break the cyclic dependency? Does it still work if `MPI_Isend` is replaced with `MPI_Issend`?
7. Describe the `MPI_Sendrecv` solution. Why is it considered the simplest and most efficient? In what sense does each process behave as a 2-port node?
8. Compare all four correct solutions in terms of memory usage, code complexity, and likely performance.
9. What is `MPI_Sendrecv_replace` and when would you use it instead of `MPI_Sendrecv`?
10. Liken the naive cyclic-shift deadlock to the dining philosophers problem. What is the analogue of "picking up the left fork"?
11. The lecturer asked: does the even/odd alternation solution work for an odd number of processes (where rank 0 and rank `num_procs-1` are both even)? Reason through the case.
12. Why is the cyclic shift example paradigmatic for MPI programming as a whole?