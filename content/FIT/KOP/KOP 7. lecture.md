### Local vs. global methods
- global methods do not operate with the state space
### State space
- operators are moving around the state space
- state space is a pair of two sets: set of states and set of operators
- operation performs a move in the state space (= changing the values of conf. variables)
	- precisely: the change of configuration and internal variable values
	- configuration variables have to be able to describe all possible combinations
- important properties
	- by my state space I am able to get to any configuration 
		- **complete** = there have to be all states
		- the encoding has to be good (it has to be able to encode all states = configurations)
	- every state must be reachable from any state (and back)
		- this is more strict
		- = **continuous**
	- example of complete, but not continuous state space:
		- encoding of configuration variables can encode all configurations = states
		- but in the [[#State graph]] it is not possible to get from each state to another (and back)
### State graph
- there could be graph loops - but there are useless (they just represent "longer" ways to get from one state to another)
- move from state to another state is: operation
- it does not have to contain all configurations, but only those, which are possible (= pruning the search state)
### Neighborhood
- $k$-neighbourhood size grows exponentially with $k$ 

slide no. 15 - this state space is not continuous - it's wrong

slide no. 25 - number of all possible states in TSP is $n!$ 
- on the slide 25 there are only options starting from A and end in A
- there is a big difference between instance graph and state space
### State vs search state
- state space
	- we know about all states in the state space, each state has a cost, or it could be computed (for optimization problems)
	- one state = complete configuration (the assignment of conf. variables is complete)
	- all states are defined
	- state with the best cost is looked for (all final costs are given or could be computed)
	- examples: Knapsack problem, TSP etc.
- search space
	- item could have undecided parts of the solution = parts of the state = parts of the configuration
	- operations
		- decide (assign a value to undecided )
		- backtrack = "undecide", go back from assigned to undecided
			- this is often not used
	- **state space is a subset of the search space**
		- state space includes all "fully-assigned" configurations, meanwhile search space includes "fully-assigned" AND "partially-assigned" configurations
	- the (partial) cost of the state does not have to reflect the final cost of the state (= solution)
	- the number of visited states is usually minimized (optimization)
	- examples: Chess, Sokoban, planning problems, robot motion problems etc.
***
### Exploration strategies for state space
- complete state space exploration
	- brute-force, B&B
	- if the optimal solution exists, these methods will find them (but they are often really slow)
- systematic state space exploration
	- each state is visited only once
	- also brute-force, B&B
- iterative deepening
	- combination of DFS and BFS
	- continuous cutting by depth and the applying DFS from the start to explore the first part of the cut
- blind exploration strategies
	- BFS, DFS, iterative deepening
	- they do not know the problem structure
- informed search methods
	- they act on the problem nature and properties, often heuristic function is involved
	- heuristic function assigns each found state a cost
		- states with higher costs are preferred
	- example: A\*

slide no. 47 - if I have a priority queue and all the priorities will be the same, the items will be drawn in the random order (it is not a queue)
- in theory
- in practice, in different languages, the implementation differ

- Greediness means, that we are locally choosing the best option
- Heuristics means that there is some function made by a human driving the algorithm
	- there is nothing said about the finding an optimum result or not (some can, some cannot)
### Local heuristic methods
- not complete = the optimum is not guaranteed
- explores only a bounded neighborhood ($k$-neighborhood)
- constructive method = starting from scratch (the trivial state)
	- going through the search state (in most cases) and constructing the solution
- iterative method = starting from some state
	- the state could be randomly generated
	- going purely through the search space and improving the solution
	- they are converging/approaching the optimum
### Random walk
- is finished when all iterations are exhausted or time limit passes
- any move is taken (no matter the cost)
	- after each step, the best solution so far is saved
- not systematic (can visit same states again or go in a loop) and not complete
### First improvement
- somehow choose a new state (by performing an operation)
- making only moves that improve the solution
- it is systematic (it cannot return to visited state, since it only chooses better neighbors)
- but it takes the first better neighbor (there could be even better neighbors)
### Best only
- first try all neighbors and then take the best one
	- keeping the local best
### Best-first
- the best neighbor is taken first and other neighbors are put into the priority queue
- it is not local-based (just to compare it to the three local methods), it is complete
### Getting stuck in local optimum
- global optimum is the best solution possible
- local optimum - best cost in the $k$-neighborhood
- if an algoritm begins with different initial states and they all converge to the same optimum, maybe we have found the global optimum (but we cannot be 100 % sure)
- how to avoid local optimums:
	- increase $k$-neighborhood (but I get slower exploration)
	- use randomized algorithms (for random restarts)
	- allow returns ([[#Best-first]], backtracking) - these are not polynomial
### Handling unfeasible states
- = states not meeting the constraints
- death = do not admit infeasible states (if generated, generate a new one)
- repair = include operators that can "repair" an unfeasible state
- decoders = choose encoding such that only valid states could be generated
##### Relaxation
- incorrect configurations are admitted, but are penalized (proportionally to their "incorrectness")
- every infeasible configuration has to be worse than any feasible solution (even the worst feasible configurations)
- why use relaxation?
	- by admitting unfeasible state, we can explore other feasible states quickly (that are reachable only as neighbors of that unfeasible state)
***
### Tutorial
- Knapsack operators
	- adding + removing items (both must be there)
- Minimum vertex cover operators
	- adding + removing items (both must be there)
- TSP
	- switching two cities
	- be aware of the configuration variable
		- for permutation it's okay
		- for general ordered list we have to introduce adding/removing cities
			- it can have missing cities, duplicite cities etc.
- always ask:
	- are we complete with this operator?
	- are we continuous with this operator?
- if the operation produces (or can produce) an infeasible state
	- we can deal with it later (e.g. using relaxation), but I have to mention it