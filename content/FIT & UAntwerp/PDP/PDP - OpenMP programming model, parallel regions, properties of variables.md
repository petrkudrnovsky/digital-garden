
> [!tldr] First 5 minutes of hell
> OpenMP is a high-level API for shared-memory parallel programming (runs in each node, handles communication). It uses parametrized directives for the compiler, global variables and library of the system operations to parallelize the program run.
> 
> OpenMP utilizes the fork-join programming model: the program starts with one initial thread that runs sequentially (other threads are initiated and waiting in the thread pool). The program run then consists of multiple parallel regions, where specified teams of threads are forked from the main thread, run the computations and then are joined back at the end (by an implicit barrier). 
> - if some of the threads fails prematurely, then all threads are terminated, resulting in the program's failure
> - the programmer is fully in control and is responsible for handling all parallelism challenges
> 
> OpenMP supports loop (data) and functional (task) parallelism model + it supports a relaxed consistence memory model (= threads can keep local values in their cache and not write-through them to the main memory immediately, explicitly, we can use `flush()`)
>
>Parallel region is defined by the `#pragma omp parallel (parameters)`, which uses the OpenMP API to wake up the `num_treads(n)` threads, run the parallel block of code and then go back to sleep in the thread pool.
>- the number of threads is fixed during the parallel region
>- the regions could be nested 
>
>Properties of variables (each variable gets an OpenMP property):
>- shared: are shared (visible) among threads
>- private: private, uninitialized variables for each thread
>- firstprivate: private, but initialized to the master's value before going into parallel region
>- default: set a default for all unspecified variables 
>- reduction: the variable is private to each thread, each thread populates it separately and then at the end of the parallel region, all results are reduced into one variable using the specified reduction operator (+, -, \*, etc.)
>- threadprivate: this defines "private" on the global level, each thread has it's own copy that is persisted across parallel regions
>- the properties of pointers apply to the pointers, not underlying objects
### POSIX threads vs. OpenMP - why OpenMP exists?
- the standard for the multi-threaded programming: POSIX
	- low-level, requires expertise, complicated to learn/debug
	- originally designed for kernel operations
- OpenMP library
	- a high-level API for multithreaded programming on a shared memory
		- it runs within every node in the parallel computer
		- internode communication is handled by OpenMPI
		- node = multicore shared memory system
	- it is portable across platforms and standardized (+industry support)
	- do not use it with other multithreaded libraries, it may interfere with each other
	- consists of 
		- parametrized directives for the compiler
		- global variables
		- library operations of the runtime environment
- programmer's responsibility:
	- check data dependencies, data races, data access collisions, or deadlocks
	- ensure the efficient utilization of the shared memory

---
### The fork-join programming model
- the run of the application is divided into regions that could be parallelized
	- the program starts with a single initial thread that executes sequentially
	- at each parallel region, "teams of threads" that are forked from the initial thread and then joined back together at the end of the parallel region (there is a implicit barrier)
	- these parallel regions can be nested
- OpenMP creates a thread pool with threads waiting to join the computations (to save time on creating/deleting threads on every fork-join)
	- after the synchronization, the threads are returned to the pool and waiting for the next fork
	- almost everything is the programmer's responsibility (data types, racing conditions, access collisions etc.)
	- no optimizations included
	- avoid thread-unsafe operations in the parallel regions
```
master ──●──[fork]──┬──thread 0──┬──●──[fork]──┬──thread 0──┬──●── master
                    ├──thread 1──┤             ├──thread 1──┤
                    └──thread 2──┘             ├──thread 2──┤
                                               └──thread 3──┘
         sequential    parallel    sequential     parallel    sequential
```

> If during a parallel region any thread is **prematurely terminated**, then **all threads of all teams** are terminated and the entire OpenMP program is killed.
### OpenMP parallel regions
- syntax for creating the parallel region:
```c
#include <omp.h>
#pragma omp parallel [clause[ [,] clause] ...]
{ structured block of parallel region }
```
- when a thread goes into a `parallel` region, it becomes the master thread with rank 0 for the new team of threads
	- main clauses:
		- `if(condition)` - if the condition is satisfied, a new team of threads is created
			- if false: no team is created and the code runs sequentially
		- `num_threads(expr)` - ets the number of threads in the team
	- variables have their modes:
		- `shared(list)`
			- variables in the list are visible to all threads (cannot be arrays or structures)
			- programmer is fully responsible for the synchronization
			- ! warning: if the variable is a pointer, it's properties apply only on the pointer, not on the data it points to
		- `private(list)`
			- each thread has it's own uninitialized copy of the variable and it is destroyed at the region's end
			- after completing the region, the values are back to values it had before the parallel block
		- `firstprivate(list)`
			- like private variables, but they are initialized to the values in the master before coming into the parallel region
		- `default(shared|none)`
			- sets the default mode for all unspecified variables
			- `none`: programmer needs to specify the value for all variables
		- ! warning: if the variable is a pointer, it's properties apply only on the pointer, not on the data it points to
- if any thread in the parallel region fails during computation, the runtime terminates all threads in all teams resulting in the program fail
- the number of threads is fixed during the whole region
	- it is not allowed to jump out of a parallel region (or jump in from outside)
- at the end, there is always an implicit barrier to synchronize the threads before continuing
- nested regions:
	- one of the threads can be a master of it's own team of sub-threads
	- the depth of this recursion is bounded by the hardware limitations
### Reduction property 
- reduction is really important and handy
- = `reduction(operator:variable)`
- each thread has it's own copy (the mode is `private`) initialized by identity element of the operator (0 for sum, 1 for multiplication) that is being accumulated by itself
- at the end, all copies of that variable are reduced together using the reduce operator (+, -, * etc.)
	- the + operator is not truly associative for floats (so the result may differ depending on the order, but it is generally acceptable)
- there are two reduction implementations:
	- linear (sequential): the result from each array is reduced one by one (using the reduction operator) - this is used by OpenMP
	- logarithmic - it has fewer operations, but the synchronization barriers are required in every parallel step, which causes huge overhead -> linear is used in OpenMP (as $p$ is usually much smaller than $p$, so the linear time is negligible)
- cannot be combined with task directive
### Threadprivate property
- for implementing a counter or other accumulative variable across multiple parallel regions
	- something like `private(list)` property, but on the global scope, the values are not destroyed at the end of the parallel region, but they persist through multiple parallel regions
	- in all following parallel regions there must be the same amount of threads in each
	- the property definition must precede all parallel regions
- as in the private(list) property, the values in the first parallel region are undefined
	- on the local level, this is handled by firstprivate()
	- on the threadprivate level (global level), this is handled by the `copyin` clause (which basically copies some master-thread value into each thread)
### OpenMP memory model
OpenMP supports a **relaxed consistency memory model**. Threads can keep **local copies** of shared variables in cache memory and are **not forced** to write-through every local update immediately into shared memory.

If the programmer needs to ensure that all threads see identical shared memory contents, they must explicitly force a synchronization - for example, using the `flush()` operation to flush local values of shared variables into shared memory. Certain directives (barriers, critical regions, atomic operations) include implicit flushes.

---
### Determining the number of threads in a parallel region
- threads are numbered from 0 (master) to p - 1
- the number of threads is determined by the first rule in this order:
	1. The `if(condition)` clause is evaluated. If false → p = 1 (sequential execution).
	2. The value of `num_threads(expr)`.
	3. The value of the last call to `omp_set_num_threads(expr)`.
	4. The environment variable `OMP_NUM_THREADS`.

If none of these are specified, the value is **implementation-dependent** (typically the number of CPU cores).
- number of threads could also be controlled through ICV (internal control variables) - team size, nesting depth etc.
	- accessible only through the OpenMP API calls
	- e.g. dyn-var (boolean), it controls the dynamic adjustment of the number of threads in the parallel regions is enabled
		- if the dyn-var is enabled, the system may give fewer threads than requested
		- if dyn-var is disabled and more threads than available is requested, the behavior is implementation defined

---
### Potential exam questions

1. Describe the OpenMP fork-join programming model. What happens at the beginning and end of a parallel region? What is the role of the initial thread, the master thread, and the thread pool?
2. List the three main components of the OpenMP API. Why should OpenMP directives not be mixed with other threading libraries?
3. What are the motivations for OpenMP? What are its limitations (name at least four)?
4. Explain the general syntax of an OpenMP construct. What is a "combined construct"? Give an example.
5. Describe the `parallel` directive and its main clauses (`if`, `num_threads`, variable property clauses). What happens when the `if` condition is false?
6. Explain each variable property: `shared`, `private`, `firstprivate`, `default`, `reduction`. For each, state what the initial value is inside the region and what happens to the variable after the region ends.
7. What is the caveat with pointer properties in OpenMP? If a pointer is declared `shared`, what does that mean for the data it points to?
8. Describe the `reduction` clause. What operators are permitted? Why may the result differ slightly for floating-point reductions? Why does OpenMP use a linear implementation rather than a logarithmic one? Give both complexity formulas.
9. Explain the `threadprivate` directive. How does it differ from `private`? What is `copyin` used for? What constraint exists on the number of threads across regions?
10. Describe the priority ordering for determining the number of threads p in a parallel region. What role do ICVs play? What happens when `dyn-var` is true vs. false?
11. What happens if any thread in a parallel region is prematurely terminated?
12. What does "relaxed consistency memory model" mean in OpenMP? When must the programmer intervene to ensure memory visibility?