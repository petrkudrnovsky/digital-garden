This lecture is about how we can learn useful designs from nature, which has developed it's own strategies and "artefacts" through evolution for millions of years. It can serve as a guide on how to build complex and adaptive systems.
- it has a connection to the high cohesion and low coupling principles

Evolution is described as a iterative and incremental "optimization" or a search process across a fitness landscape
- there is a population of entities, which is evolving generation after generation
- the individuals have heritable features, which get combined to (hopefully) produce better individuals in the next generation
	- these features are reproduced/copied or changed (crossover and mutations)
- of course there is a feature-based selection, which removes the worse-fitted individuals

It's based on the Building block hypothesis, that a nature is developing single, robust and fit building blocks (they are low coupled and independent, so the change in one does not affect the others, possible already optimized)
- variations of building blocks occur spontaneously after an environment change
	- slow environment changes produce individuals of higher fitness, but also higher coupling
	- rapid environment changes produce individuals with lower coupling (which are more robust to environment change)

Biomimetics design guidelines:
- don't try to be perfect
- function fidelity (= věrnost, přesnost) - you have to maintain enough detail in translation of the function from the nature to the artefact, but you don't have to copy everything
- trio: an agent, a task and it's environment
	- it's needed to view and consider all of them at once
### What is an NK model?
- it's a mathematical model developed to capture the complexity of systems in nature and to estimate the overall fitness of the nature's systems
- N = number of elements in the system
- K = number of functional connections between those elements
- if the K = 0, there are no connections, the elements have low coupling and are developing/evolving independently - so a change to any of them won't affect the others (no "ripple effects")
	- the landscape is smooth and there is only one peak, so as the evolution is "hill-climbing" to the global maximum
	- but this is often not the case in complex nature systems
- if the K is getting higher, we get more and more connections between elements and the landscape becomes more random with a lot of local peaks
	- as the evolution is called "myopic", it tries to be better, but only in a limited scope, hill-climbing the nearest local optimum and then the process stagnates
	- the evolution in general does not try to find the global optimum (which is often infeasible to find), but rather the local optimums
- if the K = N-1, the landscape is almost random and the system is so connected/coupled together that almost every change to the better results in negative "ripple effects" to other parts of the system