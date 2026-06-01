This lecture is about automatization and letting solvers to solve specific problems for us. 
- it is time-consuming to design a special algorithm to each problem we encounter 
- it is better to use general problem solving:
	- problem -> human -> problem formalization -> solver -> solution
	- the human only needs to formalize the problem, so the solver can solve it by itself
		- the solver can solve problems defined in language L (so we need to formalize the problem in this language)
		- and also, we need to be able to interpret the solution from the solver

For example: planning and searching, it would be great to have a general planning/searching solver instead of defining special algorithms for planning and searching everytime
- first attempts were done through variants of predicate calculus (first-order logic, propositional calculus)
- the problem was that all rules had to be defined (even for things that do not change), and that was exhausting
	- e.g. when I execute Go(TeaShop), the only thing that changes is At(x), but I also need to specify that Have(Tea), Have(Book), Have(Biscuits) etc. didn't change for the axiom to be complete (and this has to be done for every predicate and action in the system)
	- this is called the "frame problem"
# STRIPS
- STanford Research Institute Problem Solver
- STRIPS solves the frame problem by flipping the assumption 
	- it uses a closed-world assumption, which requires listing only things that have changed (the rest is automatically considered as False or unchanged)
	- this leads to more effective representations (in cases where the minority of things change at a specific moment)
### Definition
A STRIPS instance is formally a quadruple (P, A, I, G) where:
- **P** = set of all conditions (propositions/predicates that describe the world)
- **A** = set of actions (operators), each described by:
    - `precond(a) ⊆ P` - what must be true before the action can execute
    - `effect(a) ⊆ P` - what changes after the action executes
    - In the original STRIPS formulation, effects were split into `add(a)` and `del(a)` lists
- **I ⊆ P** = initial state
- **G ⊆ P** = goal state

A state is represented as a conjuction of positive, function-free literals: `At(Home) ∧ Sells(BookStore, Book) ∧ Sells(TeaStore, Tea) ∧ Sells(TeaStore, Biscuits)`
- it is a subset of P

An action has three parts:
```
Op(ACTION : Go(y),
   PRECOND : At(x),
   EFFECT  : At(y) ∧ ¬At(x))

Op(ACTION : Buy(x),
   PRECOND : At(store) ∧ Sells(store, x),
   EFFECT  : Have(x))
```
- the action adds and deletes literals from the current state (e.g. `At(y)` was added to the state (in the conjuction) and `At(x)` was removed from the conjuction)

A plan has 4 components:
1) a set of steps (each step is an instance of the operator)
2) a set of ordering constraints `Sᵢ ≺ Sⱼ`, which means that step `i` must happen sometime before step `j`
3) a set of variable binding constraints (what variables are what (another variables or constants))
4) a set of causal links `S --c--> S'`, which means that step `S` provides precondition `c`, which is needed by step `S'`
### How it works? With Partial Order Planning (POP) algorithm
- the algorithm starts with a minimal setting: it has only `Start` and `Finish` steps
- and it builds to execution plan iteratively:
	1) select a subgoal: pick a step, which has an unsatisfied precondition `c` (so from the beginning, it would pick `Finish` step for example)
	2) choose an operator: find a new (or existing) step, whose effect will solve selected precondition `c` and add this causal link and ordering constraint
		- so the rest of the algorithm knows, that this step will solve the condition for that step (causal link) and also that this step needs to be performed before that step (ordering constraint)
	3) resolve threats: it may happen that some step T will threaten by undoing the condition protected by the causal link `Sᵢ --c--> Sⱼ`, we have 2 options how to resolve it:
		- promotion: force step T before step i, so the destructive effect happens before `c` is established
		- demotion: force step T after step `j`, so the destructive effect happens after `c` has been consumed
- the algorithm terminates when all preconditions of all steps were satisfied
### Problem with partially instantiated operators
- if there exists a causal link `S --At(Home)--> S'`, if we add another step in between with the effect `¬At(x)`, at the moment, we are not sure, if the x is `Home` (which would break the causal link) or something else (which is okay for the causal link) - it is ambiguous
- there are 3 ways to resolve this (and protect the causal link from breaking):
	1) equality constraint: directly bind e.g. `x = TeaShop` (or any non-problem value) to x, so it will not be `Home`
	2) inequality constraint: `x ≠ Home` is not that restrictive (it allows all values, but not `Home`)
	3) resolve later - ignore the possible threat, if x gets bound to `Home`, try some promotion/demotion
