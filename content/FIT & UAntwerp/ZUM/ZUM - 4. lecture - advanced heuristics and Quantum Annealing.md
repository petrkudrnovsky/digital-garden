- highly related to: [[KOP 7. lecture]]
- optimization problems
	- they do not care about the path to the solution, but about the goal itself
	- the goal is to find the best state according to some objective (fitness) function
		- we have maximization and minimization problems
- local search algorithm:
	- starts with some random state
		- iterates through the search space and evaluates the objective function
			- if the new state has better value of the objective function, save it as best solution
	- finish if the found best state is good enough or the algorithm runs too long
### Brute force optimization algorithm
- only for small problems, systematically evaluates the objective function for all states and returns the global optimum
### Random optimization algorithm
- every iteration, it randomly samples a new "neighbor" and moves there if the neighbor has better objective function
### Hill-climbing optimization algorithm
- randomly samples from the current neighborhood and moves to a better solution
	- first-choice hill-climbing - generates candidates randomly until a candidate with better cost is found
	- stochastic hill-climbing - generate several candidates and from those that are better than current solution, randomly choose one and move there
- this way it always moves "up the hill" and terminates at the local optimum
### Steepest Ascent Hill-climbing optimization algorithm
- works in the same way as the normal hill-climbing, but it evaluates all available neighbors first and then it decides to move into the one, which has the best cost (minimizes/maximizes the objective function)
- it converges faster than random hill-climbing

- [[Machine learning - různá témata (FIT)#Prokletí dimenzionality]]
	- the curse of dimensionality - with adding new features, the number of states grows exponentially and sampling of the neighbors could get too difficult
		- there is a trade-off in the objective functions and what size of the neighborhood should we pick?
			- if too small: we can get stuck in local optimum easily
			- if too big: we can miss the optimum
				- because evaluating all neighbors is computationally not feasible, so I resort to random sampling and with a lot of neighbors, the probability of choosing a good neighbor is low
		- with adding features, the number of possible states grow exponentially, whereas the "interesting" region becomes a needle in a haystack (it does not grow exponentially)
### Simulated Annealing
- [[KOP 8. lecture#Simulated Annealing]]
### Tabu Search
- [[KOP 11. lecture#Tabu Search Algorithm]]
***
### Quantum Annealing
- = finding the minimum energy state of something
	- we have some energy landscape and by defining the problem, we define the "relationships" that the different qubit configurations should have (by altering the magnetic fields to "favor" concrete configurations) and as the quibits interact with each other, they influence each other and then they create an energy landscape, where we could find the lowest point = our desired minimum
		- relationships are made with couplings (what qubits should be connected and how) and biases (what combinations are energetically preffered)
		- the more qubits I have, the more states I can define and the more fine-grained energetic landscape I can define
- good for two things:
	- optimization problems (encoding to QUBO)
	- probabilistic sampling (sampling a lot of points to the landscape to get an idea of how it looks like)
- simple explanation:
	- it works like Simulated Annealing, but instead of trying to escape the local optimum be accepting worse moves, it "tunnels" right through the hills of the optimization landscape trying to find better local optimums
	- this approach is more beneficial, if the hills are tall and thin (QA cares about the width of the hills and not the height (as opposed to SA))
	- the algorithm starts in a simple quantum state (a superposition of all possible solutions) and then slowly reduce the quantum fluctuations
		- what are quantum fluctuations?
			- in real world, the particles stay at one place unless we give them energy to move somewhere else (like energy in SA to climb out)
			- in quantum mechanics, we don't know, where the particle is, until we measure it (it has some probability of being at some place over all possible positions), it is inherently fuzzy and may or may not exist on the other side of the hill with some probability (that is the tunneling) - this is described by a probability wave
				- we start with a strong fuzziness (so the probability wave "is strong" and there are high probabilities of finding the particles on the other side of hills) and we continuously reduce the level of fuzziness to sharpen and settle the system
		- it is proven, that if we reduce the quantum fluctuations slowly enough, the system evolves in the direction of the state with the lowest energy (which is/could be the optimum solution)
			- if I do it too quickly, it may happen that the system won't even reach the low state and settle there (it gets "frozen" is some local optimum)
		- this is grounded in the adiabatic theorem of quantum mechanics
- this cannot be done on normal computers, but on a specialized quantum hardware (most useful are so called D-Wave systems, which are designed especially for this purpose)
##### QUBO
- Quadratic Unconstrained Binary Optimization - this is the way how to map the problem onto the physical qubits
	- it plays the similar role as SAT in logic (I can reduce many combinatorial problems to it and solve them with it)
	- knapsack, TSP etc. have QUBO encodings
- the process includes creating binary variables (0/1) which map onto qubits (with superposition between 0 and 1), putting them into a function (where the variables could be multiplied by a constant or in pairs (one with the other))
	- this function is then being minimized by the Quantum Annealing
- if there are some constraints, they are included as penalty terms (adding a big penalty whenever the constraint is violated), so the algorithm naturally avoids these solutions
	- QUBO is "unconstrained" by definition, so the constraints need to be added this way
	- there is a slight problem: the penalty terms need to be tuned experimentally (too small -> algorithm may ignore them, too big -> they dominate and they disort the landscape of the original problem)
		- and this adds overhead
