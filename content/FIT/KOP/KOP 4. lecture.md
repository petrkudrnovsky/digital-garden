- optimalization problem classes (with the O appendix - like Optimization)
### PO class
- same as P class, but for optimalization problems
	- solution size is polynomial with the instance size
	- the problem solution can be verified in polynomial time
	- the optimization criterion can be computed in polynomial  time
	- the solution is generated in polynomial time
### NPO class
- only difference with PO class is that the NPO class problem solutions cannot be generated in polynomial time
- if an optimization version is NPO, the decision version is in NP (other versions are not simpler)
- proving NPO class
	- very similar to proving the NP class
		- polynomial constraints verification 
		- + polynomial optimization criterion verification
### Optimization problems
- optimization of the cost function (also called optimization criterion)
	- cost function gives us a "cost" or "score" for each admissible solution of the problem
- we have:
	- minimization problems
		- we are finding the minimal "cost" or "score" of all admissible solutions 
	- maximization problems
- we do not require the real optimum (if I make a mistake, I will still have some solution (even it's not optimal))
	- with the decision problems - the error is crucial!!
	- this is the engineering approach
### Pseudo-Polynomial algorithms
- algorithms, where the complexity does not depend only on the instance size
- e.g. Knapsack by dynamic programming with decomposition:
	- by cost: $O(n^2*C_{max})$ - if the $C_{max}$ is too high, the algorithm's complexity will be high
		- using inverse knapsack problem (meaning, we have given cost and the algo is trying to minimize the weight (instead of maximizing the cost for given weight))
	- by weight: the same, but by weight
		- if the cost or weight would be real numbers instead of integers, the complexity will be infinite
- we have polynomial complexity, but if we have some other variables, which do not depend on the instance size -> they can be arbitrarily large (not depending on the instance size)
- dynamic programming version of Knapsack has complexity
	- $O(n*M)$ 
		- $M$ could be arbitrarily large
		- and if $M$ will be given in real numbers (and not integers), there will be an infinite number of possible $M$ sizes, making the dynamic programming approach impossible
- BUT dyn. programming itself does not imply that the algorithm will be always pseudopolynomial
	- e.g. for Knapsack - it is pseudopolynomial
	- e.g. for Fibonnacci numbers it is not pseudopolynomial (no added variable)
### Approximation algorithms
- they operate with relative errors, they do not provide the exact optimum solution (which may be hard to compute)
- definition: it is an algorithm for an optimization problem solving each problem instance with guaranteed finite error
- 𝑟(𝑛)-approximation algorithm produces solutions at most 𝑟(𝑛)-times worse the optimum
##### How do we measure the quality?
- relative error
	- error of the whole algorithm means that the algorithm for every input will produce the solution with error less (or equal) to this error
		- maximum possible error of all inputs is the relative error of the whole algorithm
		- if the relative error is 0 for all inputs, the algorithm produces optimum solutions
	- the formula is different for maximization and minimization problems
		- hint: we always want the relative error be positive
- performance guarantee
	- if the algorithm has a perf. guarantee of infinity - we have no guarantee of the maximum possible error
	- if the performance guarantee is finite, then the maximum error is guaranteed and is finite
		- if the perf. guarantee is equal to 1 -> the algo produces optimum solutions
	- $\varepsilon=1-\frac{1}{R}$ 

- r(n)-approximation algorithm
	- this algorithm is solving a particular problem with guaranteed finite error of $R_A=r(n)$, where $n$ is the instance size ($r(n)$ is the function of instance size)
	- this algo produces solutions that are at most $r(n)$ times worse than the optimum
### APX class
- when it is possible to design an algorithm solving every instance of the NPO problem in polynomial time with finite and guaranteed error
- these algos have the guaranteed constant maximum error
	- the $r(n)$ function is constant, independent on the instance size
- e.g.: 2-approximation algorithm
	- $R=2$, $\varepsilon=1-\frac{1}{R} \iff \varepsilon=\frac{1}{2}$, so the guaranteed error is max. 50 % 
	- example is the extended heuristic on the Knapsack problem
- so problem belongs to APX when there is an polynomial-time approximation algorithm for the problem
	- if we can scale the error arbitrarily down $\implies$ it is [[#PTAS]]
### PTAS
- = polynomial time approximation scheme
- algorithms that can solve problems in polynomial time for any given non-zero error
	- so I can specify the error and the algorithm will solve it with respect to this error in polynomial time
	- but it could be exponential relative to the error
- warning! It solves in polynomial time with the size of the instance, NOT the specified relative error
	- time complexity may grow exponentially with decreasing error
### FPTAS
- here the time will by always polynomial with the instance size and polynomial with the error (with any given guaranteed error)

- there could be NPO algorithms that cannot be approximated at all (are not APX)
- ![[Pasted image 20251113230538.png]]
### AP reduction
- during the reduction - the error (= performance guarantee) could be modified (by a constant)
	- but we cannot make it simpler
- $\Pi_1 \le \Pi_2$: $\Pi_1$ is reducted to $\Pi_2$ 
- so we have APX-Hard problems
	- all problems in APX can be reduced to an APX-Hard problem
- APX-complete problem
	- is APX-Hard and belongs to APX
### Good news and bad news in the composium
- APX-complete is bad news, because we cannot specify the error level
	- but there is always a some finite R bounding the error level
- NPO-complete is the worst one
	- =most complicated to find the optimum
	- and in addition, we don't have the finite R bounding the error