
> [!tldr] 20% to remember
> Randomized algorithms are just different ways how to solve some problem. They are based on random choice, are mostly iterative, greedy, polynomial and only statistical properties can be guaranteed (e.g. they only have average errors). They are not more powerful than deterministic ones, but in some cases, they can be more efficient. But for every randomized algorithm, we can find a detereministic algorithm doing the same.
> 
> There are two major types of randomized algorithms:
> - Monte Carlo: runtime is constant, the result is random
> 	- Miller-Rabin primality test, GSAT, Random Walk SAT 
> - Las Vegas: the result is always optimum, the runtime is random
> 	- Randomized Quick-sort
> 
> Runtime vs. quality trade off = the longer it runs, the better the solution probably is. It depends on you to stop the algorithm.
> 
> Derandomization = a process of creating a deterministic algorithm from a randomized one. The time complexity remains polynomial and the relative error is not worse than the average error of the randomized algorithms.
> 
> Randomness "from outside", by changing the order of inputs, their encoding or structures, we can change the runtime or results of deterministic algorithms. This is because of using heuristics, hashing by names or using ordering-dependent structures.

- two major types: [[#Monte Carlo]] and [[#Las Vegas]]
- mostly iterative and greedy algorithms with polynomial time
- are based on random numbers, only statistical properties can be guaranteed
# Monte Carlo
- the time is given (it will run always the same amount of time = constant runtime) and the result is random
### Randomized MAX-SAT
- maximizes the number of satisfied clauses
	- randomly set each variable to 0 or 1 with 50 % probability and see what is the result (= how many clauses are satisfied)
- the longer you're running, more likely you will find better solution
	- if there is no solution, it will be running forever
### Miller-Rabin test of primality
- randomly selecting $k$ and verifying the equations (based on Little Fermat's Theorem)
	- $k^{n-1}\equiv 1 \mod n$ 
- the longer it runs (the higher number of generated $k$ is) the smaller is the chance of being non-prime 
	- after 100 numbers the probability is really small (BUT it is not proven)

- a lot of randomized algorithms consists of two phases
	- a randomized phase = constructive
	- a deterministic phase = iterative
	- for example: Randomized SAT, GSAT, Random Walk SAT etc.
		- random selecting and deterministic iterating (if better configuration is found)
***
# Las Vegas
- the result will always be optimum, the runtime is random variable (it may run random time)
### Randomized Quick-sort
- the result is always the optimum (=sorted array)
- with random pivot selecting, there cannot be "artificially" crafted inputs for which the pivot will for sure fail (as in the normal quick-sorts, where the pivot is selected in a deterministic way)
- it is proven that the average complexity is $O(n\ log(n))$ 
	- it is almost impossible to find an example with $O(n^2)$ complexity for a randomized quicksort
***
# Third view of randomness
- randomness from outside, randomness "noise" etc.
	- example of a Knapsack
		- if I permute the order of the inputs, we can get different ("random") results from a deterministic algorithm
	- it has a lot of other examples
		- change of procedure (different language constructs doing the same thing) or inputs (different ordering, encoding, identifiers etc.) can lead to different results (or different time etc.)
- there is a run-time vs. quality trade-off
	- the longer it runs, the better results we may get
### Derandomization
- process of taking a random algorithm and creating a deterministic algorithm derived from it
- for every randomized algortihm you can define a deterministic algorithm doing the same
	- the randomized algos are not computationally better than deterministic ones (but they can be, for example, way faster than deterministic ones)
***
# Experimental evaluation
- testing algorithms experimentally
- the form of the experiment
	- question -> running the experiment -> evaluating/interpreting the results
- notes:
	- random clones = randomly generated inputs that have similar/some properties of the practical instances (used in practice)