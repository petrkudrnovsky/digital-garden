This is a comprehensive summary of the Management Information Systems (MIS) research methods course, covering scientific foundations, research process, theory, qualitative and quantitative methods, mixed methods, design science, and research philosophy.

---

# What is science? (Ch. 2)
Science is about obtaining facts through proper methods in a systematic and organized way. Scientific research must be falsifiable, questionable, and conducted through defined and repeatable experiments.
By doing scientific research, we contribute to a "body of knowledge" by:
- improving explanations of phenomena (theories)
- collecting scientific evidence (e.g. case studies in unique environments)
- improving the methods of science themselves (better measurements, better processes)
### Key properties of the scientific method
- **Replicable** - research can be repeated
- **Independent** - free from subjective judgment (beware of paradigm effects)
- **Precise** - constructs and measurement precision must be properly defined
- **Falsifiable** - theories must be stated in a way that allows them to be disproven
### From observation to hypothesis
The path from the real world to testable science follows a chain:
- we observe **phenomena** we want to explain
- we create **concepts** (mental representations) and connect them through **propositions**
- we turn concepts into **constructs** - things in the real world that can be measured
- inside constructs, **variables** define current measurable values
- a **hypothesis** is a testable relationship between variables
### IS research as design science
IS research is a "Design Science" - distinct from classical science (physics, math), natural science (biology, chemistry), and social science (sociology, psychology). It combines:
- science approaches (rigor, replicability, knowledge base, falsification)
- engineering principles (learning by doing, evaluating and testing, prototypes)

The IS research cycle has two main loops:
- Environment loop - meeting real-world needs
- Knowledge base loop - storing new information, principles, methods from experience

A central Design cycle loops through improving the artefact and testing/evaluating it.

---

# Starting the research process (Ch. 3)
### Motivation and research question
Before starting research, I need:
- a **motivation** (interesting observation, gap in knowledge, helping a business)
- a **research question** (the most important part)
	- "what/who/where" questions lead to more **exploratory** research
	- "how/why" questions lead to more **explanatory** research
### Three ways of thinking
##### Deduction
- direction: general -> specific
- start with existing theory, deduct a hypothesis, test it on new data
- key: falsifiability - trying to (dis)prove the theory
- formal: given a rule and a cause -> deduce the effect
##### Induction
- direction: specific -> general
- the "discovery" way - observe cases/phenomena, find patterns, derive general rules
- warning: beware of "weak induction"
- formal: given a cause and an effect -> induce a rule
##### Abduction
- direction: observation -> simplest/most likely explanation (Occam's razor)
- essentially an educated guess, trial-and-error
- formal: given a rule and an effect -> abduce a cause
### Reasoning modes
- **Exploration** - discovering new phenomena or patterns (abduction, induction)
- **Rationalization** - building theoretical explanations (deduction, abduction)
- **Validation** - testing if explanations hold up against new data (induction, deduction)

Good research should combine all three modes.
### Research decisions
Each research project positions itself on several spectrums:
- Aim: Exploratory vs. Explanatory
- Method: Qualitative vs. Quantitative
- Boundary: Case vs. Statistical
- Setting: Field vs. Laboratory
- Timing: Cross-sectional vs. Longitudinal
- Outcome: Descriptive vs. Causal
- Ambition: Analysing vs. Designing
### Research methodologies
Each methodology has different strengths:
- **Controllability** - researcher's control over events (strong in Design science)
- **Deductibility** - support for deductive reasoning (strong in Computational and Quantitative)
- **Repeatability** - can be repeated with same/similar results (strong in Design science and Computational)
- **Generalisability** - results can be generalized beyond the study (strong in Quantitative)
- **Explorability** - potential for unknown findings (strong in Qualitative)
- **Complexity** - can lead to exhaustive knowledge contributions (strong in Qualitative)

The five main methodologies are:
- Quantitative strategies
- Qualitative strategies
- Mixed methods
- Design science methods
- Computational methods
### Systematic literature review (SLR)
Two ways to get information from existing literature:
- **Ad hoc literature review** - informal exploration, good for initial research proposals, not systematic or repeatable
- **SLR** - a predefined research strategy for answering a specific research question in an unbiased and rigorous way
##### Three phases of SLR
**1) Planning the review**
- define research questions for the literature review
- define search strategy (where to search, which terms/strings)
- define study selection criteria (inclusion/exclusion)

**2) Conducting the review**
- identify potential sources (automated search via WoS, Google Scholar, or manual backward/forward search)
- apply inclusion/exclusion criteria
- obtain full articles and extract data
- synthesize relevant information

**3) Reporting the review**
- report the protocol and deviations from it
- report search results and inclusion/exclusion outcomes
- report table of included studies with relevant information

---

# Theory (Ch. 4)
### Definition
> A theory is a system of constructs and relationships between those constructs that collectively present a logical, systematic, and coherent explanation of a phenomenon of interest
### What theory is NOT
- it is not universal (it has assumptions and boundary conditions)
- it is not self-perpetuating (it has implications, not an end in itself)
- it is not: pure data, description, prediction, design, a list of hypotheses, or an ideographic explanation of a single specific situation
### Key building blocks
- **Constructs** ("What?") - can be independent, dependent, mediating, or moderating
- **Relationships** ("How?") - may be associative, compositional, unidirectional, bidirectional, conditional, or causal; they are testable
- **Justifications** ("Why?") - theoretical grounding explaining why a relationship would exist
- **Boundaries** ("Who/Where/When?") - scope conditions under which the theory holds; a theory without stated boundaries is unfalsifiable
### Types of theories in IS
- **Type I - Analysis** - says what is; descriptive, no causal claims, no predictions
- **Type II - Explanation** - says what is, how, why, when, where; provides explanations but no testable predictions
- **Type III - Prediction** - says what is and what will be; testable propositions but weak causal grounding (common in ML/data-based research)
- **Type IV - Explanation and Prediction (EP)** - says what is, how, why, when, where, and what will be; the strongest type with both testable propositions and causal explanations
- **Type V - Design and Action** - says how to do something; prescriptions for constructing artefacts (Design Science Research)

Research usually moves from Type I upwards.

---

# Qualitative research methods (Ch. 5a)
Qualitative research is about understanding phenomena in real-life context - when boundaries are not clear, phenomena are complex, context-dependent, or not well-defined.
### Data collection techniques
##### Interviewing
- formats: descriptive, exploratory, explanatory
- semi-structured (between rigid survey and open conversation)
- benefits: rich, targeted, insightful
- drawbacks: reflexivity (interviewee responds with what the interviewer wants to hear), possible inaccuracy, bias
##### Observation
- direct observation - researcher is not involved (sitting in meetings, taking notes)
- participant observation - researcher participates (better insight, but influences what happens)
##### Documentation
- analyzing existing documents (meeting minutes, emails, project reports, system logs)
- valuable because they were not created for the research - no reflexivity
- types: structured, semi-structured, unstructured
##### Triangulation
Using multiple data sources or methods to study the same phenomenon:
- across sources (interviews + documents)
- across methods (quantitative + qualitative)
- across researchers
- across theories
### Data analysis techniques
- **Coding** - assigning labels to chunks of data in three stages:
	- open coding - uncovering and labelling concepts with higher-level categories
	- axial coding - organizing concepts into causal relationships
	- selective coding - identifying central categories and relating other concepts to them
- **Memoing** - subjective reflection about what was happening (inside thoughts, connections)
- **Critical incidents** - examining series of events to explore relationships between constructs
- **Content analysis** - semantic analysis of text
	- conceptual (presence, frequency of concepts)
	- relational (how concepts relate)
- **Discourse analysis** - structuring and unfolding communication ("how it is said")
### Rigor in qualitative analysis
- **Dependability** (reliability) - another researcher with same process should reach similar conclusions
- **Credibility** (internal validity) - does it really reflect reality?
- **Transferability** (external validity) - do findings apply in a different setting? (not aiming for generalization, but patterns may be applicable elsewhere)
### Case study
- investigating a current phenomenon within its real-life context in depth, using multiple data sources
- benefits: richness, depth, real-world context, new emerging concepts
- drawbacks: problems with controlled deduction, replicability, control mechanisms
### Action research
- introducing changes/interventions and studying the effects
- the researcher is the agent of change
- two goals: solving current organizational problems + contributing to science
- iterative, cyclic process (similar to Lean startup)
### Grounded theory
- a new theory inductively generated based on (grounded in) qualitative data
- characteristics:
	- focus on theory building, not testing
	- no pre-conceived hypothesis from prior domain knowledge
	- iterative process of collecting data, coding, and analysing

---

# Quantitative research methods (Ch. 5b)
A set of techniques to answer research questions with quantitative data.
### Typical procedure
- come up with a model, theory, and hypothesis
- develop or select concrete measurement instruments
- collect data
- analyse data (e.g. statistical modelling)
- evaluate results
### Variables and confounders
- **Constructs** are general terms (e.g. "business/IT alignment"); **variables** are their operationalized, measurable versions
- types of variables:
	- **Dependent variables** - outcomes
	- **Independent variables** - predictors
	- **Confounders** - related to both dependent and independent variables; can create spurious relationships if not accounted for
	- **Control variables** - used to neutralize confounders, either by holding them constant or including them in the statistical model
	- **Mediator variables** - sit between independent and dependent variables (full mediation = everything goes through the mediator; partial = both direct and indirect effects exist)
	- **Moderator variables** - change the strength of the relationship between independent and dependent variables (act as catalysts)
- **Conditional process modelling** = combination of mediator and moderator influences
- variable types:
	- categorical (nominal, ordinal) - binary or polytomous
	- quantitative - interval and ratio measurement levels
### Quality criteria
- **Reliability** - consistent results on replication
- **Validity**:
	- construct validity - methods measure what we intend
	- internal validity - observed effect is due to the hypothesized cause (risk: confounders)
	- external validity - relationship holds in other settings
### Measurement
- **Operationalization** - choosing/creating a concrete method to measure a construct
- levels of measurement:
	- **Nominal** - differentiate between values (male/female)
	- **Ordinal** - differentiate and order (levels of agreement)
	- **Interval** - ordering + interpretable differences (temperature)
	- **Ratio** - meaningful zero point, relative comparisons possible (length, weight)
- measurement quality:
	- **Validity** - measuring the intended construct (influenced by systematic error)
	- **Reliability** - consistent results (influenced by random error); necessary but not sufficient for validity
### Surveys and questionnaires
- **Survey** - list of questions covering a variety of topics
- **Questionnaire** - focuses on one construct or a related set of constructs
- rules: clear instructions, one construct measured by multiple questions, avoid complicated questions/double negations, exhaustive and mutually exclusive response options
### Sampling
- **Population** -> **Sampling frame** (actual subset to choose from) -> **Sample**
- **Stratum** = a subset where elements share some characteristic
- probability sampling:
	- simple random sampling
	- systematic sampling (first element random, then predetermined pattern)
	- stratified random sampling (divide into strata, then random sample each)
- non-probability sampling:
	- convenience sampling
	- snowball sampling (small group recruits others)
	- purposive sampling (researcher's judgment)
### Statistical tests
- **Correlation tests** (Pearson, Spearman) - test relationships between variables (correlation ≠ causation)
- **Regression analysis** - relationship between independent variables and one dependent variable
- **Tests of mean differences**:
	- independent samples t-test - difference between two groups on a continuous dependent variable
	- independent samples z-test - difference between two groups on a proportion
	- ANOVA - difference between more than two groups (avoids running multiple t-tests)
	- ANCOVA - ANOVA that controls for covariates/confounders
- **Tests of repeated measures** (longitudinal):
	- paired samples t-test - one group, two time-points
	- repeated measures ANOVA - one group, more than two time-points
	- mixed ANOVA - multiple groups, multiple time-points
### Latent constructs and models
Some variables (like happiness) cannot be measured directly - they are inferred through observed variables.
- **Exploratory factor analysis** - exploring which observed variables cluster under latent dimensions (without strong prior theory)
- **Confirmatory factor analysis** - formally testing a hypothesized factor structure against new data
- **Structural equation modelling** - regression with latent variables (measuring causal relationships between latent constructs)
- two types of latent constructs:
	- **Reflective** - the latent construct causes changes in its indicators
	- **Formative** - the indicators together form/cause the latent construct

---

# Mixed methods and design science (Ch. 5c)
### Mixed methods
Combining qualitative and quantitative methods within one study to get a better picture of "what, how much, why, and how."
- all methods are based in one research question and finish in **meta-inference** (overall conclusion from integrating all method conclusions - they may complement, contradict, or converge)
- types: sequential, conversion, parallel, integration
##### Strengths
- qualitative adds meaning to quantitative results (and vice versa - numbers add precision)
- benefits from individual strengths of different methods
- easier to generate and test theory within one study
- broader range of research questions, stronger evidence, increased generalizability
##### Weaknesses
- difficult for a single researcher
- must learn multiple methods and how to mix them
- more resource-intensive and time-consuming
- some methodological details are still debated
- can be difficult to publish
### Design science
> A research paradigm in which a designer answers questions relevant to human problems via the creation of innovative artefacts, thereby contributing new scientific evidence to the body of knowledge.

---

# Research philosophy (Ch. 1/Foundation)
The researcher's philosophy is the outermost "layer" - it directly influences research questions, methods, data collection, and everything downstream. The key takeaway: same topic + different paradigm = different research question, methods, and contributions.
### Research paradigms
A paradigm is built on these pillars (top to bottom):
- **Ontology** - how is reality defined?
- **Epistemology** - how do I know something? What is knowledge?
- **Theoretical perspective** - what approach to get knowledge?
- **Methodology** - what procedure to get knowledge?
- **Methods** - what tools to get knowledge?
- **Sources** - what data can we collect?

The **research onion** model shows how these layers nest into each other.
### Philosophical identities
##### 1. Positivism - "The natural scientist"
- reality is objective, fixed, measurable
- researcher is detached and value-free
- deductive approach, quantitative data
- typical methods: experiments, surveys
- criticism: strips away social context
##### 2. Interpretivism - "The artist/actor"
- reality is socially constructed through culture and language
- researcher is part of what's being researched
- inductive approach, qualitative data
- typical methods: case study, grounded theory
- criticism: accuracy - can you capture someone else's lived experience?
##### 3. Critical realism - "The archaeologist"
- bridges positivism and interpretivism
- reality is objective but layered - surface phenomena are caused by deeper hidden mechanisms
- abductive or deductive+inductive, qualitative or quantitative
- typical methods: archival research, survey
- criticism: lack of step-by-step methodological guidance
##### 4. Postmodernism - "The political activist"
- reality is subjective, fragmented, discursively constructed
- challenges power structures, highlights marginalized views
- abductive or deductive+inductive, mainly qualitative
- criticism: limited practical solutions
##### 5. Pragmatism - "The architect"
- reality is the practical consequences of ideas
- whatever works for solving problems in specific contexts
- abductive or deductive+inductive, any data type
- typical methods: mixed methods, action research
- criticism: researchers sometimes bundle data without truly integrating it