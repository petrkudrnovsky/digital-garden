### Dijkstra's Algorithm
- uses the path cost (each edge has a cost)
- complete and optimal
- implementation with the counters
	- so, we have a counter at the beginning with the value 0
	- we select the "closest" neighbor and add the cost of the edge to it to the actual value of our counter
	- if there is already a better way to get to the neighbor (so it has a lower counter than our counter + edge cost), drop this edge
# Informed search strategies using heuristics
- the heuristic functions help us to significantly reduce the number of expanded nodes and to find a good-enough solution in reasonable time
	- heuristic functions utilize some special knowledge about the problem domain or the specifications of the search space
	- they estimate the quality of the solution or the potential of the partial solution
	- important: the heuristic does not guarantee an optimal solution
	- examples:
		- Manhattan distance (from current position to the goal) in labyrinths
		- air distance between two points on a map
- difference to uninformed search strategies
	- uninformed search strategies rely solely on the goal function (telling if the state is a goal state or not)
- state space with heuristic
	- is a normal state space (as in the [[ZUM - 2. lecture#What is state space?]])
	- with the addition of `c(a)` (cost function assigning a non-negative cost to each action (edge)) and `h(s)` (heuristic function assigning an estimation of the least cost path from state `s` to the closest goal state)
### Optimistic (addmisible) heuristic
- = if it never overestimates, meaning that the value of the heuristic is never greater than the true cost needed to reach that goal
	- if the heuristic overestimates, the algorithm may miss the best solution because it may seem more expensive than it really is
- $\forall s\in S: h(s)\le h^*(s)$, where $h^*(s)$ is the true path cost to the goal from state $s$
### Consistent (monotone) heuristic
- if we move from s1 to s2 with action a, the heuristic h(s2) + c(a) should be greater than h(s1)
	- h(s1) ≤ h(s2) + c(a) or equivalently h(s1) - h(s2) ≤ c(a)
- alternative meaning: if we move in the direction of the goal, the decrease in heuristic function value cannot be greater than the cost of the action
	- I cannot have h(s1) = 100, then move with c(a) = 10 and then have h(s2) = 80
		- the best I could do is to have h(s2) = 90 (or greater, if we couldn't move in the best direction possible)
### Dominant heuristic
- if we have two algorithms A1 and A2 with heuristic functions h1 and h2, the heuristic h1 dominates h2 when:
	- for all states, the h1(s) is greater or equal to h2(s)
	- alternative meaning: A1 is more "informed" than A2 and will search through fewer nodes than A2
### How to build a heuristic?
- with problem relaxation (simplification)
	- I can simplify the problem by removing some constraints and calculating the heuristic function for that problem
	- the heuristic will automatically be admissible (the cost of the easier version will always be smaller (or equal) to the true cost (because we have removed some constraints))
- combining more admissible heuristics together with `max()` function
	- this combined heuristic is guaranteed to dominate every single heuristic (and match the best one)
		- is "more informed" and will explore fewer states
	- the `max()` is also admissible
- by precomputing the results of smaller problems
	- a smaller problem will for sure have a smaller cost than the full problem (so the heuristic is admissible) and then with solving the original problem, we can use the lookup table for the solved subproblems
- by learning the heuristic from data (e.g. machine learning)
	- but then we do not have a guarantee that it will be admissible (not overestimating)
### A good heuristic
- is optimistic, monotone, well informed, simple to compute
### Greedy search
- always chooses the node with the lowest value of the `h(s)` heuristic function assuming it quickly leads to the goal
- is not complete, is not optimal
	- if there is some issue which is not considered by the heuristic function (e.g. mountain range while using air distance heuristic) it could be quite ineffective
### A\* search
- a combination of greedy search and Dijkstra's algorithm (takes into account both path cost and the heuristic function)
	- it tries to minimize `f(s)=g(s)+h(s)` function
		- `g(s)` = function determining the cost from initial state to the current state
		- `h(s)` = (heuristic) function determining the least cost from the current state to the closest goal state
	- this combination tries to "point" us in the right direction (Dijkstra could expand unnecessary amount of nodes to find the solution)
- is complete and optimal
	- optimal it is if the heuristic `h(s)` is optimistic
