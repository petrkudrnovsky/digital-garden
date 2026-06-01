If we want the AI agent to do some useful work (to find a goal, to maximize some metric etc.), we need to define the problem to it:
- goal formulation
	- a set of states that satisfies the agent 
- problem formulation
	- defining what actions and states are even possible
- environment setting:
	- observable vs. partially observable vs. unobservable
		- depending on what the agent see
	- episodic vs. sequential
		- episodic: each agent "move" is one separate episode (the next action does not influence future actions and it is also not based on the past)
			- e.g. an agent sorting items on the assembly line
		- sequential - a current action influences all future actions, so the agent needs to consider them in advance and take the action, which ends up in the best state (given the problem and goal definition)
			- e.g. an agent playing chess
	- discrete vs. continuous
		- moving around on tiles (discrete) vs. moving around freely (continuous)
		- continuous is more accurate and represents real world better
	- deterministic vs. stochastic
		- stochastic = the moves can have different and random probabilities of being performed
	- static vs. dynamic
		- dynamic = the environment could be dynamic and change itself
			- important: the environment changes are not caused by the agent itself
	- known vs. unknown

For an AI model (within model-based AI), the goal is to define the environment, state space etc., so the AI agent can successfully find the goal from the start.
# Searching algorithms
- if the environment is deterministic, observable, discrete and static, then the solution to any problem could be expressed as a sequence of actions
- the process of looking for a solution is called: search
### What is state space?
- related to: [[KOP - Combinatorial Optimization]]
- it is a tuple of:
	- a directed graph with all possible states and set of edges representing relationships between states
	- a set of initial states
	- a set of goal states (states with certain properties which pass the goal function)
- a state: some configuration of the problem

- different algorithms apply the search strategy to find the goal state (solution)
- all possible action sequences (solutions) form a directed rooted tree with the initial state as root
### Search strategy
- how to measure the search strategy performance?
	- completeness: if the solution exists, does the search strategy guarantee to find it?
		- does it visit each state at least once?
	- optimality: does the strategy always find an optimal solution?
	- time complexity
	- space complexity (how much memory is needed?)
- to reconstruct the path, every node has to remember its predecessor
##### Uninformed search strategies
- no information about the state whatsoever
- only the information if the state is goal or non-goal
- random search, BFS, DFS
##### Random search
- an algorithm randomly chooses the next node to be expanded (visited)
- not complete, not optimal
##### BFS
- implemented using FIFO queue
- complete and optimal
- exponential time and space complexity
##### DFS
- each node is expanded as deep as possible
- implemented using LIFO
- exponential time complexity and linear space complexity
- not complete, not optimal
	- it could get stuck exploring a very deep (infinite) branch and never backtrack to find the solution
	- given the solution exists (so it is in some "finite depth"), DFS could get stuck in some different, infinite branch and eventually never find the solution, whereas BFS will always find this solution (it explores each level completely before advancing to another level)

***
# Tutorial 2
- MAPF = multiagent path finding
	- e.g. in Amazon stores
# Systematic space search
- Dijkstra
	- always expands a node to which the path had the lowest cost
	- heuristic: "how good is the path up to this point?"
- Greedy best first
	- always takes locally best option
	- heuristic: "how good the ongoing path to the end could be?"
		- using heuristic like Manhattan distance to estimate the path that leads to the end
		- or Euclidean distance or other heuristics
- A star
	- is more conservative, but can guarantee the shortest path
	- measures the sum of the "goodness" of the already taken path and the heuristic estimation of the ongoing path