# Tabu Search Algorithm
- it's an algorithm based on [[KOP 7. lecture#Best only|Best only local method]], Tabu Search is a local method as well, but it uses a special tabu list to store "non-local" information that helps the algorithms explore the solution space in a way that it can avoid getting stuck in local optimum
	- Best only greedily looks for locally best move and finishes, when there are no better moves (locally)
- difference from SA and GA
	- SA and GA: memory-less and principally based on randomness
		- their neighborhood is constant
	- TS: keeps dynamic and asymmetric memory of states/operations
		- randomness is suppressed
### Memory
- Tabu Search is "learning" through it's memory, it's adaptive and dynamic
- what to remember?
	- features influencing the solution quality (e.g. 1 on first position has 7/8 satisfied clauses and 0 on first position has 2/8 satisfied clauses, it's probably better to have 1 there - this could be memorized)
- short-term memory = basically a [[#Tabu list]]
- long-term memory
	- stores attributes (which are common in good/bad/all solutions)
	- stores [[#Aspiration criteria]]
	- stores information on good/bad solutions (intensification)
	- stores space exploration statistics (useful for aspiration criteria and for diversification)
- both memory types are used for intensification and diversification
### Tabu list
- recent moves are stored in Tabu list to prevent retracing (and cycling back), repetition and getting stuck in local optima -> it supports diversification (wider state space is explored)
- = short-term memory of the algorithm
- while Tabu Search is looking for locally best move, it is prohibited to use moves/states in the Tabu list
	- a straightforward implementation:
		- the *inverse moves* are stored (to prevent the Search to go back)
		- for a move $m$, a move $m^{-1}$ is stored
		- a problem: sometimes there is no direct $m^{-1}$ move, but a detour has to be done to get back to the original state -> all moves have to be stored, that's time consuming
	- attributes implementation (to address problems in the previous implementation)
		- operations have attributes, one operation can have multiple attributes and each attribute can have exactly one Tabu status
			- so one operation can have more Tabu statues (depending on the number of attributes)
		- it's simpler to store attributes of the states/solutions (not all operations/states)
		- when Tabu Search is looking for a new locally best state, it looks at their attributes and if their attribute is in the Tabu list, it cannot select it
		- an attribute could be anything (cost changes, configuration variable changes etc.)
			- adding/removing item, change the cost to X, cost/weight ratio on Y etc.
		- attributes then have Tabu status (= is in Tabu list or not)
- *tabu tenure* = a time for which the tabu state cannot be touched
	- could be:
		- static - an operation/attribute is in Tabu list for a constant number of iterations (the number of iterations could be dependent on instance size)
		- dynamic - it changes in progress of the Search
- implementation (has to be space-efficient):
	- instead of remembering whole configurations (which is possible, but it takes up a lot of time)
		- it's better to remeber only the attributes and their values
			- if there are multiple states with the same attribute value, then we don't care which one of them will be moved/removed/changed
	- Tabu list - the list itself
	- Tabu state - one element in the Tabu list
		- could be whole configuration snapshot, structure with attributes, inverse operations etc.
	- a operation in Tabu is permitted or penalized
##### Size of the Tabu list
- small tabu-list => intensification (exploitation)
	- if too small, the Search can cycle and get stuck in local optima
- long tabu-list => diversification (exploration)
	- if too big, too big restrictions, too big explorations of unwanted states (memory and time consuming)
### Aspiration criteria
- they cancel tabus
- = by recording some information / statistics etc., it decides if to "violate" the tabu rules
	- these criteria are based on the long-term memory
	- tabus are sometimes too powerful (restricting all good moves, search leading to overall stagnation)
- aspiration criteria could also be more general rules
	- e.g. "if all operations are tabu a no moves are possible, perform a move with the least number of Tabu statuses on it's attributes / or a move that is the longest in the Tabu list"
- why are they good?
	- they can allow potentially attractive moves (which won't cause cycling)
	- for cases, when almost all operations are on tabu and nothing could be performed
- we also maintain the long-term memory (to remember aspiration criteria / rules)
	- for this reason, we save metadata/statistics along with the attributes (frequency, duality, influence on the solution, average yield of an operation/attribute etc.)
	- these statistics are then updated after each move and they serve for dynamic changing of aspiration criteria for next moves
### Stopping condition
- when all operations are tabu and no aspiration is possible
- after a given number of iterations where the cost didn't improve
- after a fixed number of iterations
### Final algorithm
![[Pasted image 20260104114311.png|400]]

> [!NOTE] My view
> After selecting the best-only move, we add it into Tabu list, so it cannot be touched for some time (for the tenure time). This was a move, that made our solution better (it's a best-only move) and it's now forbidden, so the Search naturally explores solutions around this move.
> 
> After the tabu tenure passes and the move will be "released" to continue with the search, it's not the best move anymore (time has passed, there were new best-only moves, definitely better than the old one).
> 
> But now this "older and not-so-good" move could be relevant for escaping the local optimum. In the past, this move was the move that made our solutions better, then we didn't touch it for some time and found better moves around it (but they unfortunately led us to local optima). Those moves are now in the Tabu list and our "old and not-so-good" move now provides a good way to escape the local optima.
> - because now, the Search will select other good neighbors of this move leading somewhere else (the moves which led us to the local optimum are now forbidden)
# What is strategic oscillation?
- we want to use tabu lists and aspiration criteria to "oscillate" around the feasibility border
	- because good solutions are often near the feasibility border
- tabu lists help us to oscillate and not retracing back and cycle
# What is path relinking
- technique for better intensification
- the algorithm finds a set of high-quality solutions, inspects the paths/moves/operations between them and remembers the attributes of those moves
	- these attributes are probably good and lead to good solutions, so we favor them in the intensification phase