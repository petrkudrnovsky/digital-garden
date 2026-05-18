# Exam Q21: MPI return values of functions and error handling

How MPI signals errors via return codes, the assumptions about underlying communication reliability, the three predefined error handlers, the mechanism for attaching custom handlers to communicators, and the typical use of `MPI_ERRORS_RETURN`.

### Reliability assumptions of the MPI standard

MPI assumes that **the underlying communication is reliable**:

- A message that has been sent is always received correctly.
- The user does not need to check for transmission errors, time-outs, or similar conditions.
- MPI provides **no mechanisms for dealing with transmission failures** in the communication system.

If the MPI implementation is built on an unreliable underlying mechanism, it is the **implementer's responsibility** to insulate the user from that unreliability and to surface only **unrecoverable** transmission failures.

Similarly, MPI itself provides **no mechanism for handling MPI process failures** (a software or hardware crash that causes a process to terminate unexpectedly). MPI is fundamentally pessimistic with respect to communication errors: by default, a serious failure aborts every process in the program.

### What can still go wrong

Even with reliable transport, MPI programs may contain:

- **User / algorithmic errors**: an MPI call made with an incorrect argument - non-existing destination in a send, buffer too small in a receive, invalid communicator, etc. These would occur in any implementation.
- **Resource errors**: the program exceeds available system resources - too many pending messages, exhausted system buffers. Occurrence depends on the system and its allocation mechanism, so it differs from one platform to another. A high-quality implementation provides generous resource limits to alleviate this portability issue.

### Return codes

**Almost all MPI calls return an integer code** indicating success or failure of the operation:

- On success: `MPI_SUCCESS` is returned.
- On failure: an **error code** is returned.

The error code is the basis on which an **error handler** of the given MPI function is invoked - the handler runs **before** the MPI function returns to the caller.

MPI defines **error classes** and provides a function that associates a human-readable **error string** with each error code or class.

### Where error handlers can be attached

A user can attach an error handler to four types of MPI objects. In this lecture only handlers attached to **communicators** (sets of processes) are discussed. (Handlers attached to files are covered in Lecture 10.)

The error handler attached to a communicator will be invoked for **any error that occurs during a call to MPI for that communicator**, meaning the impact of an error is naturally scoped to the group of processes sharing that communicator.

### The three predefined error handlers

#### 1. `MPI_ERRORS_ARE_FATAL`

- On error, this handler **aborts the whole MPI program** - i.e. all processes.
- Technically, those processes call `MPI_ABORT` internally.
- This is the **default** handler attached to `MPI_COMM_WORLD` and therefore the default for any program that does not say otherwise.
- The offending MPI function **never returns** under this handler.
- Consequence: under the default, **checking return codes is pointless** - the program is dead before it could observe them.

> Under the default `MPI_ERRORS_ARE_FATAL`, all processes are killed on any error and there is no point inspecting return values - they will never be observed.

#### 2. `MPI_ERRORS_RETURN`

- The MPI program is **not aborted**; the error code is returned to the caller as the function's return value.
- **BUT**: the state of the MPI computation after an error is **undefined**. Even though the program survives, there is no guarantee that further MPI calls will succeed - this is implementation-dependent.
- Typically, the user can still output a diagnostic message before shutting down gracefully.
- This is the closest mode to conventional sequential programming, where you check error codes and react.

#### 3. `MPI_ERRORS_ABORT` (new in MPI 4.0)

- Aborts only the **processes of the affected communicator**, not all processes.
- Lets part of a large computation survive a localized failure: only the affected sub-communicator dies; the rest of the system can keep running.

### Custom error handlers

Beyond the three predefined handlers, an MPI implementation may predefine more, and the **user can write their own**. The relevant API:

- `MPI_Comm_create_errhandler` - create a new error handler from a user-supplied callback.
- `MPI_Comm_set_errhandler` - attach a handler to a communicator.
- `MPI_Comm_get_errhandler` - query which handler is attached to a communicator.
- `MPI_Errhandler_free` - release a custom handler.

The custom handler is invoked **before** the MPI function returns when an error occurs.

### Typical usage pattern with `MPI_ERRORS_RETURN`

Install the handler, then branch on the returned code using predefined error classes:

```c
MPI_Comm_set_errhandler(MPI_COMM_WORLD, MPI_ERRORS_RETURN);
int error_code;
error_code = MPI_Send(..., MPI_COMM_WORLD);
if      (error_code == MPI_ERR_COMM)  { /* invalid communicator */ }
else if (error_code == MPI_ERR_COUNT) { /* invalid message size */ }
else if (error_code == MPI_ERR_TAG)   { /* invalid message tag  */ }
// ...
```

What can realistically go wrong with a non-fatal send? Things like an invalid communicator, a message size that was somehow corrupted in transit, or an unexpected tag - relatively minor problems where retry might succeed because the underlying communication is fine but the metadata is off.

### Summary of error semantics

- Communication is assumed reliable; transmission errors are not the user's concern.
- Process crashes are not handled by MPI itself.
- The default behaviour is to die loudly: any MPI error kills every process.
- The user can opt into recoverable behaviour by switching to `MPI_ERRORS_RETURN` (whole program survives but post-error state is undefined) or `MPI_ERRORS_ABORT` (only the affected communicator dies).
- Custom handlers provide the most flexibility but the same fundamental caveat applies: after an error, MPI cannot guarantee that continued operation is meaningful.

# Potential exam questions

1. What does the MPI standard assume about the reliability of the underlying communication system? What is the implementer's responsibility if the underlying transport is unreliable?
2. Does MPI provide a mechanism for handling MPI process failures (crashes)? What is the default behaviour when something goes seriously wrong?
3. Distinguish user/algorithmic errors from resource errors in MPI programs. Give an example of each.
4. What value do MPI functions return on success? What is the role of an error code, and what is an error handler?
5. To what kinds of MPI objects can error handlers be attached? Which kind is studied in detail in this lecture?
6. Name and describe the three predefined error handlers in MPI. Which is the default and where is it attached?
7. Why is checking return codes pointless under the default error handler?
8. Describe `MPI_ERRORS_RETURN`. What guarantee does it give about the post-error state of the MPI computation?
9. Describe `MPI_ERRORS_ABORT` (new in MPI 4.0) and explain how it differs from `MPI_ERRORS_ARE_FATAL`. Why was it added?
10. Name the four functions used to manage custom error handlers attached to communicators.
11. Show the typical usage pattern for installing `MPI_ERRORS_RETURN` on `MPI_COMM_WORLD` and branching on `MPI_ERR_COMM`, `MPI_ERR_COUNT`, `MPI_ERR_TAG`.
12. Why is the impact of an MPI error naturally scoped to the affected communicator, even before `MPI_ERRORS_ABORT` was introduced?