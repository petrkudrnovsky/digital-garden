# What is entropy?

> [!NOTE] Quick definition
> Entropy measures the gap between what you observe and what you can actually know about the underlying reality.
> - high entropy = many possible hidden arrangements/microstates
> 	- information is spread out and hard to track
> 	- hard to diagnose problems
> 	- high uncertainty about what is really happening
> - low entropy = few possible hidden arrangements
> 	- I know what is going on
> 	- easy to diagnose and understand
> 	- information is organized and traceable
> 
> In nature, the entropy naturally increases, but in engineering systems, we can use energy and other techniques to artificially decrease entropy and build/invent transparent, predictable and sustainable artifacts.

- entropy is a nature's tendency to spread things out and create disorder
	- it measures the level of disorder, uncertainty and randomness
	- the goal of engineering and design science is to reduce entropy and create order
- entropy is used in various areas and sciences
	- thermodynamics
		- the Second Law of Thermodynamics: the entropy always increases in closed systems
			- a closed system (no energy, matter, information enters/leaves)
				- in this system the entropy cannot decrease (more random dice moves -> bias towards macrostates with more microstates -> more entropy)
					- or gas molecules are in one arrangement (at first) and then they spread out randomly (countless arrangements -> high entropy)
			- the entropy is irreversible (it goes only one way, like the time also goes only one way) and it has tendency to spread out, therefore increasing itself
				- and as the entropy increases, the potential energy for use decreases (which we don't want)
			- heat naturally flows from hot to cold environments
				- going the opposite way ("against nature") requires extra effort (air conditioner, fridge etc.)
	- statistical mechanics
		- macrostate = what you observe
		- microstate = the exact arrangement (the union of all individual state particles that make up the macrostate)
		- example:
			- dice: macrostate is the total sum of dices, the microstate are the individual values
				- sum of 7: (1,6), (2,5), (3,4), (4,3), (5,2), (6,1) (many microstates)
				- sum of 2: (1,1) (only one microstate)
			- gas: macrostate is the temperature and pressure, the microstate is the union of positions and velocities of all molecules
		- takeaway: the more microstates -> the more likely the macrostate occurs (Boltzmann)
			- the more ways something can be arranged internally (more microstates), the higher it's entropy (because the inner state is more uncertain/random, not organized)
				- you can't tell in which specific microstate you are in
			- so the number of microstates is also an uncertainty measure associated with the current macrostate
		- examples:
			- software bug (macrostate), specific line where the error occured (microstate)
				- the more microstates, the harder debug
			- rocket failure (macrostate), faulty component (microstate)
				- the more components connected together, the more likely is the rocket failure
### How to fight high entropy in engineering?
- lucky for us, engineering systems are not closed - they can interact with the outside environment
	- we can add energy/effort to reduce uncertainty in the system
	- 1) energy input
		- fridge uses energy to pump heat from the cold (decreasing entropy)
		- computer uses energy to perform calculation and maintain organized data
		- in general, energy is used to observe, register and organize microstates
	- 2) information input (observing and measuring the state)
		- sensors at individual components
		- collecting data about microstates, making informed decisions based on observations
		- be transparent and do not hide underlying details with excessive aggregation
	- 3) active control
		- reorganizing of messy structures, active replacing faulty components, early fixing software bugs etc.
- examples:
	- design software for testing -> observing and testing microstates (individual functions/components) and reducing uncertainty (bugs are discovered early and are well located)
	- sensors at large circuits allow for measuring, looking for deviations, allow for irregular values tracking etc.
	- transparent financial portfolios, track individual assets, not only the aggregated values
	- make hierachical organized structures to maintain control on each level
- this is useful for controlling the systems (in the [[EDSM - 5. lecture - C1]])
	- do not change settings faster than lag time (delays in the system) - we cannot observe the previous settings' results and cannot make an informed decision
	- stay consistent with measurement strategies through settings changes