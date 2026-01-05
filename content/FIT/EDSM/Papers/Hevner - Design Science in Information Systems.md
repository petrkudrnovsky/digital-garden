
> [!tldr] What is the most important contribution of this paper?
> The most important contribution of this paper is introducing a conceptual framework and a set of seven guidelines to conduct, evaluate and present design science research in the IS (Information Systems) discipline.
> 
> The conceptual framework includes 3 main pillars:
> - The Environment (relevance of the research), source of the "wicked problems"
> - The Knowledge base (foundations and rigor of the research), research takes from/contributes to it
> - IS Research - building and evaluating artefacts to solve business problems
> 
> Seven presented guidelines serve as the checklist for a high-quality design research. They ensure that it is not just "routine design", but a rigorous research that contributes back to the knowledge in the field. The guidelines also state, that the result of the research has to be a purposeful artefact that must have utility for a relevant business problem and be rigorously evaluated.
> 
> The paper also highlights the interconnection of design science and behavioral science to enable full IS research and development (a technological innovation with reactive behavioral study).

- this paper is mainly about how to develop and invent better Information Systems using two main components:
	- design-science as the proactive part
		- this approach uses scientific ways to research and develop/invent new artefacts (be it a construct, model, method or instantiation (see below))
		- focused on the utility of new tools, creations, methodologies to solve problems
			- it uses kernel theories/knowledge (from behavioral science, natural science)
				- those are applied and tested though the constructing of the artefact
			- extended knowledge base is built by constructing and evaluating artefacts
	- behavioral science as the reactive part
		- studies human and organization behavior and comes with important findings about how humans behave, communicate and how they interact with the world (here with the Information Systems)
		- it seeks the "truth", it observes, it does not create anything new
			- it develops and verifies theories that explain or predict human behavior (regarding the management of the Information System)
- why do we need a better Information System? 
	- to manage people and organizations better, to save and/or utilize resources, to speed up/eliminate processes, to control etc.
	- it is driven by real business requirements
		- managers have a business strategy and they are designing the organizational infrastructure
			- and it has to be aligned with the information systems infrastructure
## The IS Research framework
- three pillars 
	- The Environment - provides the business needs (people, organizations, technology)
		- this ensures relevance (you don't want to develop anything that is not relevant)
	- The Knowledge base - provides foundations (theories, previous artefacts, experience etc.) and methodologies (data analysis, validation etc.)
		- this ensures rigor (that is has solid ground)
	- IS Research - the actual process of building and evaluation artefacts to address the business needs
## The Information System artefact
- in the design-science paradigm, an artefact has 4 different types:
	- constructs
		- vocabulary/language or symbols how to describe/define the problem
	- models
		- abstraction or representation of the problem and it's solution space
		- they use constructs to represent a real world situation
	- methods
		- methodology, algorithm, practice, guidance or approach to solve the problem (or search for a solution)
			- can be format (mathematical formulas) or informal ("this is best-practice")
		- they define processes, guidance on how to move around in the solution space (which is defined by models)
	- instantiations
		- new concrete implemented system or prototype
		- this demonstrates feasibility (it's possible to build/construct) and "proof by construction"
			- they show that the constructs, models or methods can be implemented in a working system
		- they enable researchers to learn about the real world, how the artefact affects it and how users use it
## Design science vs. routine design
- routine science is about applying already researched and proven best-practices (= existing knowledge) and putting them together to design a system
	- a database system, CRM, financial reporting tool etc.
- design science is about inventing new approaches and methodologies or severely enhancing existing solutions by exploring new possibilities and ways
	- key difference is that the research must contribute to the archival knowledge base
	- it solves so-called "wicked problems":
		- unstable and changing requirements
		- ill-defined environmental contexts
		- complex interactions between components/departments/people etc.
		- needs to be able to change artefacts or design processes
		- dependence on human cognitive (creativity) and social (teamwork) abilities etc.
## Two main loops
![[Pasted image 20260104175502.png]]
- build-and-evaluate loop needs to repeated to be able to get feedback to learn and refine the artefact and improve the understanding of the problem
## Seven guidelines for a proper design-science research
- this provides a checklist for a high-qualite design research
- these guidelines do not have to be 100% fullfilled, but the should be followed (and refined for the current research and it's possibilities)
#### Guideline 1: Design as an artefact
- Design-science research must produce a viable artefact in the form of a construct, a model, a method, or an instantiation.
	- constructs = a new way of talking about a problem
	- models = a way to visualize the problem (using constructs)
	- methods = a set of instructions on how to solve the problem
	- instantiations = a working prototype
	- it's not about just writing theory, you have to create something people can actually use
#### Guideline 2: Problem Relevance
- The objective of design-science research is to develop technology-based solutions to important and relevant business problems.
	- in other words, the research and the final artefact must matter to people and organizations/businesses
	- the goal is to tackle wicked problems that people and businesses have and solve them
	- ask yourself "does this make businesses more efficient?"
#### Guideline 3: Design Evaluation
- The utility, quality, and efficacy of a design artefact must be rigorously demonstrated via well-executed evaluation methods
	- I have to prove that my artefact actually works - this is done by testing it
		- utility = how useful it is
		- quality
		- efficacy = how well it does its job
	- methods:
		- case studies = testing in a real company
		- experiment/simulation = testing in a laboratory
		- white box/black box testing
		- descriptive = constructing scenarios for demostrating utility
	- testing should show that the artefact is simple and powerful
#### Guideline 4: Research Contributions
- Effective design-science research must provide clear and verifiable contributions in the areas of the design artefact, design foundations, and/or design methodologies.
	- if nothing is added to the "Knowledge base" of the society, it's just routine design
	- you could add:
		- the artefact itself - if it solves something that no one could solve before or enhances existing artefacts in a new way
		- new foundations - a new algorithm, a new theory
		- new methodologies - e.g. a better way to measure if the system is working, better way to evaluate
#### Guideline 5: Research Rigor
- Design-science research relies upon the application of rigorous methods in both the construction and evaluation of the design artefact.
	- rigor = doing things correctly and professionally
	- I should use math logic, physics laws, proven scientific theories to (1) build and (2) test my artefact (I should not just guess)
	- warning - I have to balance rigor with relevance (if it is correctly mathematically proven, but it is not relevant in any business - it's not design science)
#### Guideline 6: Design as a Search Process
- The search for an effective artefact requires utilizing available means to reach desired ends while satisfying laws in the problem environment.
	- it's not about finding the perfect solution, but a solution that is good enough within the laws of the environment
		- finding the perfect solution is often not possible
		- design is a search for a good enough solution through the iterative "generate/test" cycle
			- when the artefact fails the test, we can learn from it, improve it and test again until it is good enough
#### Guideline 7: Communication of Research
- Design-science research must be presented effectively both to technology-oriented as well as management-oriented audiences.
	- technical audience should know the details (code, math) to be able to rebuild or extend my work
	- management audiences should know the effectiveness and practical applicability of the artefact
		- they decide if the company should invest money and resources to it (and make it relevant)