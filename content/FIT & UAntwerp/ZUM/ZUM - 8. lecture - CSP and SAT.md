We can basically model any NP problem with CSP or SAT (both are NP-Complete, i.e. the hardest problems in NP)
- there are many efficient and fast solvers for CSP and SAT
	- years of development, competitions
	- advance in solving SAT and CSP means an advance in solving a lot of problems
- as CSP and SAT are NP-Hard and we can reduce any practical NP problem to them 
	- [[KOP 3. lecture]]
### CSP problems
- = constraint satisfaction problem
- a way of modelling a problem
- it is expressed as a triple $(X,D,C)$
	- X - finite set of variables
	- D - finite domain of the possible values of the variables
	- C - a set of constraints over X
- it consists of two main parts:
	- 1) filtration (= constraint propagation)
		- using the constraints to filter values from domains
		- when a domain shrinks to one value, I can commit to it
		- this part does not do any search, it just prunes
	- 2) search
		- begins, when there are multiple values possible left after the pruning part
		- pick a value, descend and repeat until I get a contradiction with the constrainst, backtrack, and try another value (indeed a DFS)
		- a state = partial assignment of variables
		- a consistent state = assigned values do not violate constraints
		- important property: the assignment order is commutative (any ordering can be used)
- the goal state is that all variables are assigned and the assignment is consistent
	- consistent assignment = no constraints are violated
	- this is also being checked at every "partial assignment" (but the constraint that is being checked has to have all variables assigned - otherwise it is uncheckable - we cannot verify if it is satisfied or not)
##### Example problems to solve
- Sudoku
	- having `allDifferent` constraint (digits have to be different in all rows, columns and sub-squares)
- Graph coloring problem
	- simple assignment of variables (where the set of constraints is a conjunction that needs to be satisfied)
	- built only from the binary inequality constraints
- Verbal arithmetics
	- `SEND + MORE = MONEY`
		- each letter is a distinct digit (0..9) and the CSP determines what digit each letter is, so this equation holds
		- the constraints are written as algebraic formulae with carry variables $R_1, \ldots, R_4$ (much more compact format than a list of compatible tuples)
### SAT problems
- problems are usually specified in the DiMACS CNF format
```txt
c This is a comment
p cnf 4 2
1 -2 0
-1 3 4 0
```
- `p cnf [num of variables] [num of clause]`
- each line is one clause (a sum of literals), always ending with 0
- solvers:
	- based on hill-climbing algorithm: WalkSAT, GSAT and many others
- short intro to CNF Boolean formulas (that is all SAT understands):
	- a literal (a variable or it's negation)
	- clause (a disjunction of literals)
	- term (conjuction of literals)
	- CNF = conjunction of clauses
### Modelling CSP as SAT
- we need to translate the finite-domain variables and constraints into a pure Boolean form -> we will encode it
- encoding of the variables
	- one variable can take |D| values, so we create |D| propositional variables
		- "$y_d$ is true iff x takes value d"
		- one CSP variable is mapped to the |D| formulas
	- but, we also have to add formulas that forbid multiple assignments of values to one variable 
		- "at most one assignment must be true" AND "at least one assigment must be true" - this ensures that each variable has exactly one assigment 
- encoding of the constraints
	- inequality
		- two variables differ as long as they never take the same value (forbid them taking the same value at the same time)
		- e.g. a constraint "X != Y" for the domain {red, green, blue} translates into three Boolean formulas:
			- X and Y are not both red
			- X and Y are not both green
			- X and Y are not both blue
	- equality
		- two variables are equal, if they take the same value, so at least one value d is shared by both
- this encoding is simple and verbose, there exist more sophisticated encodings that take up less space