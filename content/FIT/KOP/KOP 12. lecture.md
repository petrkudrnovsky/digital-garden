# Global methods
- these methods consider the whole space (or the concept of the state space is not defined at all)
- examples:
	- decomposition-based (e.g. dynamic programming) - true global methods
	- some variants of genetic and stochastic algorithms and approaches
### Stochastic methods
- these methods try to explore the whole state space by restarting from different state space points
- examples:
	- parallel runs of local search algorithm (each has started in different state space point)
	- restarting a randomized local search algorithm (e.g. SA)
	- mutations in GA (by the mutation, the algorithm "jumps" to different part of the state space)
	- island GA, cellular GA (heavy parallelization of different parts of the search space)
	- clustering methods
		- random samples in the whole state space are made -> choose X best states from those samples and
			- sample their neighborhood OR
			- run local search methods from these reference points
		- based on the idea that good samples can cluster around global/local optimums
### Decomposition
- based on the idea of decomposing the problem instance into smaller instances, solving those instances and then constructing the final solution from the subsolutions
	- the decomposed problems are the same, but the instance size is smaller
	- the solutions of subproblems are at the end composed into the final solution
- different types of decomposition according to ability to compose a final solution:
	- if composing a solution from two subproblems, both subsolutions have to be present (otherwise there is no resulting solution)
	- OR if only one solution can be present in order to get a solution
	- (if any of the branches does not have a solution - the result of course also does not have a solution)
- different types of decomposition based on depth:
	- linear depth - the size of the the subproblem instance is smaller by 1 (or any other constant)
	- logarithmic depth - the size of the subproblem instance is half of the original instance
##### Reduction
- a type of decomposition, where one of the obtained subinstances has a trivial solution
##### Approximate decomposition
- if we get optimum solutions of subinstances $S_1$ and $S_2$ and the constructed solution $S$ is not necessarily optimum
- if $S_1$ or $S_2$ do not exist -> we don't know anything about $S$ 
- in general, we are able to get at least some solution, optimum is not guaranteed
##### Pure decomposition
- if we construct an optimum solution $S$ from two optimal solutions of subinstances $S_1$ and $S_2$ 
- if $S_1$ or $S_2$ do not exist -> we know that $S$ does not exist as well
- in general, at least one optimum solutions can be obtained
##### Proper decomposition
- if we can construct all optimum solutions $S$ of instance $I$ from **some** optimum solutions of two subproblems
	- so if any of the subproblem solutions are optimal, in proper decomposition, I am able to construct a optimum solution
- in general, all optimum solutions can be obtained
### Decomposition in global methods
- the global method is recursive 
	- if the current instance is trivial -> we get quick solution and return
	- try the best decomposition possible (if cannot, proceed to "worse" decomposition):
		- 1) proper decomposition
		- 2) pure decomposition
		- 3) approximate decomposition
	- if we didn't get a valid solution from any of these -> the final solution is not known
	- only proper and pure decompositions are able to tell that the solution CANNOT exist
- there is a trade off between finding solution by approximate decomposition or to try to find another starting instance and try to do the proper decomposition with the goal of finding the optimum
	- depends on the use-case
	- most of the time, it's better to continue with the approximate decomposition to find a almost-as-good-as-optimum (approximation) faster
### Dynamic programming
- it's a form of decomposition approach, only pure decomposition is used
- each decomposed instance can be characterized/described by a small number of values
	- and the solutions of those decomposed instances can be indexed by these values
	- decomposed instances are divided into disjoint classes and solutions from one class are needed to compute the solutions of another class 
		- e.g. when decomposing Knapsack problem by capacity - there are "classes" of solutions at X items
- memory (cache) is employed (memoization) to store intermediate results
- different techniques:
	- recursive (not used in practice)
		- start from some given instance
		- determine subinstances that necesarilly need to be solved
		- descend recursivelly to trivial solutions
		- compose instance solutions from computed subinstances
	- forward-only (the correct way)
		- start from the trivial instance
		- compute all (nontrivial) subinstances until the solution is reached
- how to?
	- make a recursive formulation of the problem
	- characterize subinstances and define the memory structure (of intermediate results)
		- the intermediate results have to be reused (no multiple calculations of the same problem)
	- determine the order of the instance decomposition (to make use of the reusing subinstances)
### Complexity of global methods
- all global methods are rather exponential, we use local optimum based methods/functions to find the approximate solution in polynomial time - that's more usable in business cases
