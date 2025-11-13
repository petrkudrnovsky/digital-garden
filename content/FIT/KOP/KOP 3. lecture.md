### Reduction of problems
- this is not about making problems simpler
- it is a transformation to another problem
- we can solve a problem transforming it into another problem (that is at least as hard, could be harder) and solving that problem
	- the original problem should be as most as hard as the new problem
### Hardest problems in the class
- those problems, to which all other problems from the class could be reduced to
	- all problems can be solved using the solver of the hardest problem in the class
### Karp reduction (= polynomial reduction)
- transforms a decision problem to another in polynomial time on deterministic TM
- notation: $\Pi_1 \le_P \Pi_2$ 
- it is only for decision problems, so the result of $\pi_2$ problem is the same as the result of $\pi_1$ problem
	- it is simply only True/False
- Karp reduction has property of transitivity
- there is no reduction symmetry in general (we cannot transform to each other in general)
	- there are problems, where is it possible (the problems are equivalent)
	- but in general, if we can reduce one problem to another, we cannot do it the other way
		- "simple" problems can be reduced to "hard" problems, not vice-versa
- what is polynomial (Karp) equivalence?
	- if there is equivalence between problems in terms of Karp-reduction
		- one problem can be reduced to another and vice-versa
		- they are "equally difficult"
### NP-Hard problems
- problems that are at least as hard as all NP problems
	- they don't necesarrily have to be in NP class, for example the Halting problem is also NP-Hard
- all decision problems from NP can be Karp-reduced to an NP-Hard problem
- if a decision problem is NPH, also the optimization, constructive and enumerative versions of the problems are NPH
	- thanks to [[#Cook's Reduction (= polynomial Turing reduction)]]
	- the decision problem solver could be used as a subroutine to solve other versions of the problem
- an optimization problem is NP-Hard if it's decision problem is also NP-Hard
- NP-Hard and co-NP-Hard problems
	- it depends on the reduction used
		- Karp-reduction: the NPH and co-NPH are disjoint
		- Turing reduction: NPH = co-NPH
### NP-Complete
- are NP and at the same time they are NP-Hard (because NP-Hard problem can be outside of NP class)
- definition:
	- a problem $\Pi$ is NPC iff 
		- $\Pi$ is in NP
		- all problems in NP are Karp-reductible to the $\Pi$ 
- it is an equivalent class
	- so any NP-Complete problem can be transformed to any other NP-Complete problem w.r.t. Karp-reduction
- by an NPC solver, I can solve all NPC problems
	- if I find NPC solver running in polynomial time, I can solve all NP problems in polynomial time and I will prove $NP=P$ and make a lot of money
##### How to prove that the problem is NPC?
- how to prove it's in NP (=> not harder then NP)?
	- we can solve it by non-deterministic TM OR we can prove, that the verification of the solution is in polynomial time (the certificate)
- how to prove it's in NPC (=> not simpler then NPC)?
	- we have at least one known problem in NPC
	- our problem should be solvable by the origin NPC problem solver
		- so the problem has to be reductible to our problem (in polynomial time **for all instances**)
		- in other words: find some NPC problem and prove that it is reducible to our problem in polynomial time for all possible instances
- complete proof
	- we take any NPC problem, try to reduce this problem to our problem in polynomial time -> if we succeed, we have a NPC problem
	- with the solver of our problem we are basically able to solve any known NPC problem
##### NPC problem examples
- SAT, Knapsack, TSP, Hamiltonian Circuit, Vertex cover etc. (more than 3000 of them are listed)
### X-Hard and X-Complete problems
- just a generalization of NP-Hard and NP-Complete
	- X-Hard problems are at least as hard as X
	- all problems from X can be efficiently (in polynomial time, not losing error etc.) reduced to a X-Hard problem
### Cook's Theorem
- he has proven that the SAT problem is NPC
	- meaning that the NPC class is not empty a we can easily prove that other problems are NPC using the proof in [[#How to prove that the problem is NPC?]]
### NPI problems
- NP intermediate problems ("in the middle")
	- no polynomial algorithm is known yet
	- no proven NP-completeness yet
- they have to exists, otherwise P=NP
### Turing reduction
- for both decision and optimization problems
- problem $\Pi_1$ is Turing reductible to $\Pi_2$ ($\Pi_1\le_T\Pi_2$) if there exists a TM program $M_1$ solving each instance of $\Pi_1$ by calling a subroutine (which is TM $M_2$ solving $\Pi_2$)
	- subroutines could be called many times + it also considers incomputable problems
- Karp reduction is the special case of the Turing reduction
	- subroutine is called exactly once and the complexity is polynomial
- every computable problem is Turing reducible to any other computable problem
	- even between complexity categories
- all computable problems are Turing-equivalent
	- exception are undecidable problems
		- e.g. Halting problem (we cannot decide for the algorithm if it will stop or not)
### Cook's reduction (= polynomial Turing reduction)
- the subroutine is called the polynomial number of times 
	- nothing is said about the complexity of the subroutine (if it is an oracle with complexity of $O(1)$, the total complexity is polynomial)
- no one has said anything about the complexity of the solver (=subroutine)
	- but it has to be called polynomial number of times
### Relations summary
![[Pasted image 20251112222707.png]]