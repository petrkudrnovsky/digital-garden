- synchronization directives for synchronization of thread accesses to shared memory with parallel regions:
### Barrier
```c
#pragma omp barrier
```
- when explicit in a parallel region (`#pragma omp barrier`), all current threads from the thread team must wait for each other in order to continue
	- they are sleeping until all arrive
- barrier is also implicit at the end of each parallel region, every `for` construct and at the end of a `single` block
### Master
```c
#pragma omp master
{ /* structured block */ }
```
- the following block could be executed by master only (thread 0), other threads skip this region and continue further (without stopping and waiting)
- a difference to `single` directive: only thread 0 (master) is allowed, `single` lets a random thread (selected by OS)
### Single
```c
#pragma omp single
{ /* structured block */ }
```
- has an implicit barrier at the end of its block (other threads must wait until all the executing threads complete)
- other threads skips this region, but must wait at the end 
### Critical
```c
#pragma omp critical [name]
{ /* structured block */ }
```
- a critical region (inside a parallel region) to ensure a mutually exclusive access to shared resources
- if multiple critical regions are in the code, the mutual exclusion principle applies to all of them at the same time globally (one thread in one of the critical blocks keeps other threads from other critical regions as well)
	- this applies for anonymous critical blocks, we can also name them, then the global mutual exclusion applies to all blocks with the same name (more fine-grained control)
- example:
```c
#pragma omp parallel
{
    if (report_is_needed) {
        #pragma omp critical listing
        {
            printf("Very important ");
            printf("and long print");
        }
    }
    // ...
    if (report_is_needed) {
        #pragma omp critical listing
        {
            printf("Another very important ");
            printf("and long print");
        }
    }
}
```
- the `reduction` clause could be done with `critical` sections as well - just perform a parallel for and then the sum will be in the `critical` region
### Atomic
```c
#pragma omp atomic [read | write | update | capture]
/* single statement */
```
- important for accessing scalar data types (integers, floats...) in shared memory
- an operation (e.g. read/write/read-modify-write) is executed atomically (uninterruptedly) by a single thread
	- read-modify-write could be counter incrementation (`i += 2`)
	- without atomic, the threads can switch in any point between these three operations producing different results
- the behavior is similar to `critical`, but is more efficient for single-variable operations (it maps directly to hardware atomic instructions)
	- critical is more general and has a bigger overhead
```cpp
int i = 10;
#pragma omp parallel shared(i) num_threads(3) 
{
	#pragma omp atomic update
	i += 2;
}
```
- also critical section or mutexes can be used, but this is the most efficient
##### Atomic update
- most common variant (for read-modify-write operations)
```c
int i = 10;
#pragma omp parallel shared(i) num_threads(3)
{
    #pragma omp atomic update
    i += 2;
}
// i is ALWAYS 16, regardless of thread execution order
```
##### Atomic read and atomic write
- to ensure atomic reads and writes on all platforms (since OpenMP is portable) and not all platforms ensure atomic reads/writes
##### Atomic capture
- extension of `atomic update` by remembering the original value before change, so it could be further applied
```c
int *ptr = (int*) malloc(....);  // shared array
#pragma omp parallel shared(ptr) num_threads(3)
{
    int *my_ptr;
    #pragma omp atomic capture
    { my_ptr = ptr; ptr += BLOCK_SIZE; }
    // each thread now has exclusive access to
    // my_ptr[0], ..., my_ptr[BLOCK_SIZE - 1]
}
```
### Flush
```c
#pragma omp flush [(list)]
```
- forces the write-through of the local values of shared variables to the shared memory, so that all threads can see consistent values
- there are a lot of implicit flushes: barrier, critical (entry and exit), atomic, end of parallel and for regions
### Taskwait
```c
#pragma omp taskwait
```
- synchronization of the child tasks with the parent one
- the parent task blocks until all of its direct child tasks have completed 
- see [[PDP - OpenMP functional parallelism (directive task), semantics, parameters]]
### Cancel
```c
#pragma omp cancel [parallel | for | taskgroup] [if (expr)]
```
- user-controlled escape from a parallel region
- provides a mechanism to **prematurely leave** a parallel region when continuing is wasteful (e.g., when one thread has found the search result)
- the mechanism:
	1. If ICV `cancel-var` is true and `expr` is true, the thread executing `cancel` issues a **signal** for other threads to stop.
	2. The signaling thread jumps to the **final implicit barrier** to wait for the remaining threads.
	3. Other threads detect the cancellation at **cancellation points**: at barriers, at other `cancel` directives (regardless of their own `if` expression), and at explicit `#pragma omp cancellation point` directives.
- example:
```c
#pragma omp parallel for private(eureka)
for (i = 0; i < n; i++) {
    eureka = testing(i, ...);
    #pragma omp cancel parallel if (eureka)
    // ... other work if not eureka ...
}
```
- `cancel taskgroup` applies for the task-based parallelism and it terminates all tasks within a `taskgroup` scope
	- the `taskgroup` construct defines a scope where the creating thread waits for completion of all tasks (and their descendants) generated within it
### Summary comparison

|Directive|Who executes|Barrier at end|Purpose|
|---|---|---|---|
|`barrier`|all threads wait|(is the barrier)|global synchronization point|
|`master`|thread 0 only|no|serialize to master, others skip and continue|
|`single`|one arbitrary thread|yes (implicit)|serialize to one thread, others wait|
|`critical`|one thread at a time|no (mutual exclusion)|mutually exclusive access to shared resources|
|`atomic`|one thread at a time|no|atomic scalar memory operation (R/W/RMW)|
|`flush`|calling thread|no|force write-through of cached shared variables|
|`taskwait`|parent task|no (task-local)|wait for direct child tasks|
|`cancel`|signaling thread|jumps to final barrier|premature exit from parallel region|

---

### Potential exam questions

1. List all OpenMP synchronization directives and briefly explain the purpose of each.
2. What is the difference between `master` and `single`? Which one has an implicit barrier? When would you use each?
3. Explain the `critical` directive. What is the difference between anonymous and named critical regions? What happens when two anonymous critical regions appear in different parts of the code?
4. Why is `i += 2` not atomic? Explain the Read-Modify-Write decomposition and show what values are possible with 3 threads and initial value 10 without `atomic`. What value is guaranteed with `#pragma omp atomic update`?
5. Describe the four variants of `atomic`: `read`, `write`, `update`, `capture`. When is each needed? Why does `atomic` require an auxiliary variable when two memory locations are involved?
6. Compare `atomic` vs `critical` for protecting a single shared variable update. Which is more efficient and why?
7. Implement parallel reduction using a critical region. Compare its overhead with the `reduction` clause approach. What is the time complexity in both cases?
8. Explain the `cancel` directive. How do other threads detect the cancellation? What are cancellation points?
9. In the `Phase1/Phase2/Phase3` example, describe the execution order and what changes when each synchronization directive is removed.
10. Why does `flush` not solve the data race problem with `i += 2`? What does `flush` actually guarantee?
11. Explain `taskwait`. How does it differ from a general `barrier`? Why is it essential for recursive task-parallel algorithms?
12. Show how `atomic capture` can be used to implement dynamic partitioning of a shared array among threads. What is the equivalent scheduling clause?