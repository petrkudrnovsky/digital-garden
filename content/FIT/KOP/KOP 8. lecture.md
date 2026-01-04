# Simulated Annealing
### Why do we need advanced heuristics?
- this solves the problem of local greedy methods getting stuck in local optimums
	- since they are always selecting the local best neighbor (greedy approach)
	- they have "small iterative power" (meaning from different starting points, they are unable to "iterate" to the global optimum and end up somewhere else)
		- they just don't have the "power" with the iterations to get closer and closer to the global optimum
- we need to sometimes do the temporarily worse move with hope, that we will escape the the local optimum and find the global one
### Basics of SA
- annealing = from metallurgy
	- it is a physical process of cooling the hot metal down (it depends on the velocity of the cooling)
- Simulated annealing is a fusion of Random walk and First improvement ([[KOP 7. lecture#Random walk]] and [[KOP 7. lecture#First improvement]])
	- their combination is a threshold algorithm
		- always accept the move that improves the solution
		- sometimes accept a move that makes the move worse
			- we allow moves that are worse less than some defined acceptance threshold (= it's defined by a probability)
		- in other words: the search for new neighbors is randomized (algorithm makes random moves to get new neighbors), the move is accepted, if the cost is improved or if the cost is worse by $T$ threshold at most
### Pseudocode of SA
```c
simulated_annealing {
	t = t_0 // start with initial temperature
	state = initial_state // start with a random valid state
	while (t < frozen()) {
		while (not(inner_loop_end())) {
			new = random_neighbour(state) // random choice of neighbour
			delta_C = cost(new) - cost(state) // difference in the solution cost
			if (delta_C < 0): state = new // maximization problems: (delta_C > 0)
			else if (accept(delta_C, t)): state = new
		}
		t = cool(t)
	}
}
```
### Parameters of SA
- initial state
	- SA should not depend on the initial state, it should be random
	- could be constructed by a local constructive method and start from there 
		- for a Knapsack problem it could be a simple cost/weight sorting heuristic 
		- this will give me a valid local optimum (and the SA's task is to escape it and find the global optimum)
	- does not have to be valid
		- there could be mechanisms for it 
			- SA restarts - multiple restarting at different states in order to reach to optimum
			- relaxation - [[KOP 7. lecture#Relaxation]]
- how to decide if the state will be accepted?
	- the improvement is accepted always
	- the worse move:
		- the higher the cost difference, the lower the acceptance probability
		- probability to accept: ${random()} < e^{-\frac{\Delta C}{t}}$ 
		- and vice versa
	- limit cases
		- moves that don't change the cost ($\Delta C = 0$) are also taken immediately
		- if the temperature is 0 or the cost difference is too big (going to infinity) -> the probability is 0 %
- initial temperature
	- changes of the temperature
		- it is set to some initial value and then we're decreasing the temperature (the cooling/annealing phase)
		- two loops
			- outer loop, decreases the temperature until it is "frozen"
			- inner loop, works with the unchanged current temperature (the temperature is used in the probabilistic function for accepting the "worse" states)
	- when is high = big exploration/diversification, the acceptance probability is higher, we can be losing time with random search that is not needed at high temperatures
		- this phase avoids getting stuck in local optimum
	- when is low = small exploration/intensification/exploitation, small iterative power
		- the acceptance probability is lower, convergence to the solution
		- tends to be stuck in the local optimum
	- if $t=0$, SA becomes a First-Improvement approach (not accepting any worse moves)
	- algorithms for setting the initial temperature
		- run SA "backwards" from $t=0$ and increase it quickly and observe, how does $\Delta C$ changes (it is instance specific)
			- it is able to reflect the "nature" of the current instance
			- from the beginning the $t=0$ means that only improvements are accepted, as $t$ is gradually increasing, the number of acceptances of "worse" states is also increasing 
				- when the ratio of accepting worse states reaches 50% (so 50% of worse states are accepted) - we stop the algorithm and record the temperature
			- why 50%?
				- because at the beginning of the "normal" SA, I want enough exploration (50% of the worse states are accepted at the beginning)
					- too much would be unneccesary - random walk
					- too low would result in small exploration and risk of being stuck in local optimum
- frozen() function
	- determines, when does the outer loop end
	- possibilities:
		- constant number of iterations (end after X outer iterations)
		- constant final temperature (end when reaching X temperature)
		- when I observe that nothing happens (no changes to best solution after X iterations)
- inner loop
	- it works with a constant temperature (it is changed in outer loop)
	- "how long it should run with constant temperature" - hard question
		- most common way is scaling it with instance size (linear scaling)
		- alternatives: constant number of iterations, changes with current temperature
- the $k$-neighborhood is typically with $k=1$ 
- cooling functions
	- linear cooling - not so good, the cooling is too fast
	- geometric cooling - most popular
		- constant alpha is called "the cooling factor", it is less than 1, but is approaching 1
		- usual values: 0.8 - 0.99
	- exponential cooling - is even slower
	- logarithmic cooling - is extreme, very slow cooling
		- it is theoretically proven that this way will find a global optimum after infinite steps 
		- we have a local search algorithm that is able to produce global optimum
		- the problem is the $c$ variable (the depth of the local optimum), it is difficult to determine (we would have to solve it by bruteforce, but then SA would not make sense ;)
			- we have to know, how deep the local optimums are
### Different modes of SA
- exploration
	- we are trying to explore the state space, we are not afraid of worse solutions
	- temperature is high = basically random movements are accepted
- exploitation
	- after some exploration, I have found some part of the state space that looks promising and I will try to converge to some local optimum
### Useful enhancements
- remember best obtained solutions
	- for scenarios when I find a good local optimum or even global optimum and escape it and won't find it again
- restarts are good, but in practice, it's better to find good-tuned parameters