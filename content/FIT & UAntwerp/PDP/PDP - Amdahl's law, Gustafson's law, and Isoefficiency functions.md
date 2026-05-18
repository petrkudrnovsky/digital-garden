### Parallel scalability
- definition: scalability is the property of a parallel algorithm to keep the parallel optimality if both $p$ and $n$ grow or shrink
	- the scalability expresses that larger problems can be solved in the same time as smaller problems if sufficient $p$ is utilized
	- it's not always about having a maximum number of processing units $p$ available
		- it's the scalability issue, we have to scale $p$ with $n$ to keep all cores busy all the time
- ![[Pasted image 20260224131558.png]]
	- T - time, E - efficiency, S - speedup
- there are two types of scalability:
	- strong scalability: measures the capability of a parallel algorithm for fixed $n$ to achieve linear speedup with increasing p (Amdahl's law puts strong limits to this)
		- alternatively: strong scalability is the measure of efficiency decrease if $p$ increases while $n$ is fixed
	- weak scalability: defines how the parallel time varies with $p$ for fixed $n/p$ 
		- alternatively: weak scalability is the measure of growth of $n$ such that a fixed efficiency is preserved when $p$ increases
### Amdahl's Law
- each sequential calculation has some part of it inherently sequential, call it $f_s$ 
	- the remaining part of $1-f_s$ could be parallelized
	- we assume the perfect parallelization without any overhead
- derivation: let $T(1) = T_s + T_p$ be the sequential time, with $f_s = T_s / T(1)$
    - under perfect parallelization the parallel time is $T(p) = T_s + T_p/p = f_s \cdot T(1) + (1-f_s) \cdot T(1)/p$
    - dividing $T(1)$ by $T(p)$ yields the formula below
- the formula ($n$ is fixed, $p$ varies): $$S(n, p) = \frac{1}{f_s + \frac{1-f_s}{p}}\le\frac{1}{f_s}$$- the law: 
	- the parallel speedup $S(n,p)$ will always be bounded by $\frac{1}{f_s}$ no matter how many threads are used
	- it measures the parallel algorithm efficiency decrease if $p$ is increasing while $n$ is fixed
		- that every added processor has more marginal benefit
		- after a certain value of $p$, adding new processors does not make sense, there is not enough parallel work to do (see the above plots)
			- that is called the saturation limit
	- it basically says that a fixed size problem provides a limited amount of parallellism (there is a limit of a reasonable number of parallel threads to execute it)
- examples:
	- if $f_s=10\%$, the speedup will be $S(n,p)\le10$ for any $p$
		- no matter how many processors in parallel I use, the speedup will be at most 10 times faster (because the non-parallelizable 10 % of sequential time will be a bottleneck)
	- if $f_s=20\%$, the speedup $S(n,p)\le5$ for any $p$
### Gustafson's Law
- weak scalability: if we scale $n$ and $p$ linearly together, a constant efficiency can be maintained
	- and thus the linear speedup and optimal cost
	- meaning, if we add more computing units $p$, we should also scale up the problem size $n$
	- it measures the $n$ growth when we preserve the efficiency while increasing $p$
- the non-parallelizable sequential portion stays constant (I/O operations, initialization...), so as we add processors $p$ and proportionally grow the problem $n$, the parallel portion scales linearly, hence overall speedup is linear in p
- the formula (here $f_s$ is the sequential fraction of the parallel execution time on the scaled problem, not of the serial time): $$S(n, p) = f_s + (1 - f_s) \cdot p = p - f_s(p - 1)$$
- reconciliation with Amdahl: the two laws are not contradictory, they answer different questions
    - Amdahl fixes $n$ and asks how speedup behaves with $p$ - strong scaling, pessimistic
    - Gustafson fixes time-per-processor and lets $n$ grow with $p$ - weak scaling, optimistic
    - the apparent disagreement comes from where $f_s$ is measured: Amdahl on the serial execution, Gustafson on the scaled parallel execution
### Isoefficiency functions
- when preserving the efficiency of the parallel algorithm, isoefficiency functions define the lowerbound and upperbound of the $n$ and $p$ relationship
- we can define some fixed efficiency that we want to achieve, e.g. 0.2 (20 %):
	- the isoefficiency functions specify:
		- the minimum n needed for a given p: $\forall n_p = \Omega(\psi_1(p)): E(n_p,p)\ge E_0$     
		- the maximum p allowed for a given n: $\forall p_n = O(\psi_2(n)): E(n,p_n)\ge E_0$     
	- to maintain the given efficiency guarantee
- we ask: "what minimum efficiency do I want to guarantee?"
	- and with isoefficiency functions, I can calculate the correct scaling of n and p to maintain this efficiency (when I have to option to remove/add computing units and increase/decrease the problem size)
- the isoefficiency is the property of the algorithm, not of the problem
	- different algorithms of the same problem can have differernt isoefficiency values
### Potential exam questions
1. Derive Amdahl's law from $T(1) = T_s + T_p$ assuming perfect parallelization of the parallelizable part.
2. State Amdahl's formula and compute the asymptotic speedup limit for $f_s = 5\%$.
3. What is the saturation limit and why does adding more processors beyond it not help?
4. State Gustafson's formula. How is $f_s$ defined differently than in Amdahl's law?
5. Are Amdahl's law and Gustafson's law contradictory? Explain.
6. Define strong scalability and weak scalability. Which law is associated with which?
7. Define efficiency in terms of sequential work $T_s$ and parallel overhead $T_o$, and explain how this leads to the notion of isoefficiency.
8. What does it mean for an algorithm to have isoefficiency function $\Theta(p \log p)$? Is this better or worse than $\Theta(p^2)$?
9. Derive the isoefficiency function of parallel reduction on $p$ processors.
10. Explain why isoefficiency is a property of the algorithm, not of the problem. Give an intuitive argument.