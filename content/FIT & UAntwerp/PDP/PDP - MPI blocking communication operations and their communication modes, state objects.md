
> [!tldr] First 5 minutes of hell
> A blocking operation is the kind of operation, which returns only when a certain completion condition is satisfied.
> 
> `MPI_Send` is a blocking operation (it is completed when the input buffer can be modified (= the sending of all the data is complete)) - realizes "standard mode"
> - there are two options (decided by the MPI library, the code must handle both)
> 	- the data are either sent to the destination process (non-local operation - the completion of the MPI_Send depeds on the data reception from the destination process)
> 	- or the data are copied to the temporary system buffer (for later sending) - this is a local operation (the completion is after loading the buffer, it does not depend on the reception of the destination process)
> - the uncertainty is good for portability of the MPI application across multiple systems (each system handles buffering in a different way and the run of the program should not depend on it)
> - before sending, there is a small handshake in the background
> 	- the sender sends a tiny rendesvous message "hey, I have N bytes with tag T for you, are you ready?"
> 	- receiver firstly has to get to its `MPI_Recv` and then replies, yes, I am ready, send it to the address 0x... in my memory
> 	- sender then transfers the data
> 
> `MPI_Bsend` - realized "buffered mode", only local operation (the completion does not depend on the destination process receiving the data)
> - the programmer has to prepare a buffer to load the data into (calling `MPI_Buffer_attach`), in case of calling multiple MPI_Bsend functions, the buffer has to be able to store the sum of all data
> - buffering may improve the performance of a correct program, but a sufficient large buffer must always be allocated
> 
> `MPI_Ssend` - realizes the "synchronous mode", it is blocking until the destination process initializes the data reception, so it is only non-local operation
> 
> `MPI_Rsend` - realizes "ready mode", the receiving process must already be waiting for the message (e.g. with `MPI_Recv` and buffer already allocated)
> - the handshake is omitted, because the receiver is ready (the library trusts it, so it sends the message right away, if the receiver is not waiting, the system is undefined)
> - it is non-local operation (the receiver has to be involved)
>   
> There are two communication modes:
> - point-to-point: communication between two MPI processes
> - collective: communication among all MPI processes associated with the given communicator
> 	- communicator is a group of processes within which is allowed communication (each communication function has a parameter for the communicator to specify to which processes could the message be sent)
> 	- the implicit default is: `MPI_COMM_WORLD` = all defined processes within the MPI run
> 	- `MPI_Comm_rank`, `MPI_Comm_size` = functions to get the rank within the comm. group and the number of MPI processes in the comm. group
> 
> State object - `MPI_Status` is a data structure informing about the status of the source and of the communication for the receiving process:
> - it is populated by the `MPI_Recv` function and it contains information:
> 	- "who sent this message?", useful, when `MPI_ANY_SOURCE` is used
> 	- "what tag is there?", useful, when `MPI_ANY_TAG` is used
> 	- how many elements will arrive?, the `MPI_Recv` has specified only the maximum of elements to arrive (there could be less)
> 		- this is not directly in the struct, but needs to be retrieved using `MPI_Get_count(&status, MPI_INT, &received);` function

### Definition: blocking operation

An MPI operation is **blocking** if the corresponding MPI function returns only after a defined completion condition has been satisfied. Concretely:

- `MPI_Send`, `MPI_Bsend`, `MPI_Ssend`, `MPI_Rsend` are blocking in the sense that **after they return, the input buffer (`buf`) can be safely modified** - the data is "gone" (either delivered or safely captured by the MPI subsystem).
- `MPI_Recv` is blocking in the sense that **after it returns, the received data is safely in its buffer**.

Example illustrating the blocking-send guarantee:

```c
int c = 10;
MPI_Send(&c, 1, MPI_INT, ...);
c = 20; // cannot influence the sent data; the destination receives 10
```

### Function signatures

```c
int MPI_Send(const void *buf, int count, MPI_Datatype datatype,
             int dest, int tag, MPI_Comm comm);

int MPI_Recv(void *buf, int count, MPI_Datatype datatype,
             int source, int tag, MPI_Comm comm,
             MPI_Status *status);
```

Parameters:

- `buf` - pointer to send buffer / receive buffer (contiguous memory).
- `count` - on send, the number of elements; on receive, the **maximum** number of elements that fit.
- `datatype` - MPI datatype (`MPI_INT`, `MPI_DOUBLE`, `MPI_UINT64_T`, ..., or a user-built `MPI_Type_create_*` type).
- `dest` / `source` - rank of the partner process within the communicator. `source` may be `MPI_ANY_SOURCE`.
- `tag` - integer label distinguishing semantically different messages. `tag` on receive may be `MPI_ANY_TAG`.
- `comm` - communicator (e.g. `MPI_COMM_WORLD`); sender and receiver must use the same one.
- `status` - output state object (or `MPI_STATUS_IGNORE`).

### The four send communication modes

#### 1. Standard mode - `MPI_Send`

Returns when the data is **either**:

1. **sent** to the destination process, **or**
2. **copied** into a temporary system buffer for later sending.

**The choice is up to the MPI library** - the programmer cannot assume which option will be taken. Consequences:

- In case (1), completion depends on the receiver becoming ready ⇒ `MPI_Send` is a **non-local** operation.
- In case (2), `MPI_Send` can return even before the receiver has initiated reception ⇒ behaves as a local operation.

Portable code must remain correct under both choices.

#### 2. Buffered mode - `MPI_Bsend`

- Completion does **not** depend on the receiver: `MPI_Bsend` is a **local** operation.
- The MPI library copies the data into a **user-supplied buffer** that must be pre-attached:

```c
MPI_Buffer_attach(buf, bs);   // bs must include MPI_BSEND_OVERHEAD per pending Bsend
... MPI_Bsend(...) ...
MPI_Buffer_detach(&buf, &bs);
```

- If a process issues several `MPI_Bsend`s, the buffer must accommodate the **sum** of their sizes plus per-message `MPI_BSEND_OVERHEAD`. Otherwise `MPI_Bsend` may complete with an error.
- Trades extra user memory for guaranteed local completion.

#### 3. Synchronous mode - `MPI_Ssend`

- The function does **not return until the destination process initiates the data reception** - a **rendez-vous** synchronization.
- **Non-local** operation. Whoever (sender or receiver) arrives first blocks until the other catches up.
- Useful when the program must be sure the receiver actually engaged.

#### 4. Ready mode - `MPI_Rsend`

- If the receiver has **not** already initiated reception, the function terminates **with an error** and behaviour is undefined.
- Otherwise it returns once the receiver initiates reception. **Non-local.**
- Use only when correctness of the program guarantees the receiver is already posted - then the implementation can skip a handshake and run faster.

### Why standard mode is deliberately ambiguous (motivation)

- In standard mode MPI gives the user no control over whether the message is buffered or not.
- The reason is **portability**: any system runs out of buffering memory if message sizes grow, and different implementations buffer to different degrees for efficiency.
- MPI takes the position that **a correct (and thus portable) MPI program must not depend on system buffering**.
- Buffering may improve performance of a correct program but must not affect its result.
- If buffering is required for correctness, the programmer must use **buffered mode** and explicitly allocate a sufficiently large user buffer.

> Programmers cannot assume which option standard mode will take. Portable code must work under either - and must never rely on system buffering for correctness.

### The state object: `MPI_Status`

Some MPI functions, notably `MPI_Recv`, return a pointer to an `MPI_Status` structure carrying metadata about the just-received message:

```c
MPI_Status status;
MPI_Recv(&buf, count, MPI_INT, MPI_ANY_SOURCE, MPI_ANY_TAG,
         MPI_COMM_WORLD, &status);
```

Public fields:

- `status.MPI_SOURCE` - rank of the source process.
- `status.MPI_TAG` - tag of the received message.

The **size** (number of received elements) is **not** a struct field; it must be queried separately:

```c
int received;
MPI_Get_count(&status, MPI_INT, &received);
```

If the caller does not need the metadata, the special placeholder `MPI_STATUS_IGNORE` can be passed:

```c
MPI_Recv(..., MPI_STATUS_IGNORE);
```

### Canonical use of `MPI_Status`: receiving a message of unknown size

Pre-allocate a buffer large enough for the maximum possible message, receive into it, query the actual count, then resize:

```c
static const int count = 1000;
std::vector<int> v(count);
MPI_Status status;
MPI_Recv(&v[0], count, MPI_INT,
         MPI_ANY_SOURCE, MPI_ANY_TAG, MPI_COMM_WORLD, &status);
std::cout << "Source: " << status.MPI_SOURCE
          << " Tag: " << status.MPI_TAG << std::endl;
int received;
MPI_Get_count(&status, MPI_INT, &received);
v.resize(received);   // free unused tail
```

This is also the foundation for combining `MPI_Probe` with `MPI_Recv` (covered separately under probing).

### Wildcards on receive

- `source = MPI_ANY_SOURCE` - accept message from any sender (typical in master-slave: master does not know which slave will finish first, then learns the rank from `status.MPI_SOURCE`).
- `tag = MPI_ANY_TAG` - accept any tag (recover the actual tag from `status.MPI_TAG`).

### Matching rules

A send and a receive **match** when:

- they share the same communicator,
- the sender's `dest` equals the receiver's process rank, and the receiver's `source` equals the sender's rank (or `MPI_ANY_SOURCE`),
- the tag matches exactly (or the receiver uses `MPI_ANY_TAG`),
- the datatype is the same,
- the receive buffer is at least as large as the sent message.

### Local vs non-local summary

- `MPI_Bsend` - **local** (depends only on user buffer).
- `MPI_Send` - **non-local in case (1), local in case (2)**; programmer cannot assume.
- `MPI_Ssend` - **non-local** (rendez-vous).
- `MPI_Rsend` - **non-local** (requires receiver pre-posted).
- `MPI_Recv` - **non-local** (depends on a matching send).

### Cyclic shift: a paradigmatic deadlock illustration

The cyclic shift (`proc_num` sends to `(proc_num+1) % num_procs`) implemented naively as `MPI_Send` followed by `MPI_Recv` may deadlock under standard mode (if the implementation chooses option 1 for all processes simultaneously) and **always** deadlocks under `MPI_Ssend`. This is the classical dining-philosophers situation and is the prime motivation for either alternating send/receive order on even/odd ranks, using `MPI_Bsend`, switching to nonblocking primitives, or using `MPI_Sendrecv`.

> The probability of deadlock with `MPI_Send` is not 100%, but it is also not 0% - the worst possible situation for portable code. With `MPI_Ssend` it is always 100%.

# Potential exam questions

1. Define what it means for an MPI operation to be **blocking**. State the precise post-condition guaranteed by `MPI_Send` and by `MPI_Recv`.
2. List the four blocking send modes in MPI and give the completion condition of each.
3. What is the difference between a **local** and a **non-local** operation? Classify `MPI_Send`, `MPI_Bsend`, `MPI_Ssend`, `MPI_Rsend` accordingly.
4. Explain standard mode (`MPI_Send`). Why is it deliberately under-specified, and what does this imply for portable programs?
5. Describe the rendez-vous semantics of `MPI_Ssend`. When would you choose it deliberately?
6. Describe `MPI_Bsend`. What is the user's responsibility regarding buffer management, and what happens if the buffer is too small?
7. When is `MPI_Rsend` safe to use, and what is the penalty if its precondition is violated?
8. Give the full signatures of `MPI_Send` and `MPI_Recv`, and explain every parameter.
9. What is the `MPI_Status` object? What information does it carry, how is the message size obtained, and when do you pass `MPI_STATUS_IGNORE`?
10. Show a code pattern that uses `MPI_Status` and `MPI_Get_count` to receive a message of a priori unknown size into a `std::vector` and trim it to the actual size.
11. Explain the wildcards `MPI_ANY_SOURCE` and `MPI_ANY_TAG`. Give a typical use case for each.
12. Why does the naive `MPI_Send`-then-`MPI_Recv` implementation of a cyclic shift sometimes deadlock under `MPI_Send` and always deadlock under `MPI_Ssend`?