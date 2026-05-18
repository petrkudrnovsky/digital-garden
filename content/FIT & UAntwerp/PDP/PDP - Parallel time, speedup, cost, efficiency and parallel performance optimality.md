### Prerequisities
##### Sequential time complexity
- parameters which influence the time complexity: size of the data, algorithm used, what problem do I solve
- every problem has a sequential lower bound (it doesn't have to be known)
	- typical (trivial) lowerbound is the size of the data (because for solving the problem we need to read the data)
	- this lower bound cannot be beaten in the future
- upper bound is set by the complexity of the worst-case run of the best known sequential algorithm
	- e.g. sorting (we know the fundamental lower bound and we have algorithms that reach it)
	- e.g. matrix multiplication (trivial lower bound is $\Omega(n^2)$, but we didn't discover that fast algorithms (yet)) - or the true lower bound is different (we don't know that also)
- sequential optimality
	- a sequential algorithm $A$ for problem $K$ is optimal iff $T_A(n) = \Theta(SU^K(n)) = \Theta(SL^K(n))$ (the best known algorithm matches the lower bound)

---
### Parallel time $T(n, p)$
- parameters: same as sequential + number of processors, threads, processes etc. 
	- $n$ - amount of data
	- $p$ - number of processors/threads/processes (depending on the context)
		- it says nothing about their organization etc., it is simplified
- **definition:** $T(n, p)$ = the time elapsed from the beginning of a parallel computation until the last thread finishes the execution
	- consists of two components:
		- **computational time**: arithmetic, logical, memory operations
		- **communication and synchronization overhead**: shared data access, message passing, barrier synchronizations, mutual exclusion, etc.

- the whole parallel programming is about a trade-off with the speed gain and costs (increased overhead costs, distributed data etc.)
	- we need to account for overhead latencies related to communication and synchronization
	- e.g. broadcast part in array search (distributing the information to all computing nodes what to search for)
	- balancing the computation itself against the overhead costs imposed by distributing both computation and data
- example - parallel search in unsorted shared array
	- given $n$, what is the best choice of $p$ to minimize time?
		- small $p$: local search dominates
		- big $p$: communication overhead dominates
$$
T(n, p) = \underbrace{O(\log p)}_{\text{broadcast } x} + \underbrace{O(n/p)}_{\text{local search}} + \underbrace{O(1)}_{\text{store result}}
$$
- parallel time lower bound $L^K(n, p)$:
	- it's only theoretical minimum parallel time (counting with zero overhead)
$$
L^K(n, p) = \frac{SL^K(n)}{p}
$$
---
### Parallel cost $C(n, p)$
- = processor-time product
- definition:
$$
C(n, p) = p \cdot T(n, p)
$$
	- the cost we need to pay for the parallel computation
		- I rent 8 CPUs for 6 hours, I will pay for 48 CPU-hours
	- this is the simplest calculation (the cores are assigned statistically (based on previous costs) and I have to pay even for idle cores)
- the best I can do is sequential complexity (in terms of the cost)
	- we cannot have a parallel algorithm that has a lower cost than a sequential complexity
		- that would imply that by simulating the parallel algorithm sequentially would yield a faster sequential algorithm - that's a contradiction
	- = the total work done is asymptotically the same as the best sequential algorithm
- cost optimality
	- means you are not wasting parallel resources - the total work done by all processors is asymptotically the same as the best sequential algorithm

---
### Parallel speedup $S(n, p)$
- how many times faster the parallel execution is compared to the best known sequential algorithm
- definition:
$$
S(n, p) = \frac{SU(n)}{T(n, p)}
$$
	- $SU(n)$ is the sequential upper bound (the worst-case time for the fastest sequential algorithm (if the parallel time is worse than $SU(n)$, it does not make sense to parallelize the algorithm))
- the best speedup is $p$: ($p$ times faster with $p$ processors), in reality, it's often less (due to overhead costs)
	- + the speedup cannot be greater than $p$ (you cannot do better than $p$ times faster with $p$ processors (in the asymptotic sense))
- linear speedup is the goal (if $p$ increases $k$ times, the total computation time ($T(n,p)$) should decrease $k$ times)
	- it depends on the degree of data independence (more independent = more options to paralellize)
- superlinear speedup (speedup exceeds $p$)
	- better speedup caused by hardware limitations with the sequential approach
		- memory effect: where sequentiall algorithm requires more main memory, resulting to disk swapping (and cumulative memory of the parallel system holds everything in RAM, avoiding swapping entirely)
		- state-space search anomaly: parallel search may explore a different part of the search tree first and find the solution faster by luck
	- this is rather an exception

---
### Parallel efficiency $E(n, p)$
- = a relative utilization of computational resources during a parallel computation
- it will never be 100 % due to overhead (communication and synchronization overhead)
- if we scale and the efficiency remains the same, we have an optimal parallel algorithm
- definition:
$$
E(n, p) = \frac{SU(n)}{C(n, p)} \leq 1
$$
**Lemma:** $E(n, p)$ is the speedup per core:
$$
E(n, p) = \frac{SU(n)}{C(n, p)} = \frac{S(n, p) \cdot T(n, p)}{p \cdot T(n, p)} = \frac{S(n, p)}{p} \leq 1
$$
##### Constant efficiency
- **Definition (Constant efficiency):** Given a constant $0 < E_0 < 1$, a parallel algorithm has constant efficiency if $E(n, p) \geq E_0$, i.e., asymptotically $E(n, p) = \Omega(1)$.

---
### Parallel performance optimality theorem
- parallel optimality
	- these are equivalent:
		- parallel algorithm is cost-optimal
		- it has linear speedup
		- it has a constant efficiency
**Theorem (Parallel performance optimality):** The following three conditions are equivalent for a parallel algorithm:
$$
\begin{aligned}
&\text{(1) Cost-optimal:} && C(n, p) = \Theta(SU(n)) \\
&&& \Longleftrightarrow \\
&\text{(2) Linear speedup:} && S(n, p) = \Theta(p) \\
&&& \Longleftrightarrow \\
&\text{(3) Constant efficiency:} && E(n, p) = \Omega(1)
\end{aligned}
$$
These three measures express the same thing about the quality of a parallel algorithm. Verifying any one of them establishes optimal parallel performance.

---
### Summary of definitions and relationships

| Measure | Definition | Bound |
| --- | --- | --- |
| Sequential lower bound | $SL^K(n)$ | fundamental limit |
| Sequential upper bound | $SU^K(n)$ | best known algorithm |
| Parallel time | $T(n, p)$ | time until last thread finishes |
| Parallel time lower bound | $L^K(n, p) = SL^K(n) / p$ | theoretical minimum |
| Parallel cost | $C(n, p) = p \cdot T(n, p)$ | $\Omega(SU(n))$ |
| Parallel speedup | $S(n, p) = SU(n) / T(n, p)$ | $O(p)$ |
| Parallel efficiency | $E(n, p) = S(n, p) / p$ | $\leq 1$ |

The equivalence theorem: **cost-optimal $\Longleftrightarrow$ linear speedup $\Longleftrightarrow$ constant efficiency**.

---
### Potential exam questions

1. Define $SL^K(n)$ and $SU^K(n)$. What is the trivial lower bound? When is a sequential algorithm optimal vs. merely the best known?
2. Define parallel time $T(n, p)$. What two components does it consist of on a real parallel computer?
3. Define parallel cost $C(n, p)$. Prove that $C(n, p) = \Omega(SU(n))$ (Lemma 5).
4. Define cost optimality. Why does Lemma 5 imply that cost optimality is equivalent to $C(n, p) = \Theta(SU(n))$?
5. Define parallel speedup $S(n, p)$. Prove that $S(n, p) = O(p)$ (Lemma 9).
6. Define linear speedup. Is $S(n, p) = 0.5p$ considered linear speedup? Why or why not?
7. Under what circumstances can superlinear speedup occur? Does this violate the theoretical bound $S(n, p) \leq p$?
8. Define parallel efficiency $E(n, p)$. Show that $E(n, p) = S(n, p) / p$.
9. State and prove Theorem 16 (parallel performance optimality) - the equivalence of cost optimality, linear speedup, and constant efficiency.
10. Define the parallel time lower bound $L^K(n, p)$. Compute it for comparison-based sorting with $p = n$.
11. Given the parallel search example with $T(n, p) = O(\log p) + O(n/p) + O(1)$, what happens if $p$ is too large relative to $n$?