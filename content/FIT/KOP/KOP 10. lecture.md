# Genetic programming
### Schemata
- schema can show what two configurations have in common
- one schema can represent more individuals
- it's a string over alphabet {0, 1, \*}
- order of schema = number of fixed values (the rest are metasymbols \*)
- lenght of schema = the length from the first fixed value to the last fixed value
- if all solutions have common parts
	- it's a good idea to keep them
	- it's a part of the state space (we are narrowing it by fixing some values)
### Schema Theorem
- it basically says that schemata with above-average fitness and high survival probability grow in the population, whereas schemata with below-average fitness and lower survival probability shrink
	- because, if it has lower probability of survival, the right side of the equation is smaller, so the $m(S,t+1)$ could be smaller as well
- this Theorem is the foundation of the **Building Block Hypothesis**
	- GA works by finding and combining good schemas ("building blocks") that lead to optimal solutions
	- short (low schema length), low-order and highly fit schemata survive better, are recombined together to form sustainable and good-fit longer schemata, which then form optimal solutions
		- the order of "good" schemata gradually increase, until complete solutions (with no wildcards) are presented
		- at the beginning I have a lot of small schematas (they are surviving easily)
		- at the end there is either a block with all values specified (a "full schema") or several blocks with almost all values specified (near-optimal solutions)
			- these schematas were able to survive and are the most fittest
##### Schema disruption problems
- if the schema is short (it's length), it will more likely survive crossover and mutation
	- the longer it is, the more possibilities for cutting it by e.g. one-point crossover are there
		- I have a schema (a good building block) and it could destroyed by one-point crossover (each part goes to different child)
	- **Linkage problem**
		- = the chance of survival does not depend only on the schema order, but on it's length
			- schema having order = 4 and length = 4 will survive more likely than schema having order = 4 and length = 6
		- the 2-point crossover is less disruptive than 1-point crossover
			- mathematically, there is a lower chance of schema disruption (one cut in the schema and one cut outside of schema OR both cuts in the schema)
			- this works mainly for compact schemas (which are the best performing in GAs)
		- but more-point crossover or uniform crossover are more disruptive than 1-point crossover
		- the solutions?
			- reordering and grouping common genes together
			- working with building block explicitly -> [[#Fast Messy GA]]
			- probability models of relations between values of variables -> [[#Bayesian Optimization Algorithm (BOA)]]
- if the schema has a high order
	- more (fixed) positions can be mutated and the chance of schema disruption is higher
- if the fittness of the schema is high
	- the schema will more likely survive crossover and mutation
### Fast Messy GA
- one of the approaches to GA
- the position information is not used in the algorithm (just for decoding)
	- the only useful information is the value of the gene itself
	- gene is a pair (position, value)
		- FMGA uses only the value, position is only for decoding
	- same in the Knapsack (I don't care which items are first or last)
- this specification can lead to underspecified and overspecified individuals
	- underspecified - not having all the genes
		- ((0, 1) (2, 0)) -> where is gene on position 1?
		- solution: reference individual (below) is used (only the value on position 1 is "borrowed")
	- overspecified - having multiple values on one position
		- ((0, 1) (2, 0) (1, 1) (2, 1)) -> two values on position 2
		- solution: first occurence is used
- reference individual
	- there is only one for the whole generation
	- it is always fully specified (no asterisks), so the fitness could be always calculated
	- is gradually better and better, it is the best individual constructed from the population
	- used to fill the blanks in underspecified individuals
- how does it work?
	- the principle is similar to generic GA
	- at first, random strings are generated (mainly small, low-order and short) representing promising schemata
	- then selection and thresholding of strings to obtain a given order
		- using relative fitness
		- thresholding/shortening: removing genes, which do not contribute to fitness (to maintain short and compact building blocks and eliminate bloat)
	- crossover operation (cut & splice)
		- both parents are cutted at one point at random
		- the 4 parts are then combined (so 12 new children are created (4\*3 options))
		- children can have variable lenghts
	- gradually building high-order, fitter blocks from short schemata and this way, we obtain good building blocks
- why is it better than standard GA?
	- more resistence to schema disruption (the position information travels with the gene value) and genes that work together can stay together
	- the children can be of variable lenghts (more flexibility, more complexity)
	- FMGA identifies the good building blocks better
- why is it worse than standard GA?
	- it's really complex 
		- computational complexity
		- complex operator implementation
		- a lot of parameters, difficult tuning
- today, the standard GA with some improvements is used more often
- FMGA is rarely used, sometimes in research, but the practical applicability is not big
### Bayesian Optimization Algorithm (BOA)
- instead of explicitly representing individuals, a probabilistic model, which describes the population is created
- the idea
	- similar to standard GA
		- GA tries to get better solutions by improving the individuals directly
	- in BOA we have model representing the population (it's properties) of promising solutions
		- the model is being refined (improved) to better capture what makes a solution good
		- and the model samples/generates better and better individuals
		- operations of crossover and mutation are replaced with probabilistic modelling
##### Principle
```txt
BOA:
Generation t:
Population → Selection → Build Model → Sample from Model → New Population
             (keep good)  (learn structure) (generate)
```
- the selection filters out the good/fit individuals and then a probabilistic model is created to capture the nature, properties and relationship between the fit individuals
	- e.g. if on position 1 is 1, then on position 2 is 0 (this property is in 90 % of fit individuals - it is probably a good property)
	- it tries to "explain" the observations in the individuals
- before sampling from the model, the created model is optimized in a separate loop
	- adding/removing/reverting edges and other operators are present
	- the cost metric is "how precise is the model when describing the current population?"
	- could be optimized using greedy algorithms, Simulated Annealing etc.
##### Bayesian network $B$
- in general
	- it represents the structure of the problem 
		- 1) it describes the data
		- 2) is used to generate new data of similar properties
	- are represented in DAG (directed acyclic graph)
- in BOA
	- it represents probabilistic dependencies between two genes
	- is able to describe the statistical properties of selected individuals and generate new individuals with similar or better properties
##### Application
- the population is described using a Bayesian network, which is a DAG describing the selected population properties, dependencies and interactions
	- conditional probability is quantify these relationships
### Genetic programming (GP)
- the idea of genetic programming was originally to develop/evolve a computer program doing user-defined tasks (programmers won't be needed "in the future")
- now it is used to develop/evolve digital and analog circuits, antennas, math formulas etc.
	- the results show, that it is able to develop weird, extraordinary/unconventional/creative solutions which perform better than designs from humans
- how does it work?
	- the input is a high-level program/circuit/antenna description
		- terminals, non-terminals (functions), constraints, fitness function, termination function
	- the output is a finished computer program/circuit/antenna
	- programs are internally represented using trees
		- internal nodes = functions/operators
		- leaf nodes = terminals (variables, constants)
	- operations:
		- crossover
			- randomly select parent nodes and exchange them (along with their subtrees)
			- the result is a syntactically valid executable program (proceeds in the next generation)
		- mutation
			- randomly change: function, terminal
			- replace subtrees with terminals, delete subtrees...
		- reproduction
			- probabilistically select an individual based on fitness and copy it into new generation (basically elitism)
		- architecture-altering operations
			- operations changing subroutines of complicated programs (add/delele iterations, loops, recursions, memory etc.)
### Cartesian Genetic Programming (CGP)
- this approach is used to evolve logic circuits
	- using these approaches, we were able to design very effective multipliers
- the network is represented in a grid (that's why it's Cartesian)
	- a grid of gates (their functions (AND, XOR etc.)) and their inputs/outputs could be represented
	- we evolve the connections and functions of gates
	- it is encoded in a linear string (type of gate, input node, output node)
		- if the node is not connected to output, it is "inactive"
- there is no crossover, just mutations
	- just modifying the "chromosome"
		- 1) the gate will become connected (or disconnected) 
			- changing input/output nodes
		- 2) the gate can change itself (e.g. AND -> OR)
	- neutral mutation = change of the node/gate that is not connected to output 
		- = exploration without the change of the fitness
- reproduction is done by $1+\lambda$ population (for 1 parent, there are $\lambda$ children)
- minimize the logical circuit
	- the number of gates in the grid is the same, but we want to maximize the number of gates that are not connected
		- so we want to achieve the desired outputs using the least gates possible
		- = minimize the number of activery used gates
	- we can choose to minimize the size or depth of the circuit
### Parallel GA
- in general: separation of the whole population to multiple CPU's to achieve higher paralellism
- strategies:
	- island models
		- the population is split into several islands (each island having a subset of individuals)
		- islands are evolved and processed separetely
		- occasionally a migration of fittest individuals between islands happens
			- prevents premature convergence of the islands (degeneration) by bringin new genetic material
		- it uses advantage of a big diversity between islands (each is evolving by itself)
	- cellular GA
		- each CPU owns only 1 individuals
		- thousands of CPUs are organized in a 2D grid
			- and each individual (on it's CPU) can communicate only with it's neighbors
			- so crossover happens only with neighbors
				- the child replaces the old individual on the particular CPU
		- the individuals are isolated the same way as in the islands approach
			- but the isolation is by distance
- the fitness could also be calculated in parallel