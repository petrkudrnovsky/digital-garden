For better notes, visit:
- [[KOP 9. lecture]] - simulated evolution (genetic algorithms)
- [[KOP 10. lecture]] - genetic programming
### Genetic algorithm
- fitness function that evaluates all chromosomes in the population
- repeat: selection - crossover - mutation
- replace the population and evaluate the fitness function again
- selection
	- elitism is involved
	- roulette-wheen selection
	- tournament selection
- crossover
	- one-point, two-point, n-point
	- uniform crossover
- mutation
	- random bit flips
### Genetic programming
- e.g. evolution of Lisp programs
- we are iterating on the syntax tree (terminals in leafs and functions in internal nodes)
- crossover:
	- select two nodes in two trees and exchange the subtrees
- mutation:
	- pick a node:
		- subtree mutation = exchange the current subtree with a random subtree
		- point mutation = exchange just the node with another with the same arity
		- shrink mutation = delete the subtree
		- permutation mutation = permute the subtrees of the selected node
### Evolutionary programming
- alternative to artificial intelligence
- reproduction is done only by mutation
- e.g. for finite state machines
### Evolution strategies
- both the genotype and also the strategy parameters are being evolved
- the evolution of the evolution