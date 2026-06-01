
> [!summary] What is an agent? (compilation from more definitions)
> An intelligent agent is an entity, which perceives its environment via sensors and acts rationally and autonomously upon that environment with its effectors. By doing so, it interprets perceptions, solves problems, draws inferences and determines actions. 
> 
> There are agents:
> - reactive (responding to changes in the environment) vs. proactive (they behave in an opportunistic and goal-directed way) vs. social (communicating with humans or other agents)
> - learning (changing behavior based on prior experience)
> - mobile (could move to another place)
### Multi-agent system
- a collection of semi-autonomous sub-agents in a shared environment, where each agent:
	- perceives the environment
	- acts flexibly to reach its objectives
	- interacts with other agents (cooperates or competes)
### Agent program
- is a concrete implementation of the "agent function", which is an abstract mathematical description of the behavior of the agent:
	- $f: P^*\rightarrow A$  (mapping of sequence of perceptions to some action)
### Types of agents (based on complexity)
- presentation: https://courses.fit.cvut.cz/BI-ZUM/media/en/lectures/BI-ZUM_lecture-agents.pdf
- simple reflex agent
	- select action based on the current percept (not considering history of percepts)
	- guarantee to work well only in fully observable environments, controlled by if-then rules
- model-based reflex agent
	- it maintains an internal model, that summarizes the percept history:
		- 1) how did the world evolve independently of the agent (how to world evolves?)
		- 2) how did the agent's actions affect the world (what do my actions do?)
	- it could operate in partially observable environments
	- is still driven by condition-action rules (like the simple reflex agent)
- goal-based agent
	- includes a description of a desirable state (goal) and uses search and planning to find the sequence of actions to reach the desirable state (goal)
	- with planning, it knows in which state the agent will be if it performs action A
- utility-based agent
	- uses "utility function" (mapping of states to real numbers, each number indicates how good that state is (is it useful?))
		- the agent then acts rationally, if it selects actions which lead to states with a high utility levels
	- this is better than binary goals (reached/not reached) as it gives a continuous metric of success
		- a goal-based agent cannot decide between two plans how to get to the goal
	- it will know in which state it will be and also how "happy" it would be in that state after performing action A
### How does the rationality in agents work?
- in reality, the agent may not know the true outcomes of his actions and instead it only has a distribution of all the outcomes (so called, the "lottery")
	- those are all possible outcomes with assigned probabilities (those have to add up to 1)
- then, each agent has a utility function, which assigns the utility "level" to each outcome
	- we have an expected utility: $EU(action) = \Sigma\ P(outcome\ |\ action) \cdot U(outcome)$ 
- there are two modes:
	- self-interested rational agent, which selects the action, which maximizes its individual expected utility (so finding the lottery with maximum utility (each possible lottery has utility (multiplied by the probabilities of the outcomes)))
	- cooperative rational agent, which selects the action, which maximizes the collective utility of all agents in the cooperative network
		- there are different ways to combine the utilities of all agents in the network
***
# Game Theory
- game theory is a mathematical study of interactions between rational, self-interested agents
	- it includes formal decriptions, analyzing and choosing the optimal strategy etc.
- game categories:
	- cooperative games - modelling unit is a coalition of agents (they may have different interests, they form a coalition, if they can gain by binding together)
		- if there are binding agreements, they are enforced (so if a coalition commits to do some action, every agent has to follow through) - like a contract
		- the game theory studies the way how the coalitions forms and how are the payoffs split onto individual agents
	- non-cooperative games - a modelling unit is an individual agent with its own interests and individual actions
		- there are no binding agreements between agents that the agent must take into account (it may decide freely to cooperate, compete etc.)
		- the agent decides based on what others are doing
### Games in the normal form
- a finite game in a normal form for $n$ players is a triplet of:
	- a set of players
	- a set of actions sets for each player
		- action = a single move available to one player
		- action set $A_i$ = all actions available to player $i$
		- action profile = a tuple $(a_1, a_2, ... , a_n)$, one action per player
			- there are combinations of all actions of all players together (one action set determines one step in the normal-form game)
	- utility function for each player
		- $u_i(a)$ means the utility of player $N_i$ with action $a$ 
- e.g. rock, paper, scissors game; choosing sides; matching pennies
- how to choose the best action profile?
	- using Pareto optimality
		- "an action profile is Pareto optimal if there is no other profile that makes at least one player strictly better of without making any player worse off"
		- it is a multi-criteria optimization
		- action profile "a" Pareto-dominates another action profile "b" iff
			- for add players the utility of the action profile "a" is higher or equal to the action profile "b"
			- there exists at least one player, whose action profile's utility is strictly better than the utility of the action profile "b"
		- a game usually has multiple Pareto-optimal action profiles and choosing among them requires additional criteria (e.g. Nash equlibrium)
		- Pareto-optimality does not mean that the action profile is fair or good, it just means that there is no "free lunch" improvement anywhere that would increase the utility without worsening any of the players
	- using Nash equilibrium
		- it is a stable state
		- the Nash equilibrium is an action profile, where all players select their best responses (= no player wants to change the action profile (by changing its action) because it would decrease it's utility)
		- best response theory:
			- given what everyone else is doing, my action will maximize my utility
### Games in the extensive form
- games in the form of a game tree
- it is a tuple of:
	- a set of players
	- a set of actions for each player
	- a set of decision nodes
	- a function which assigns a set of possible actions for each decision node
	- a function that assigns to each non-terminal node a player whose turn it is
	- a set of terminal nodes (a node cannot be terminal and decision at once)
	- a successor function:
		- takes a decision node and an available action (in that node) and outputs another node (decision or terminal)
	- a set of utilities for each terminal node for each player (some terminal nodes are better for player X (e.g. player X wins there) and some are better for other players)
- examples: 
	- two player zero-sum game
		- e.g. chess, tic-tac-toe
		- finishes with $N_1$ winning, $N_2$ winning or a draw, nothing else
		- = one player's gain is exactly another's loss
- optimal move is when a player selects an optimal action and its position is not worse
	- e.g. the player can win and makes a move on the path to the win
	- the player has selected the move that maximizes the utility
	- play optimally is very difficult, it is a combinatorial problem (it is not feasible to explore the whole game tree, so heuristics are often involved)
### Minimax algorithm
- selects the optimal action (while assuming that the opponent play optimally)
	- considers two players:
		- MAX (wants to maximize the utility)
		- MIN (acts as an opponent and wants to minimize the utility)
	- minimax algorithms assumes the two-player zero-sum game with perfect information (all information is available (e.g. chess, not poker))
- instead of two players having their own utilities, there is only one utility for the game and player 1 tries to maximize it and player 2 tries to minimize it (and therefore they are effectively playing against each other)
- from the current decision node, generate a complete game tree (or to the depth equal to $d$ (optimization to reduce the combinatorial explosion)) by depth first post-order (parent is calculated after all children are evaluated) traversing and split the nodes on each tree level to:
	- max nodes (turns of the MAX player)
		- evaluates to the maximum evaluation of its direct children
	- min nodes (turns of the MIN player)
		- evaluates to the minimum evaluation of its direct children
	- terminal nodes - do not have any children, represent the end of the game
	- if there is a $d$ cutoff, a heuristic function is used to estimate the values (evaluations) of the decision nodes at the cutoff (since they are not terminal nodes)
- there is an evaluation function, which evaluates the node (its utility)
	- it nees to be fast and accurate (the speed of minimax depends on this function)
- alfa-beta pruning (for optimizing the minimax algo):
	- for each expanded node, two values (alfa, beta) are kept 
		- $\alpha$ - the highest value that MAX player can guarantee so far along the path to the current node
			- = the highest utility that player MIN cannot reduce if MAX plays optimally
		- $\beta$ - the lowest value that MIN player can guarantee so far along the path to the current node
			- = the lowest utility that MAX player cannot increase if player MIN plays optimally
	- the pruning rule:
		- at a MIN node: if a child's value ≤ α, stop exploring further children (MAX has a value of at least α secured elsewhere, so MAX would never let the game reach this MIN node)
		- at a MAX node: if a child's value ≥ β, stop exploring further children (MIN has a value of at most β secured elsewhere, so MIN would never let the game reach this MAX node)
- complete example at the end of https://courses.fit.cvut.cz/BI-ZUM/media/en/lectures/10-games-anim.pdf