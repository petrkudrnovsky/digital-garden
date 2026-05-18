# Exam Q20: MPI probing of messages

Probing in MPI: non-destructive inspection of incoming messages via `MPI_Probe` / `MPI_Iprobe`, the typical applications (optional messages and unknown-size reception), the race condition that arises in multithreaded processes, and the matching-probe family (`MPI_Improbe` / `MPI_Mrecv`) that solves it.

### Definition: probing

A **probe** is an operation that **tests whether a message matching given criteria is available for reception**, without actually receiving it. Probing is **non-destructive**: the message remains in the process's receive area and may be probed repeatedly or received later.

This contrasts with reception (`MPI_Recv`): a receive **consumes** the message and removes it from the receive area, so each arrived message can be received only once.

### The two basic probing functions

```c
int MPI_Probe (int source, int tag, MPI_Comm comm,
               MPI_Status *status);

int MPI_Iprobe(int source, int tag, MPI_Comm comm,
               int *flag, MPI_Status *status);
```

#### `MPI_Probe` - blocking, non-local

Returns only after a message matching `(source, tag, comm)` becomes available. The output `status` is filled in with **the same value that an `MPI_Recv` with the same arguments would have returned**.

#### `MPI_Iprobe` - nonblocking, local

Returns immediately. If a matching message is available, `flag = true` and `status` is filled in (again, identically to what `MPI_Recv` would produce). Otherwise `flag = false` and `status` is **undefined**.

In both functions:

- `source` may be `MPI_ANY_SOURCE`.
- `tag` may be `MPI_ANY_TAG`.
- A probed message **may not be received afterwards** (probing does not commit the program to receive), and the same message **may be probed repeatedly**.

> A `MPI_Probe` / `MPI_Iprobe` call detects a message that would have been received by an `MPI_Recv` function called at the same location with the same arguments.

### Two canonical applications

#### 1. Reception of optional messages

The most common case: a process is doing useful work and wants to react if and only if a particular kind of message has shown up. A typical example is **premature termination of a search**: another process announces it has found the optimal solution, and every process should periodically probe for the corresponding tag to know whether to abort early.

```c
int flag;
MPI_Status status;
MPI_Iprobe(MPI_ANY_SOURCE, TAG_FOUND_OPTIMUM, MPI_COMM_WORLD, &flag, &status);
if (flag) { /* receive it and shut down */ }
```

#### 2. Reception of a message of unknown size

Probe first to learn the size from the status object via `MPI_Get_count`, then allocate a buffer of exactly the required size, then call `MPI_Recv`:

```c
MPI_Status status;
int flag;
MPI_Iprobe(MPI_ANY_SOURCE, MPI_ANY_TAG, MPI_COMM_WORLD, &flag, &status);
if (flag) {  // a matching message is available
    int count;
    MPI_Get_count(&status, MPI_INT, &count);   // discover the size
    std::vector<int> buf(count);                // allocate exactly enough
    MPI_Recv(&buf[0], count, MPI_INT,
             status.MPI_SOURCE, status.MPI_TAG,
             MPI_COMM_WORLD, MPI_STATUS_IGNORE);
}
```

This is the cleaner alternative to "allocate a worst-case buffer and resize afterwards" - here we never over-allocate.

### Implementation requirements of probing

The MPI standard specifies what implementers must guarantee:

- If `MPI_Probe` is called by one process and another process calls a matching `Send`, then `MPI_Probe` returns successfully, **except when the message is consumed by a competing `MPI_Recv` executed by another thread of the probing process**.
- Similarly, if a process is actively probing with `MPI_Iprobe` and a matching message has been sent, then **eventually** `MPI_Iprobe` returns `flag = true`, provided
    - the message has not been received in the meantime by a competing `MPI_Recv` in another thread of the same process, **and**
    - the message has not been claimed by a competing matching probe (`MPI_Mprobe` / `MPI_Improbe`) in another thread of the same process.

In short: probing only "sees" a message that is still genuinely available in this process's receive area.

### The multithreaded race condition

In a multithreaded MPI process (e.g. running with `MPI_THREAD_MULTIPLE`), all threads share the network drivers and the receive area. Two threads can both probe the same arrived message and **both believe they will be the ones to receive it**. The slower of the two then issues a `Recv` and blocks indefinitely (or receives the wrong, next-arriving message), because the faster thread already consumed the one they had jointly observed.

Plain `MPI_Probe` / `MPI_Iprobe` provide **no atomic reservation** of the probed message - hence the race.

### The matching-probe family: `MPI_Improbe` and `MPI_Mrecv`

To eliminate the race, MPI provides a **probe that reserves the message for a future receive in the same thread**.

```c
int MPI_Improbe(int source, int tag, MPI_Comm comm, int *flag,
                MPI_Message *message, MPI_Status *status);

int MPI_Mrecv(void *buf, MPI_Count count, MPI_Datatype datatype,
              MPI_Message *message, MPI_Status *status);
```

#### How it works

- In contrast to `MPI_Iprobe`, when a matching message exists `MPI_Improbe` returns a **message handle** (`MPI_Message`) referring to the probed message. The message is now **claimed** for the calling thread - no other thread can intercept it.
- That handle is the input to `MPI_Mrecv` (matching receive), which actually copies the data into the buffer.
- Before returning from `MPI_Mrecv`, the message handle is **reset to `MPI_MESSAGE_NO_PROC`**. A subsequent call to `MPI_Mrecv` with this reset handle receives nothing.
- A call with a fabricated/non-existing message handle causes a **fatal error** - this is treated as a clear hacking attempt and crashes the program.

`MPI_Improbe` is the nonblocking variant; a blocking `MPI_Mprobe` exists analogously.

### Probe vs receive: the consumption distinction

- A **probe** is a query - the message stays available.
- A **receive** is a consumption - the message is gone after one successful call.
- A **matching probe** sits in between: it queries **and atomically claims** the message for a guaranteed subsequent matching receive in the same thread.

### State objects involved in probing

- `MPI_Status` - filled in by both `MPI_Probe` / `MPI_Iprobe` and the matching variants. Carries `MPI_SOURCE`, `MPI_TAG`, and the underlying count (queried via `MPI_Get_count`).
- `MPI_Message` - opaque handle returned by `MPI_Improbe` / `MPI_Mprobe` and consumed by `MPI_Mrecv`. Special value `MPI_MESSAGE_NO_PROC` denotes "no claimed message".
- `flag` - integer output of the nonblocking variants (`MPI_Iprobe`, `MPI_Improbe`); zero means no matching message is currently available.

# Potential exam questions

1. Define probing in MPI. Why is it called "non-destructive", and how does it differ from reception?
2. State the signatures of `MPI_Probe` and `MPI_Iprobe` and explain every parameter, including the role of `MPI_ANY_SOURCE` and `MPI_ANY_TAG`.
3. Which of `MPI_Probe` and `MPI_Iprobe` is local, and which is non-local? Justify.
4. What information does the status object filled in by `MPI_Probe` carry, and how is the message size obtained?
5. Describe the two canonical applications of probing. Show code for the unknown-size reception pattern.
6. Why may a probed message **not** be received? What happens if the same message is probed repeatedly?
7. Formulate the implementation guarantees that the MPI standard imposes on `MPI_Probe` and `MPI_Iprobe`. What is the role of competing receives in other threads of the same process?
8. Explain the race condition that arises in a multithreaded process when two threads probe the same incoming message.
9. Describe the matching-probe family (`MPI_Improbe` / `MPI_Mrecv`). What is the `MPI_Message` handle, and what guarantee does it provide that `MPI_Iprobe` does not?
10. What happens to the `MPI_Message` handle after `MPI_Mrecv` returns? What happens if `MPI_Mrecv` is called with a fabricated handle?
11. Compare probe, receive, and matching probe with respect to message consumption.
12. Sketch how you would implement an "early termination" scheme in a parallel search using `MPI_Iprobe`.