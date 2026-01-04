### Problems and algorithms
- almost all problems could be solved by bruteforce
	- but that often unfeasible in practice
	- each problem has an exact algorithm, that can find the optimum (the shortest path, the minimum of things etc.)
- but sometimes the price for finding the optimum is too high and for our usecases could the values close to the optimum be enough (that's often the case in practice)
- we can use heuristics to solve the problems faster
- problem is a task to be solved and an algorithm is the way, how to solve it
### Combinatorial problems
- we have:
	- set of input, output variables
	- constraints
	- optimization criteria (for optimization problems)
	- configuration variables - finite amount and finite domain, they describe the current state of the search for a solution
		- the values of configuration variables determine the current "configuration"
		- given by the problem and partially given by the algorithm
		- from them (together with input variables), for every instance:
			- the searched output can be constructed
			- the constraints can be verified
			- for optimization problems, the optimization criterion can be calculated
		- for constructive problems, the configuration variable often equals the output variables
			- because at the end of the algorithm, the current state (= configuration) is the desired output state
- all combinatorial problems could be solved by bruteforce (but often unfeasible in practice) BUT it finds the optimum
	- if there is finite amount of configuration variables
	- and the configuration variables have finite and discrete domains (= meaning they can have a finite number of different possible values)
		- this is the reason, why brute-force will always work (because it can try out all different combinations)
- to speed it up, we can use:
	- approximate, local methods
	- global methods
- vocabulary:
	- instance = assignment of input variables (= a task to solve)
	- configuration = assignment of configuration variables (= a current state of the instance, current state of the search for the solution)
	- solution = assignment of output variables satisfying the constraints
- types of combinatorial problems:
	- decision problem
		- exists a solution that satisfies constraints for given input variables?
	- constructive problem
		- construct a solution that satisfies the constraint for given input variables
	- counting problem
		- count the number of solutions that ...
	- enumerative problem
		- construct (several|all) solutions that ...
	- all those problems have also the optimization variant - so that the solution has to be better than any other solution according to optimization criteria
	- all those problems are computationally equivalent (in terms of the complexity class membership) - meaning, we can polynomial-time reduce between each other
- problem versions
	- simple optimization = we know the complete instance in advance before solving the problem
		- we can easily determine the optimization criterion (and calculate it in advance)
	- online optimization
		- we do not know the whole problem before solving
		- the solving is based on incoming requests
	- multi-criteria optimization
		- it's difficult to determine the "optimum"
			- is cheapest or fastest path better?
	- multimodal optimization
		- requires different suboptimal methods combined
### Types of problems
- The Knapsack problem
- The Traveling Salesman
- SAT = Boolean SATisfibility
	- determining if there exists a variable assignment making the Boolean formula true 
	- input is a Boolean formula in CNF (conjuctive normal form)
- Hamiltonian circuit
	- a problem of finding a continuous circuit in the graph including all vertices exactly once (all vertices in the subgraph have deg=2)
	- there does not have to be a solution (input could be a not-complete graph)
	- optimization criterion could be that the sum of the edge weight has to be minimized
### Complexity
- is the function of the instance size (for each problem the instance is encoded differently - but it is always derived from the $n$ items)
- types:
	- time-complexity - but it does not have to be measured only by the runtime, but by the number of comparisons (search), number of position switches (sorting), number of configurations processed, number of recursive calls (= nodes visited) etc.
	- memory-complexity
		- coarse-grain measure = num of items (nodes, variables etc.)
		- fine-grain measure = num of bits needed to encode the instance (it depends on the computational model, on the chosen encoding etc.)
### Asymptotic complexity
- big O (upper bound), big Omega (lower bound)
- real case for "big instances": big Theta
	- $f(n)=\Theta(g(n)) \iff f(n)=O(g(n)) \text{ and } f(n)=\Omega(g(n))$
- multiplicative constants are not considered
- we don't care about small instances
### Complexity of a problem
- problem $\Pi$ has complexity $O(f(n))$ if there exists an algorithm solving $\Pi$ in $O(f(n))$ 
	- but there could be better algorithm (we only know the upper bound)
- it could be mathematically proven that a problem is $\Omega(f(n))$, so there is not (even unknown) algorithm solving $\Pi$ with better complexity than $\Omega(f(n))$
	- after a proof, we know, that the problem cannot be solved faster than $\Omega(f(n))$ 
***
## What You Should Know from Lecture 1 (AI summary)

### 1. Difference Between Problem and Algorithm
- **Problem** = formal specification of task (input, output, constraints, optimization criteria)
- **Algorithm** = method to solve the problem
- Many algorithms can solve the same problem with different complexities
- Problem complexity vs. algorithm complexity
### 2. Definition of Asymptotic Complexity
- **O (Big O)** - upper bound, worst case: f(n) = O(g(n)) ⟺ ∃c > 0, ∃n₀: ∀n > n₀: f(n) ≤ c·g(n)
- **Ω (Big Omega)** - lower bound, best case: f(n) = Ω(g(n)) ⟺ ∃c > 0, ∃n₀: ∀n > n₀: f(n) ≥ c·g(n)
- **Θ (Big Theta)** - tight bound, real case: f(n) = Θ(g(n)) ⟺ f(n) = O(g(n)) AND f(n) = Ω(g(n))
- Multiplicative constants are ignored
- Only behavior for large instances matters
### 3. Complexity Measures
**Time complexity measures:**
- Instance size (n)
- Characteristic operations (comparisons, swaps, configurations processed, recursive calls)

**Memory complexity measures:**
- Coarse-grain: number of items (nodes, variables, items)
- Fine-grain: number of bits needed to encode instance

**Types:**
- Maximum complexity (worst case)
- Average complexity (typical case, experimental)
### 4. Algorithm Complexity vs. Problem Complexity vs. Average Complexity
**Algorithm complexity:**
- Complexity of a specific algorithm
- Can be analyzed theoretically (worst case, best case, average case)

**Problem complexity:**
- **Upper bound O(g(n)):** If there exists an algorithm solving the problem in O(g(n))
  - But a better algorithm might exist
- **Lower bound Ω(g(n)):** If every algorithm (even unknown) requires at least Ω(g(n))
  - Must be mathematically proven
  - No better algorithm can exist

**Average complexity:**
- Must be derived experimentally
- Shows real-world behavior
- Can differ significantly from worst-case theoretical bounds
- More representative of practical performance