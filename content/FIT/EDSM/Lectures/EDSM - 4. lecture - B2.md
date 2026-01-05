- hierarchy is the way to limit complexity of the system, to restrain it
- every complex system has hierarchy (which is recursive)
	- could be modular as well
- with every "level" of the hierarchy, we have:
	- black-box view
		- interactions between the system and it's environment
	- white-box view
		- interactions between the components of the system
- this way, we can study only parts of the system (white-box view of that respective part) and other parts have as black-boxes
# Hierarchical architectures
- these are a good design, but they don't solve everything
- there are different approaches
	- top-down, bottom-up and meet-in-the-middle
- engineering is basically doing low coupling and high cohesion in modular design in practice
### Low coupling
- [[GRASP - Nízká provázanost]]
### High cohesion
- [[GRASP - Vysoká soudržnost]]
### Integration is tricky
- if we want to integrate more and more utilities and functions often create side-effects
	- more complexity, changes, redesign from scratch etc.
- sometimes, we have:
	- incomplete or unambiguous specification of the problem
	- knowledge from multiple domains has to be incorporated
	- no deterministic path to solution
- these problems create design problems with side-effects (engineering is about mitigating them)
### Evolvability and scalability are also very tricky
- just adding new engine to a rocket is not enough (a lot of related things have to be changed)
- I cannot just add a new module to a CPU (it infuences a lot of things, the instruction set etc.)
- Airbus 380 is not just resizing Airbus 340 etc.
### What we need to engineer something that is sustainable?
- so it could be evolved, maintained and upgraded easily?
	- modular design, strong focus on hierarchical methodologies (low coupling, high cohesion) and their combination (see [[EDSM - NST (all lectures)]])
	- related domain knowledge, knowledge of physics and business background