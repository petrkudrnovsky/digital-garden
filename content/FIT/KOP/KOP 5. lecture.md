### Circuit complexity theory
- "what can be computed with LIMITED resources?"
- we are converging the combinatorial problem into the Boolean function 
	- we are then trying to minimize it (with respect to the size of the circuit (# of gates used) or to the depth (= longest path in the circuit))
	- gates: XOR, AND, XNOR etc.
- circuit complexity is just a different computational model
	- different from algorithm complexity theory (different classes, different "view" on the problems etc.)
	- we can determine and prove lower bounds more precisely 
		- e.g. it is proven that this problem cannot have less than X gates and Y levels (= lower bound)
	- there is another way of proving P=NP
		- if we can find a circuit of polynomial size for some NPC problem, we will prove P=NP
- complexity measures:
	- size (# of gates) = CPU time on 1 sequential CPU, because values of all gates have to be determined
	- depth (# of levels) = CPU time on unlimited number of CPUs
		- because the values of all gates are determined in parallel
	- we can often exchange one for the another
		- to have smaller depth, we have to add more gates (more gates)
		- to have less gates, we have to increase the level (the critical path from input bit to output bit is longer)

Basis - the set of gates (which are allowed)
- bounded - with limited number of inputs ($n$ is the number of input pins)
	- fan-in 2 means it has exactly 2 input pins
### P class
- = computes a problem in polynomial time on a parallel computer (gates are organized in parallel) with a polynomial number of processors
	- the time is polynomial in theory (having unlimited number of parallel processors) - but it has to be still polynomial
### $NK^k$ (Nick's class)
- polynomial size (polynomial number of processors)
- polylogarithmic time (depth is logaritmic w.r.t. $k$)
### $NK^1$
- I am able to compute any combinatorial problem with this class (being converted to Boolean function/formula)
	- the limitation with the bounded fan-in does not matter - I always can build $n$-input gate from a tree of 2-input ones 
		- their number is polylogarithmic
### $AC^0$ 
- alternating circuits, does not use NOT gates (they are allowed only at the beginning to invert the inputs)
- first order predicate logic is and extension of propositional logic (Boolean formulas and statements)
	- it introduces objects, variables, quantifiers and predicates (= properties that objects have)
	- the quantifiers can range over the variables/objects, not predicates (that's second order predicate logic)
### $ACC^0$
- modulo gate is able to substitute the XOR
	- with modulo we can do the counters 

- all gates (including MOD, MAJ) is considered as one unit (in the size and depth measure)
	- HW implementation is not considered
- all NC, AC and ACC are less complex than P class
	- meaning that the polynomial class problem could be restricted (by restricting the resources)
	- the indexes in NC, AC and ACC can go up to infinity
	- in P class there are no limitations on resources
### SAT examples
- indexes have to be encoded
- certificate checking circuit
	- Y = 000000 or 001101 (or any other combination), it gives the current state to the SAT solver - if at least one of the combinations satisfies the formula, the solver will output 1
	- this circuit has polynomial size, constant depth and can be constructed in polynomial time (and size) => it's in P
- SAT is of exponential size and polynomial depth
	- it's in NP, the certificate checking is in P
### Shannon ('49)
- he found out, that for any function, the size of the circuit (= # of gates) is exponential
	- for randomly generated functions 
	- practical functions (that we use in our devices), are special, so the construction in processors is much easier and smaller
		- because those functions are "practical/easy to define" and have structure, which allows us to compress the number of gates etc. (and have polynomial sizes etc.)
		- but it is exponential in general for random picked function (the function does not have to make sense), they act random, so they are incompressible and have exponential circuit sizes
### Communication complexity
- minimum number of bits that has to be exchanged to merge the result of two parts of the function to get the final result of the function
- Rent's rule
	- how pins and the complexity depend on each other
	- when you exponentially increase the complexity of the circuit, the number of pins (input/output) increases linearly
		- the more complicated CPU there will be, the more pins they will have
### Kolmogorov complexity
- complexity of some measure (e.g. number of bits) of how to describe an object (e.g. algorithm, but it can be arbitrary artefact/object)
- it tells us the minimum description of an object
	- e.g. "AAAAAAAAAA" could be described as "10x 'A'", which is shorter and needs less bits to encode
		- => the string (or any other object) is structured, so it can be compressed and described in compressed manner
		- and the complexity in different programming languages is different only by a constant (so it does not depend on different languages)
	- "asdjgfhaposghp" is random string, it's description cannot be shorter than "print('asdjgfhaposghp')"
		- => the length of a string is the same as the length of a program to print this string
- it shows us the maximum efficient compression of objects
	- compression tools are only trying to approximate Kolmogorov complexity, because this complexity is undecidable problem
***
# Seminar
