- ChatGPT etc. are data-driven AI
	- give it a load of data and let it find the underlying patterns (like machine learning)
	- data + answers = rules
- we will talk about model-based AI ("the clasiccal AI")
	- = symbolic AI, rule-based AI
	- programmers define the exact rules and bake them into the system
	- rules + data = outcome
### What is an intelligent agent?
- it has some properties:
	- it has sensors/inputs
		- used to describe the environment around the agent
	- it has effectors ("output")
		- used to change the environment
	- it performs decision making
		- planning
		- game theory
		- search
### Different approaches
- a matrix introduced in the AI - The modern approach book
	- https://aima.cs.berkeley.edu/
		- a really good book on AI (used on many universities)
- four different approaches:
	- **Thinking humanly** - the cognitive science approach. AI models the actual mental processes of humans (how brains work). Closely tied to psychology and neuroscience.
	- **Thinking rationally** - the "laws of thought" approach. Uses formal logic to represent and reason about the world correctly. Rooted in Aristotle's syllogisms.
	- **Acting humanly** - the Turing Test approach. A machine is intelligent if its behavior is indistinguishable from a human's. Focus on observable output, not the internal process.
	- **Acting rationally** - the **rational agent approach**, which the book itself adopts. An agent acts to achieve the best expected outcome given its goals and available information. This is the dominant paradigm in modern AI.
### Turing test
- by Alan Turing, he founded the foundations of Turing machines (and laws of computability), he also helped to resolve the Enigma cypher code
- A.L.I.C.E - a simple, pattern matching algorithm, that has performed really well of simulating a human behavior
- this test basically says: "if it acts intelligently, it is intelligent"
### A Chinese room argument
- a program could look smart even though it's just reading information without understanding them properly (a guy in a room full of Chinese books just reads from them without actually undestanding, what do they mean)
- it was a philosophical argument - distinguishing syntax and semantics
	- it contradicts the Turing test, if it behaves intelligently, it could just be a sophisticated work with letters and syntax that is far from understanding the semantics of it
### History
- Shakey - a first autonomous robot
	- was able to get the environment around it, plan the task and execute it successfully
- DARPA Grand Challenge - first competition of autonomous self-driving cars
	- the biggest problem is the explainability of the AI
		- if it is not explainable, we cannot debug it, see, why it decided in the way it has decided
	- the autonomous cars are not that explainable, and that's a problem
		- the chatbots etc. are also not-explainable
- Deep Blue vs. Garry Gasparov
- AlphaGo
### Neuro-symbolic AI
- neuro part: machine learning (gaining knowledge from human experience etc.)
- symbolic part: the A.I. part: no knowledge, but strong reasoning and explainability
- the connection of those two parts would be best, but it's still not developed to some good level
	- it's an open challenge
### Branches of AI
- it ranges from computer vision, pattern recognition, planning/scheduling, machine learning, robotics, agent systems, text generation, autonomous robots/cars etc.
***
# Tutorial 1
- add Surynek to Gitlab whenever I want to check my homework and get points
	- if he leaves the project, it means that he has checked it
	- for next assignment, I will have to add him again
- problem definitions:
	- 2 jugs problem
		- fill the 5l to the full
		- pour 3l to the 3l, leaving 2l in the 5l one
		- empty the 3l one
		- pour the remaining 2l to the 3l one
		- fill the 5l up again
		- and pour the 1l to the 3l one
		- leaving 4 l in the 5l
	- how to move a horse around a chessboard to visit all the cells?
		- for any $n$
	- how to put queens onto a chessboard so they cannot endanger one another?
		- for any $n$
- state space
	- for these kinds of problems (if we want to transfer them into graph problems):
		- we need to define a state (= vertex)
			- it's kind of a snapshot of the environment (only the important facts)
		- here it would be an ordered pair (number of liters in first, number of liters in the second)
		- then we need to decide which type of graph it would be
			- (un)directed graph, here it would be directed graph
- backtracking notes 
	- what it is, how is it used etc.


