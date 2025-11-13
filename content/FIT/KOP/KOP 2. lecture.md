- tractable (= poddajný, snadno ovladatelný, řešitelný) problems can be solved with polynomial time
- intractable problems 
	- we have good non-polynomial and bad non-polynomial problems
		- NP - not that bad problems
			- we can verify a solution in polynomial time, we know how to solve it (but in a polynomial time on non-deterministic TM)
### Turing machine
- = an abstract computational device, which is controlled by a FSM (finite state machine)
	- serves as a theoretical model to compute the complexity of an algorithm (how much time and space is needed to solve a problem)
		- runtime = number of TM execution steps
		- space = how much tape is needed (number of used/occupied tape cells)
- universal turing machine
	- finite state automat is written on the tape (it reads the "instructions" for the FSM and then acts by it)
	- it is basically a programmable Turing machine
- TM solves a problem if it **halts** in some final state
##### Deterministic TM
- TM that for a combination of input symbol (the symbol being read) and current state, there exists only one transition rule
	- so for a given input and starting position, the DTM follows one and only path with no ambiguity
	- important:
		- but for different inputs the path could be different 
		- and different DTMs can solve one problem in a different ways
### P-Class
- decision problems solved in polynomial time by a deterministic Turing machine
	- the problem is recognised by a DTM in polynomial time
- for each problem in P-Class there exists at least one DTM that solves the problem in polynomial time (meaning in $O(n^k)$ where $k$ is a finite number) for ALL inputs
- proving P-Class
	- if I am able to write a program for a normal computer that calculates it in polynomial time
### Non-deterministic TM
- it's able to split itself
- there are multiple transitions from the pair one symbol X one state
- if he has a decision, he will split and solve both directions in parallel at the same time
	- if any of those branches finds a solution, the NTM accepts
	- there is an unlimited number of parallel branches
- theorem:
	- if a NTM solves a problem in $T(n)$ time, the DTM solves it in $2^{O(T(n))}$ time 
### NP-Class
- problems solved in polynomial time by NON-DETERMINISTIC TM
	- another definition:
		- NP-Class problems are problems, which could be verified by a DTM in polynomial time
		- problem belongs to NP, if there exists a non-deterministic TM which solves all yes-instances in polynomial time
- **NP = non-deterministic polynomial**
- certificate is a prove that a solution exists
	- certificate = values of configuration variables satisfying the constraints
	- if we can verify the solution (= verifying if it satisfies all the constraints) in polynomial time -> the problem is NP (because in non-deterministic TM, one branch of the algorithm is able to solve the problem in polynomial time)
	- we know the solution (from an oracle) and we just need to verify if the solution exists (the oracle "tells us the way through the algorithm")
		- the certificate itself could be very hard to find (takes exponential time or more), but verifying it is polynomial for a DTM
- to prove that a problem belongs to NP, we just have to prove that the certificate could be verified by a DTM in polynomial time
	- or, we can build a non-deterministic TM (but that's the hard way)
- by definition the non-deterministic TM does not have to stop for "no-problems" - it can run forever (or halt in polynomial time exploring all branches (and no branch ends with "yes")) or do anything else (it is just not defined)
	- for yes-problems, the NTM has to halt in polynomial time $O(n^k)$ where $k$ is a finite number
- we formulate a exist-like question ($\exists$)
	- then we run an algo (like oracle) which will give us a solution
	- and we are able to verify this solution (= certificate) in polynomial time
##### P = NP?
- this is a famous problem, we actually don't know, if all NP-Class problems could (not) be solved by a DTM in polynomial time 
	- maybe we just didn't find the right algorithm yet
### Co-NP Problems
- complements of NP problems
	- each problem has it's complement problem (and the answer is the opposite)
	- both concrete NP problem and it's co-NP problem have the same sets of instances
		- but the yes-instances for NP problem are no-instances for a respective co-NP problem and vice versa
- these problems are same complex as NP Problems, but the difference is:
	- there is no certificate for the yes solution
		- because if it answers YES, it means nothing and the algorithm has to try all possible instances and get a YES from all of them to get a final YES answer
	- the algorithm must try ALL configurations to finish to answer "yes" (or to find a counterexample to answer "no")
- Co-NP problems have verifiable certificates for "no" answers (which are actually "yes" answers in NP problems)
- differences
	- [[#NP-Class]] problem definitions start with $\exists$ (does there exists a configuration, such that the constraints are satisfied? And this needs to be verified in polynomial time)
	- Co-NP problems start with the $\forall$ (usually for all ($\forall$) possible combinations of configurations)
		- in the verification:
			- if the "yes" must be for all configurations -> it's a Co-NP problem
- how to tell?
	- from a formulation of a verification question
		- formulate is in a way, so that the yes-answer is verified "immediately" 
			- if it is possible in polynomial time => NP
			- if "yes" has to be answered for all configurations => co-NP
### Not NP problems
![[Pasted image 20251112203009.png]]
- $\Sigma$ problems and $\Pi$ co-problems
	- $\Sigma_2$ problem has a verification in $\Pi_1$ co-problem (which is co-NP class)
	- $\Pi_1$ co-problem has a verification in $\Sigma_0$ problem (which is P class)
	- the certificate verification of a $k^{th}$ class is in the $(k-1)^{th}$ co-class


- for not-NP problems, certificate cannot be verified in polynomial time
- PSPACE (polynomial space) - problems solved using polynomial memory (regardless of time)
- EXPTIME - solvable in $2^{(poly(n))}$ time, it is proven, that it is strictly more complex than P-Class
	- 2-EXPTIME = double-exponential time
	- $k$-EXPTIME = generalization for multi-exponential
- $P \subseteq NP \subseteq PSPACE \subseteq EXPTIME \subseteq EXPSPACE$

- NPTIME (the same as NP) = PTIME using non-deterministic TM
- NEXPTIME = problems solvable in exponential time using non-deterministic TM

- NL - problems solvable in a logarithmic space (using non-deterministic TM)
- NPSPACE, NEXPSPACE
***
# Seminar
- topic: configurations
### What is configuration?
- is given by the problem (not algo, not implementation)
- it describes the state of the search for the solution
- there has to be a finite amount of all configurations - so the bruteforce can try them all
- for constructive problems, the configuration typically equals the output
### What are configuration variables?
- they encode the configuration
	- "how to encode the parts of the configuration?"
- there has to be a finite amount of them with finite and discrete domains
- for constructive problems, they are typically equal to output variables
- what to ask if I assign a configuration / configuration variables?
	- can it represent a solution
	- can the constraints be checked
	- can we evaluate the optimization criterion?
	- how can we enumerate the configurations?
	- how many possibilities? Is there a finite number?
	- which configurations are valid solutions?


- configuration variables - implementation of configuration (which is by itself a really abstract)
	- they encode the configuration
- every solution has to have a corresponding configuration (= the results)

- we have an instance of a problem and configuration is a form of  possible solution