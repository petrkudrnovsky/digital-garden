- [2 minute summary of COBIT 2019](https://www.youtube.com/watch?v=v1Q-Zxf8rHQ)
	- my take: it is a tool which designs a tailored system for the company, so it knows how the IT department (and all information technologies) in the company performs
		- do they take unnecessary risks? are there many hardware failures? how many IT skilled people do we have?
	- the COBIT process is just answering a lot of questions, putting the answers to the framework and getting a tailored system:
		- what capabilities the company should focus on?
		- how to implement them?
		- how to assess their maturity?
	- the COBIT experts are not paid for filling up a complex Excel, they are paid for knowing, which cell to fill and why
***
Core idea: COBIT gives you a structured way to set up IT governance that's customized to your organization. It's not a one-size-fits-all checklist - it's a configurable system.
### Why Do We Care About COBIT?
COBIT provides practical guidance on:
1. **How to set up** a Risk function and a Security function in an organization
2. **Which processes** are needed for IT risk management and information security management
3. **How to build risk scenarios** (structured templates for thinking about what could go wrong)
4. **How to mitigate risks** using governance and management objectives

The core insight: **good IT governance → better alignment → more value from IT for the business.** Risk management is a key enabler of that chain.
### 3.1 COBIT as an I&T Framework
COBIT is a framework for governance and management of **enterprise information and technology (I&T)**. Important distinction:
```
IT   = the IT department
I&T  = ALL technology and information processing in the enterprise,
       regardless of which department owns it
```
- So COBIT covers everything, not just what the IT department does.
#### Governance vs. Management
This distinction comes up constantly. Think of it as two organizational layers:

```
GOVERNANCE (Board level)
  → Sets direction: "What do we want from IT?"
  → Evaluates options, prioritizes, makes strategic decisions
  → Monitors: "Are we on track?"

MANAGEMENT (Executive level)  
  → Executes direction: "How do we get there?"
  → Plans, builds, runs, and monitors day-to-day activities
  → Follows the direction set by governance
```

The board says "we need to reduce IT risk exposure by 30%." Management figures out how to actually do it.
#### Three Outcomes of Enterprise Governance of I&T (EGIT)
1. **Benefits realization** - getting actual value from IT investments
2. **Risk optimization** - managing IT risk to acceptable levels
3. **Resource optimization** - using IT resources efficiently
### 3.2 COBIT 2019 Product Architecture
#### The Core Idea: Tailoring
There is no universal IT governance setup. Every organization is different, so COBIT gives you:
- **40 governance and management objectives** (the "boxes") as a starting menu
- **Design factors** to determine which boxes matter most for YOUR organization
	- if my strategy is to be a "cost leader", the innovation box becomes less important, whereas the project management box becomes more important
- **Focus areas** for deep-dives into specific topics (security, risk, DevOps, SMEs, etc.)
	- if some area (e.g. security) is very important to me, I can turn it into a focus area
	- [[DRS - The risk and security function]] - the focus on the specific topics

Think of it like this: COBIT Core is a generic template with 40 items. Design factors are the knobs you turn to customize it. Focus areas are optional add-on modules.
![[Pasted image 20260408104236.png]]
### Key Concepts: Principles

COBIT has two sets of principles - one for the governance **system** you build, one for the governance **framework** itself.
#### Governance System Principles (what your system should be)

| #   | Principle                               | Plain meaning                                                                                                                                                                                                         |
| --- | --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| A   | **Provide stakeholder value**           | Everything must ultimately create value for stakeholders                                                                                                                                                              |
| B   | **Holistic approach**                   | It's not just processes - you need people, information, culture, infrastructure all working together. We have to look at it as an interconnected system where everything matters. Do not look at separate parts only. |
| C   | **Dynamic governance system**           | Strategy, technology, and threats change; your governance must adapt. When one or more of the desing factors are changed, the impact on the EGIT system must be considered                                            |
| D   | **Governance distinct from management** | Keep board-level direction separate from operational execution (see the difference between governance and management)                                                                                                 |
| E   | **Tailored to enterprise needs**        | Use design factors and focus areas to customize the government system to the enterprise needs                                                                                                                         |
| F   | **End-to-end**                          | Cover all technology in the organization, not just the IT department (see the difference between IT and I&T)                                                                                                          |
#### Governance Framework Principles (what COBIT itself should be)

| #   | Principle                      | Plain meaning                                                                                       |
| --- | ------------------------------ | --------------------------------------------------------------------------------------------------- |
| 1   | **Based on conceptual model**  | Structured, consistent, with clear component relationships allowing for consistency and automation. |
| 2   | **Open and flexible**          | New focus areas can be plugged in without breaking the whole thing                                  |
| 3   | **Aligned to major standards** | Doesn't contradict ISO, NIST, COSO, etc.                                                            |
### Key Concepts: The Goals Cascade
This is the mechanism that connects "what the business wants" to "what IT needs to do." It flows top-down:

```
Stakeholder Needs & Drivers
        ↓
  Enterprise Goals      (13 generic goals, e.g., "managed business risk")
        ↓                   organized by Balanced Scorecard dimensions:
                            Financial, Customer, Internal, Growth
  Alignment Goals       (13 IT-related goals, e.g., "managed I&T-related risk",
        ↓                   "security of information and privacy")
  Governance &          (the 40 boxes - specific things you must do,
  Management Objectives      e.g., APO12 Managed Risk, APO13 Managed Security)
```

The mapping works through **Primary (P)** and **Secondary (S)** links.
- there is a matrix table of enterprise goals and alignment goals and there could be linked with a (P) primary link or (S) a secondary link or nothing (not related)
- if they are linked, they support each other

Not every enterprise goal connects to every management objective - the cascade helps you figure out which objectives matter most for your specific goals.

Common misunderstanding: alignment goals are NOT internal IT department objectives. They represent what IT should deliver to the whole enterprise.
### Key Concepts: The 5 Domains and 40 Objectives
The 40 governance and management objectives are grouped into 5 domains in two groups:
- first group: the government objectives
- the second group: the management objectives

![[Pasted image 20260408110856.png]]

**"But EDM and MEA both monitor - aren't they the same?"** No:
- EDM monitors at the **strategic** level - is the overall direction being followed?
- MEA monitors at the **operational** level - are specific processes performing well?

Risk-relevant objectives you'll see often: **EDM03** (Ensured Risk Optimisation), **APO12** (Managed Risk), **APO13** (Managed Security), **DSS05** (Managed Security Services).

![[Pasted image 20260408110959.png]]
### What's Inside Each Box? (The 7 Components)
Each governance/management objective is described through 7 interacting components. Think of these as "everything you need to make this objective work" (the holistic approach):

![[Pasted image 20260408111803.png]]

In the COBIT book, for each objective you get:
- Process components
	- Process -> Practices -> Activities
		- one process has one or more practices and each practice has one or more activities
		- each activity is tagged with a capability level (0 - nothing is organized, 5 - the best possible organization)
			- e.g. if I want to reach capability level 2, I need to do first 5 activities, if I want the capability level 3, I need to do 2 activities more
			- the more activities, the more complex organization
			- ![[Pasted image 20260408112229.png]]
				- here, there are only capability levels 2, 3 possible (not too complex for 4, 5 and 1 would be incomplete)
	- This is ~80% of the content of the COBIT
- Organizational structures
	- ![[Pasted image 20260408112512.png]]
	- this component is a matrix of (A) accountable roles and (R) responsible roles
	- there must be only one accountable person and one to many responsible people to each practice (and each practice then has activities (as seen above))
- Information flows
	- ![[Pasted image 20260408112901.png]]
	- every practice could have inputs and outputs - and this forms a network of practices and defines how they depend on each other
- People, skills and competencies
	- ![[Pasted image 20260408113035.png]]
	- there are people working in the company that will interact with the objective and those people have to have the right skills
		- this provides with the guidance, where to teach those people the skills
- Principles, policies and procedures
	- ![[Pasted image 20260408113226.png]]
	- for each objective has written rules describing how things should happen
- Culture, ethics and behavior
	- ![[Pasted image 20260408113321.png]]
		- the human/cultural aspects that are needed for this objective (e.g., "leaders must create a culture of continuous improvement")
- Services, infrastructure and applications
	- The technical enablers (tools, platforms, etc.)

These were called "enablers" in COBIT 5 - same concept, renamed to "components."
#### Generic vs. Variant Components
- **Generic components** come from the COBIT core model - applicable to any organization in principle (those described above)
- **Variants** are customizations of those components for specific contexts (e.g., security-specific organizational structures, DevOps-specific processes)
	- they build on top of the generic ones

When there are enough variants for a topic, they get bundled into a **Focus Area** (like the Information Security Focus Area or the I&T Risk Focus Area).
- so a focus area is essentially a collection of generic components and their specialized variants for the topic of the focus area
- so if the enterprise says that they want apply the focus area (e.g. I&T Risk, DevOps ...), they have to apply all components in that focus area 
### Key Concepts: Design Factors
Design factors are the parameters you use to customize COBIT to your organization. There are 11 of them:
- Enterprise strategy
   - What you decide: What's your primary strategic focus?
   - Options:
      - Growth/Acquisition - focus on growing/revenue
      - Innovation/Differentiation - innovative product/services for clients
      - Cost Leadership - short-term cost minimization
      - Client Service/Stability - provide a stable and client-oriented service
  - Only one or max. two strategies can be chosen
  - There is a mapping table, which maps the strategies onto priorities in different governance/management objectives:
	  - ![[Pasted image 20260408124054.png]]

- Enterprise goals
   - What you decide: Which enterprise goals matter most?
   - Options:
      - 13 goals organized by BSC dimensions (Financial, Customer, Internal, Growth)
      - Those goals are in the goals cascade mapped onto Governance/Management objectives through the Primary and Secondary links (see [[#Key Concepts The Goals Cascade]])
	      - e.g. mapping of alignment goals onto enterprise goals (they support them)
	  - ![[Pasted image 20260409093121.png]]

- Risk profile
   - What you decide: Which IT risks exceed your appetite?
   - Options:
      - 12 risk categories with example scenarios (e.g., logical attacks, third-party incidents, IT cost overruns)
      - There is again a mapping matrix between governance/management objectives and the IT Risk factors/scenarios (to see, which objective can help to manage these risks on a scale from 0-4)
      - The company has to define it's risk appetite and define the biggest risks to which the current enterprise is exposed

- I&T-related issues
   - What you decide: What IT problems do you currently face?
   - Options:
      - Frustration between departments
      - Failed projects
      - Service delivery problems
      - Audit findings
  - Again, there is a table matrix of governance/management objectives, that maps which objectives could help to mitigate/solve the I&T issue categories

- Threat landscape
   - What you decide: How hostile is your environment?
   - Options:
      - Normal - the enterprise is operating under normal threat levels
      - High - the enterprise is operating in high-threat environment (e.g. geopolicital situation, high-threat industry sector etc.)

- Compliance requirements
   - What you decide: How regulated are you?
   - Options:
      - Low - compliance requirements are lower than average
      - Normal
      - High - compliance requirements are high due to industry-sector and geopolicital related reasons

- Role of IT
   - What you decide: How critical is IT to your business?
   - Options:
      - Support - not crucial for running and innovation
      - Factory - IT failing = big impact, but not the primary driver for innovation
      - Turnaround - IT mainly for innovation processes, not critical for running
      - Strategic - IT is critical for both running and innovation
  - This one deserves extra attention because it fundamentally changes what matters:
```
                    IT critical for RUNNING business?
                         No              Yes
                   ┌──────────────┬──────────────┐
 IT critical for   │              │              │
 INNOVATING     No │   SUPPORT    │   FACTORY    │
 business?         │  (IT is nice │ (If IT fails,│
                   │   to have)   │  biz stops)  │
                   ├──────────────┼──────────────┤
                Yes│  TURNAROUND  │  STRATEGIC   │
                   │ (IT drives   │ (IT is core  │
                   │  innovation) │ to everything│
                   └──────────────┴──────────────┘
```
- a bank where IT is "Strategic" will have very different governance priorities than a bakery where IT is "Support."

- Sourcing model
   - What you decide: Where does IT come from?
   - Options:
      - Outsourcing
      - Cloud - maximizing cloud services
      - Insourced
      - Hybrid - combining all three above

- Implementation methods
   - What you decide: How do you build software?
   - Options:
      - Agile
      - DevOps
      - Traditional - using e.g. Waterfall methods and separating software development and operations
      - Hybrid

- Technology adoption strategy
   - What you decide: How fast do you adopt new tech?
   - Options:
      - First mover - trying to gain a first-mover advantage, very flexible
      - Follower - adopting tech when becoming mainstream
      - Slow adopter - very late in adoption of new tech

- Enterprise size
   - What you decide: How big are you?
   - Options:
      - Large (>250 FTE)
      - Small/Medium (50-250 FTE)
      - FTE = full-time employee

Each design factor has a **mapping table** that links it to the 40 governance/management objectives with importance scores (typically 0–4). This tells you: "Given your specific design factor values, here's how important each objective is for you."
- in practice, the higher importance translates into setting higher target capability levels
### The Governance System Design Workflow (4 Steps)
This is the practical process for building your tailored governance system:

```
Step 1                Step 2                Step 3                Step 4
UNDERSTAND     →    DETERMINE       →     REFINE          →    CONCLUDE
CONTEXT             INITIAL SCOPE         SCOPE                DESIGN

• Enterprise         • Apply design       • Apply remaining    • Resolve 
  strategy             factors 1-4          design factors       conflicts
• Enterprise           (quantitative        5-11               • Finalize
  goals                via mapping          (mostly              priorities
• Risk profile         tables)              qualitative)       • Set target
• I&T issues                                                     capability
                                                                 levels
```

**Step 1:** Understand your organization - pick your strategy archetype, identify key goals, do a high-level risk assessment, list current IT issues.

**Step 2:** Use the first 4 design factors (strategy, goals, risk profile, I&T issues) with the quantitative mapping tables to calculate initial importance scores for each of the 40 objectives.

**Step 3:** Apply the remaining design factors (5–11) - these are mostly qualitative adjustments that refine priorities and may trigger the need for specific focus areas.

**Step 4:** Resolve conflicts (different design factors may pull in different directions), involve all key stakeholders, and finalize the design. The output is (one plan for the whole enterprise):
- Prioritized governance and management objectives (out of the 40 objectives)
- Target capability levels for key processes (it cannot be level 5 everywhere, see [[#Critical Insight on Capability Levels]])
- Specific governance components needing attention
- Applicable focus area guidance

All key stakeholders must be included in the discussion on the design of the governance system (board and executives, management of the IT, risk and assurance management). Also the generic nature of COBIT needs to be considered, it points the company in some direction, it cannot be 100% tailored to the enterprise needs/nature, so be ready to be able to deviate from the plan (when it is justified)
#### Critical Insight on Capability Levels
- as in [[#What's Inside Each Box? (The 7 Components)]] - the first component (processes, activities)
Some companies (and regulations) claim you need capability level 5 for everything. **This does not make sense** for several reasons:
- For some processes, CL 5 isn't even defined in COBIT
- It's rarely cost-effective to run everything at CL 5
- It's practically impossible to implement CL 5 across all objectives in any reasonable timeframe

The smart approach: set a **baseline minimum** (e.g., CL 2–3) for most objectives, then selectively target CL 4–5 only for your most critical processes. The whole point of the design approach is to **differentiate** what's important from what's less important.
***
### 3.4 Performance Management
Performance management answers: "Is our governance system actually working?". The COBIT philosophy lies on the following principles:
- simple to understand and use
- consistent with the COBIT conceptual model and support it
- provide reliable, repeatable and relevant results
- be flexible
- support different types of assessments
#### Process Capability Levels (0–5)
COBIT uses a CMMI-based scheme to measure how mature a process is:

| Level | Name                | What it means                                                                              |
| ----- | ------------------- | ------------------------------------------------------------------------------------------ |
| **0** | Incomplete          | Basically nothing organized (very rare in practice - there's always _something_)           |
| **1** | Initial / Intuitive | Some activities happen, but ad-hoc and incomplete                                          |
| **2** | Performed           | Basic complete set of activities - the minimum viable process                              |
| **3** | Defined             | Well-organized, using formal organizational assets and standards. For important processes. |
| **4** | Measured            | Performance is quantitatively tracked - you have metrics. For critical processes.          |
| **5** | Optimizing          | Continuous improvement based on measurements (very rare - must be justified)               |

How activities map to levels: each activity in a process is tagged with a capability level. To achieve CL (capability level) 3, you need to do ALL activities up to and including level 3. To achieve CL 2, you need all activities tagged as level 2 and below.

Why not CL 1? Because level 1 means an incomplete process - necessary to exist, but not sufficient to be reliable.
#### Rating Achievement
How well a capability level is achieved can be expressed as:

```
Fully     = >85% achieved
Largely   = 50-85% achieved
Partially = 15-50% achieved
Not       = <15% achieved
```
Formal certifications use binary pass/fail. Internal assessments (used in performance-improvement contexts) typically use the range above.
#### Organizational Structure Maturity (from UA Research)
No official standard exists for assessing organizational structures, but University of Antwerp proposed a 4-level maturity model:

|Level|Name|Description|
|---|---|---|
|1|Initial|Structure exists with only the most essential attributes|
|2|Established|All essential and important attributes are present|
|3|Performing|Advanced attributes consistently applied|
|4|Measured|Performance is measured and adjustments are made|

Assessed across 5 dimensions:
- Lifecycle Management - the structure must exist and be useful for the performance of the enterprise
- Composition & Resources - the structure contains people with the right skills and necessary resources needed to operate
- Good Practices - have a set of good practices
- Decision-Making & Responsibilities - everybody makes decisions per their mandate, authority and responsibility
- Communication - effective communication with stakeholders

---
### 3.5 Information Quality Management
The "information" component of governance can be assessed using a quality model with 3 categories and 15 criteria to ensure the quality of the "information piece". When you need to evaluate whether a piece of information (e.g., a risk register) is good enough, run through these:
#### 1) Intrinsic Quality (is the data itself correct?)

|Criterion|Question to ask|
|---|---|
|Accuracy|Is it correct and reliable?|
|Objectivity|Is it unbiased and impartial?|
|Believability|Is it regarded as true and credible?|
|Reputation|Is the source highly regarded?|

#### 2) Contextual Quality (is it useful for the task?)

|Criterion|Question to ask|
|---|---|
|Relevancy|Is it applicable to the task at hand?|
|Completeness|Is anything missing? Sufficient depth and breadth?|
|Currency|Is it up to date?|
|Appropriate Amount|Too much or too little information?|
|Concise Representation|Is it compactly presented?|
|Consistent Representation|Same format throughout?|
|Interpretability|Are the languages, symbols, and definitions clear?|
|Understandability|Can it be easily comprehended?|
|Ease of Manipulation|Can it be applied to different tasks?|

#### 3) Security / Privacy / Accessibility (to which extent is the information available or obtainable?)

|Criterion|Question to ask|
|---|---|
|Availability|Can you get it when you need it?|
|Restricted Access|Is access properly limited to authorized people?|
Use case: when auditing an important information artifact like a risk register, you can systematically evaluate it against these 15 criteria to identify quality gaps (it is measurable).

---
### COBIT Product Family - What Exists

For reference, the publications relevant to this course:

```
COBIT 2019 Core:
├── Introduction and Methodology
├── Governance and Management Objectives (the "big book" with 40 boxes)
├── Design Guide (how to tailor your governance system)
└── Implementation Guide

COBIT 2019 Focus Areas:
├── Information Security
├── I&T Risk
└── DevOps (+ others)

Complementary Risk Guidance:
├── Risk IT Framework (2nd Edition)
└── Risk IT Practitioner Guide
```

The Focus Areas and Risk IT guides are especially important for this course - they contain the risk scenario guidance we'll use later.