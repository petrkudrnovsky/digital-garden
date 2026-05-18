- the OpenMP is primarily focused on data parallelism
- the directives `parallel` and `for` can be joined on a single codeline (as they are often used together)
```c
#pragma omp parallel for clause1 clause2 ...
for (int i = 0; i < n; i++) { /* loop body */ }
```
- `parallel` creates a team of $p$ threads
- `for` partitions the $n$ iterations among threads according the specified schedule and then all threads process the partitions concurrently
- there is an implicit barrier at the end of the for-loop (unless there is `nowait` clause)
	- `nowait` is useful, when two consecutive `for` loops have no data dependency between them (early threads can start the second loop even when other threads are still finishing the first loop)
- ! warning: if the data are not data independent, the for-loop may produce incorrect results (due to race conditions) - this is the programmer's responsibility
### Scheduling
- we can define the way the iterations in the for-loop are assigned to respective threads
- types:
	- static - least overhead, default
		- iterations are assigned before the execution begins
		- good when all iterations take up the same execution time
		- chunk size could be specified, default is that each thread gets roughly a block of $n/p$ iterations 
	- dynamic - whenever a thread becomes idle, the system gives him next iteration to execute
		- a master-slave pattern
		- chunk-size = 1 by default, useful when the iteration latencies are unpredictable (conservative approach)
		- good when the iteration time of each iteration vary 
		- has slightly higher overhead (increasing  the chunk size decreases the overhead - fewer scheduling decisions)
	- runtime
		- The scheduling type and chunk-size are determined at runtime from the environment variable `OMP_SCHEDULE`. This allows tuning without recompilation.
	- auto
		- Scheduling is left entirely to the compiler and operating system.
	- guided - also dynamic, for each assignment, it computes:
		- good when the iteration costs grow monotonically with the index (so batch cheap first iterations together and let smaller chunks handle the expensive finish) 
$$x = \max\left(\left\lceil \frac{\text{remaining unassigned iterations}}{p} \right\rceil, \text{chunk-size}\right)$$
- lecturer's advice:
	- latencies are the same => static
	- latencies are inpredictable => dynamic
	- latencies are growing => guided
	- if I don't know => dynamic
### Variable properties
- are the same (see [[PDP - OpenMP programming model, parallel regions, properties of variables]])
- a new: `lastprivate(list)` property:
	- has the value of the last iteration (not depending on which thread did it and when)
	- the value from this property is copied out of the parallel for region
- `reduction(operator:variable)` also works in the parallel for
### Handling nested loops
- by default, `#pragma omp for` applies only to the outermost loop
	- inner loops run in sequential
		- that is the most common - simple outer loop for parallelization, the inner loop runs sequentially within each thread
- with the clause `collapse`, we can balance the iterations among the threads by collapsing all nested iterations into a single, larger iteration space that is scheduled among threads
	- mapping the nested loop iterations onto the free threads
		- the OpenMP does the mapping itself

**Code 4** (outer loop only):
```c
#pragma omp parallel for
for (int y = 0; y < YM; y++)
    for (int x = 0; x < XM; x++)
        computeElement(x, y);
```

**Code 5** (collapsed):
```c
#pragma omp parallel for collapse(2)
for (int y = 0; y < YM; y++)
    for (int x = 0; x < XM; x++)
        computeElement(x, y);
```

- if the outer and inner data are data-dependent, there are multiple approaches:

**Code 6** - `parallel for` inside the outer loop (team created YM times):
```c
for (int y = 0; y < YM; y++)
    #pragma omp parallel for
    for (int x = 0; x < XM; x++)
        computeElement(x, y);
```

**Code 7** - `parallel` wraps everything, `for` on the inner loop (team created once):
```c
#pragma omp parallel
for (int y = 0; y < YM; y++)
    #pragma omp for
    for (int x = 0; x < XM; x++)
        computeElement(x, y);
```
- smaller overhead than Code 6, because the parallel region is created once and the team of processes stays the same
---
### Potential exam questions

1. Describe the syntax and semantics of `#pragma omp for`. What is the difference between `#pragma omp for` and `#pragma omp parallel for`?
2. List and explain the five scheduling types (`static`, `dynamic`, `guided`, `runtime`, `auto`). For each, state when it is most appropriate and what its overhead characteristics are.
3. Given n = 12 iterations, p = 3 threads, and monotonically growing iteration costs, compare the total execution time under `schedule(static)`, `schedule(static,1)`, and `schedule(guided)`. Explain the differences.
4. What is the purpose of the `collapse(i)` clause? Give an example where `collapse(2)` produces better load balance than the default. What is the overhead?
5. Explain why parallelizing the outer loop of Gauss elimination with `#pragma omp parallel for` produces incorrect results. What is the correct parallelization pattern?
6. Compare Code 6 (inner `parallel for` inside sequential outer loop) with Code 7 (`parallel` wrapping everything, inner `for`). Which is faster and why?
7. Explain the `lastprivate` clause. How does it differ from `private` and `firstprivate`?
8. How does OpenMP implement the reduction operation? Why is the linear implementation preferred over the logarithmic one? Give the time complexity formulas.
9. What happens if you omit `#pragma omp taskwait` (or the barrier) after a parallel loop where the result feeds into the next computation? Is `nowait` safe in this case?
10. Write an OpenMP pseudocode for the parallel dot product of two vectors using the `reduction` clause. Explain the variable properties.