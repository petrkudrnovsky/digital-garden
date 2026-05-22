
> [!tldr] First 5 minutes of hell
> The non-blocking operation returns immediately (independently of the completion condition). The data transfer is then in progress and we cannot modify the buffer (after sending) or read the buffer (before receiving) until explicitly testing whether the communication has finished. 
> 
> Operations: `MPI_Isend`, `MPI_Ibsend`, `MPI_Issend`, `MPI_Irsend`, `MPI_Irecv` (prefix "I" = immediate)
> - the completion conditions did not change (see previous exam question), only that those functions return immediately
> - `MPI_Irecv` initiates the data reception, but the data cannot be read until the explicit check
> 	- so this function does not return `MPI_Status` struct, because the data are not received yet
> - all operations have an extra variable: `MPI_Request`, which is like a semantic connection between the kickoff of the communication and the completion of the communication
> 	- this is useful for checking whether the communication has finished or not?
> 
> How to check if the communication is finished?
> - `MPI_Test` - for active pooling (returns instantly with true/false if the communication is finished)
> - `MPI_Wait` - for passive pooling, but is blocking (waits until the communication is finished)
> - both functions fill the `MPI_Status` field after the communication takes place
> - both functions serve for checking both sending and receiving statuses even though `MPI_Status` is not needed for the sending side (it is done for universality), could be ignored
> - there are also: 
> 	- `Testany`, `Waitany`, `Testall`, `Waitall` (testing/waiting for any process or all processes)
> 
> What are the benefits of the nonblocking operations?
> - threads can do useful work while waiting on the data (communication X work overlap)
> - threads can wait on multiple communications at once (communication X communication overlap)
> - mitigation of the risk of communication deadlock (which could happen easily with blocking communication method - one process waits on other, which waits on the first since it needs certain data to continue)
> 	- the send needs to be matched with receive in order that does not cause deadlock

### Definition: nonblocking operation

A nonblocking MPI function **returns immediately** after kicking off the operation, **independently of any completion condition**. The data transfer is in progress; whether it has finished is unknown until the program explicitly tests for it.

Two consequences follow directly from this definition:

- For nonblocking **sends**: the input buffer **cannot be modified** until completion has been verified.
- For nonblocking **receives**: the output buffer **cannot be read** until completion has been verified.

### The five nonblocking primitives

Each blocking primitive has a nonblocking counterpart, distinguished by the prefix `I` (immediate):

- `MPI_Isend` - nonblocking standard send.
- `MPI_Ibsend` - nonblocking buffered send.
- `MPI_Issend` - nonblocking synchronous send.
- `MPI_Irsend` - nonblocking ready send.
- `MPI_Irecv` - nonblocking receive.

The communication mode (standard, buffered, synchronous, ready) determines under what conditions the **eventual completion** will happen, exactly as in the blocking case. The nonblocking variant only changes **when the call returns**, not what completion means.

### The `MPI_Request` state object

All nonblocking functions take an extra output parameter: a pointer to a variable of type `MPI_Request`. This handle is the unique link between the kickoff and the later completion check.

```c
int MPI_Isend(const void *buf, int count, MPI_Datatype datatype,
              int dest, int tag, MPI_Comm comm,
              MPI_Request *request);

int MPI_Irecv(void *buf, int count, MPI_Datatype datatype,
              int source, int tag, MPI_Comm comm,
              MPI_Request *request);
```

With multiple outstanding nonblocking operations, each generates its own `MPI_Request`, which preserves the semantic pairing of kickoff and completion. Conceptually, the kickoff and completion check act like opening and closing parentheses surrounding arbitrary intervening code.

### Completion: `MPI_Test` and `MPI_Wait`

```c
int MPI_Test(MPI_Request *request, int *flag, MPI_Status *status);
int MPI_Wait(MPI_Request *request, MPI_Status *status);
```

- `MPI_Test` is **active polling**: returns immediately. `flag = true` means the operation has completed (and `status` is filled in for receives); `flag = false` means it is still in progress.
- `MPI_Wait` is **blocking**: returns only when the operation has completed. The completion condition is the same as the equivalent blocking call would have used (e.g. for `MPI_Issend`, `MPI_Wait` returns once the receiver has initiated reception).

The two mechanisms can be **freely mixed**: poll a few times with `MPI_Test` to overlap useful work with the communication, then fall back to `MPI_Wait` once the data is genuinely needed.

### Where the status object comes from in nonblocking receive

For nonblocking reception, the `MPI_Status` object is filled in by `MPI_Test` / `MPI_Wait`, **not** by `MPI_Irecv` itself - because at the moment `MPI_Irecv` returns, no data has been received yet, so source/tag/count are not yet known.

```c
int c;
MPI_Request request;
MPI_Irecv(&c, 1, MPI_INT, MPI_ANY_SOURCE, MPI_ANY_TAG,
          MPI_COMM_WORLD, &request);
// c is not defined yet
MPI_Status status;
MPI_Wait(&request, &status);
// now c holds the received value, status is filled in
std::cout << "Source: " << status.MPI_SOURCE << std::endl;
```

### Canonical example: `MPI_Isend` + `MPI_Test` + `MPI_Wait`

```c
int c = 10;
MPI_Request request;
MPI_Isend(&c, 1, MPI_INT, 1, 0, MPI_COMM_WORLD, &request);
// c cannot be modified yet
int flag;
MPI_Status status;
MPI_Test(&request, &flag, &status);
// c can be modified only if flag == true
MPI_Wait(&request, &status);
// now c can be safely modified
```

### Collective completion: `Testany`, `Waitany`, `Testall`, `Waitall`

`MPI_Test` and `MPI_Wait` operate on **one** request. For a set of outstanding requests:

- `MPI_Testany` / `MPI_Waitany` - test/wait for **any one** of the requests to complete; returns the index of the one that did.
- `MPI_Testall` / `MPI_Waitall` - test/wait for **all** requests to complete simultaneously.

```c
MPI_Request requests[3];
MPI_Irecv(..., &requests[0]);
MPI_Irecv(..., &requests[1]);
MPI_Irecv(..., &requests[2]);
MPI_Status statuses[3];
MPI_Waitall(3, requests, statuses);
```

This enables expressing "do something if any of my outstanding sends or receives has progressed" in a single call, instead of looping over individual `MPI_Test`s.

### Universality of `MPI_Wait` / `MPI_Test`

`MPI_Wait` is the **same function for all four send modes and for receive**. Its universality requires an output `MPI_Status` parameter even though status makes no real sense for sends. The MPI designers chose universality over neatness - one wait/test call covers all five operation kinds; the user simply ignores the status (`MPI_STATUS_IGNORE`) when waiting on a send. The same applies to `MPI_Test`, which additionally has the output `flag`.

### Why nonblocking communication matters

#### Correctness

Any blocking operation creates the possibility of **deadlock** if data dependencies between communication operations are misdesigned. For complex communication patterns, ensuring that every blocking send is matched with a receive in a deadlock-free order is hard; for **dynamic** patterns determined at runtime, it is harder still. Nonblocking communication breaks cyclic dependencies because the kickoff returns immediately, allowing receives to be posted on the same process.

The cyclic shift permutation is the canonical example: naive `MPI_Send` + `MPI_Recv` on a ring may deadlock; replacing the send with `MPI_Isend` followed by `MPI_Recv` and then `MPI_Wait` works correctly and even works if `MPI_Isend` is replaced with `MPI_Issend`. Symmetrically, `MPI_Irecv` followed by blocking `MPI_Send` works as well.

#### Performance

Nonblocking communication enables two kinds of overlap:

- **Overlap of communication with computation**: between the kickoff and the completion check, the process can do useful work that does not touch the buffer.
- **Overlap of communication with other communication**: multiple nonblocking operations can progress simultaneously on systems with capable networks.

### Communication modes of nonblocking operations: summary

- Return from blocking `MPI_Send`, `MPI_Bsend`, `MPI_Ssend`, `MPI_Rsend` depends on satisfaction of the mode's defined condition.
- Return from `MPI_Isend`, `MPI_Ibsend`, `MPI_Issend`, `MPI_Irsend` is **immediate, independent of any condition**.
- Their **eventual completion**, verified via `MPI_Test` / `MPI_Wait`, satisfies exactly the same condition that the corresponding blocking operation would have satisfied on return.

So the mode (standard / buffered / synchronous / ready) controls **what completion means**; the I-prefix controls **when the call returns**. The two dimensions are orthogonal.

### State objects in nonblocking communication

Two distinct state objects are involved:

- `MPI_Request` - **input/output** to nonblocking calls and to `MPI_Test` / `MPI_Wait`. Created by the nonblocking kickoff, consumed (and reset) by a successful completion check.
- `MPI_Status` - **output** of `MPI_Test` / `MPI_Wait`. Carries `MPI_SOURCE`, `MPI_TAG`, and the underlying count (queried with `MPI_Get_count`). For sends, the status field has no meaningful content - use `MPI_STATUS_IGNORE`.

# Potential exam questions

1. Define a **nonblocking** MPI operation. State precisely what is and is not guaranteed when an `MPI_Isend` or `MPI_Irecv` returns.
2. List the five nonblocking point-to-point primitives in MPI and explain how each relates to its blocking counterpart.
3. What is the role of the `MPI_Request` state object? When is it created, how is it consumed, and what happens after a successful `MPI_Wait`?
4. Explain the difference between `MPI_Test` and `MPI_Wait`. Can the two be mixed on the same request? Give an example pattern.
5. For nonblocking receive, where does the `MPI_Status` object get filled in - in `MPI_Irecv` or in the test/wait? Why?
6. Describe `MPI_Waitany`, `MPI_Waitall`, `MPI_Testany`, `MPI_Testall`. When would you prefer each over a loop of `MPI_Wait`s?
7. Why does `MPI_Wait` have a `MPI_Status` output parameter even when waiting on a send? How do you handle this?
8. Explain the orthogonality of communication mode (standard/buffered/synchronous/ready) and blocking/nonblocking. What does each axis control?
9. Why is nonblocking communication important for correctness? Give the cyclic-shift example as illustration.
10. Why is nonblocking communication important for performance? Distinguish overlap of communication with computation from overlap of communication with communication.
11. Show a complete code fragment in which process 0 sends an integer to process 1 using `MPI_Isend` + `MPI_Test` + `MPI_Wait`, and process 1 receives it using `MPI_Irecv` + `MPI_Wait` and prints the source rank.
12. State what the eventual completion of `MPI_Issend` (via `MPI_Wait`) guarantees, and contrast it with the eventual completion of `MPI_Ibsend`.