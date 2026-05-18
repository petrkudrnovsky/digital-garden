# The big picture
The whole field rests on one separation that the source PDF assumes you already know: governance vs management. Think of it like this:
- Governance = the board deciding "how much risk are we okay with, and who's accountable when things go sideways?"
- Management = the operational team actually identifying, measuring, and responding to risk day-to-day.
COBIT 2019 (the framework being used) splits these into two objectives you'll see referenced constantly:
- EDM03 "Ensure risk optimisation" - the governance side
- APO12 "Managed risk" - the management side
Everything in chapters 5-7 is either about EDM03, APO12, or the inputs they need (like a risk taxonomy and risk scenarios). Hold onto that map.
# Chapter 5 - risk governance
### What governance means here
ISO 38500 defines governance as three verbs: Evaluate, Direct, Monitor. Apply that to IT risk and you get IT risk governance: the board evaluates where the company stands on risk, directs management on what to do, and monitors whether it's working. That's it. The rest is detail.
### EDM03 - ensure risk optimisation
The single most important concept in this whole chapter is risk appetite and risk tolerance. Before doing anything else, you must answer: "how much risk are we willing to take?" The entire purpose of risk management is to drag actual risk down below that appetite line.

EDM03 has three sub-practices, each broken into activities with a capability level attached. The first four activities of each are the essentials - everything beyond that is "nice to have."
EDM03.01 Evaluate risk management - figure out where you currently stand and what the plan is.

Key activities:
- Understand the organisation and its IT risk context
- Determine the risk appetite (how much risk you'll take pursuing objectives)
- Determine the risk tolerance (acceptable deviation from appetite)
- Make sure appetite stays below your actual risk capacity (more on these three terms in chapter 5.2)

EDM03.02 Direct risk management - tell the organisation how to act on the strategy. Translate strategy into practices, build communication plans, define escalation rules.

EDM03.03 Monitor risk management - measure whether the risk profile actually stays within appetite and tolerance, and report deviations.

The pattern (Evaluate, Direct, Monitor) repeats - it's just ISO 38500 applied at one level deeper.
### Who's accountable
At the governance level, the board is Accountable (capital A in the RACI sense). Everyone else - CEO, CRO, CISO, I&T governance board - is Responsible. Accountability cannot be delegated; responsibility can.
### Why this matters in practice
The lecturer notes most companies actually do this badly. Risk appetite usually isn't explicitly defined, and monitoring is weak. The question worth asking is whether companies skip it because it's unnecessary, or because they're bad at it. The implication: doing it properly is a real competitive advantage.
### Risk taxonomy - giving risk a language
Risk is a function of frequency and impact: `Risk = f(Frequency, Impact)`. A risk taxonomy is just the vocabulary you use to express both dimensions. Without one, statements like "our compliance level is low" trigger a "so what?" response from management, because they can't tell what the business impact is.
###### Quantitative vs qualitative
- Quantitative methods need reliable historical data and a cause/effect model. In IT, you usually don't have either - but modern Bayesian and Monte Carlo approaches work with estimated ranges instead, so quantitative analysis is becoming more feasible.
- Qualitative methods are easier and cheaper but more subjective.
###### Frequentist vs Bayesian (a genuinely important distinction)
Frequentist view treats risk as an objective property of the world. Probabilities come from historical data on past events. Problems:
- Slow to adapt - if you base your estimates on a 20-year-old database, your model is wrong
- Controls stay unchanged until they fail (i.e. too late)
- Useless when there's little historical data
Bayesian view treats risk as partly a judgement of the observer. You combine historical data with expert judgement and update as new information arrives. This handles changing circumstances, works without long historical records, and is rigorous if you control for cognitive bias.
For IT risk specifically, Bayesian is usually the better fit - threats and technologies change too fast for pure frequentist analysis to keep up.
###### The simple L/M/H matrix
The familiar 3x3 Low/Medium/High impact-vs-likelihood grid is intuitive but flawed:
- Subjective - everyone's idea of "Medium" differs
- Poor discernment - can't tell which Medium risks actually need attention
- Bias toward the middle - people avoid picking extremes
Its only virtue is simplicity. Don't rely on it for serious work.
###### Business impact criteria
You need to pick what kinds of impact matter to your organisation. Two example sets:
- ISACA Risk IT: financial (share value, profit, revenue, cost of capital), client (market share, customer satisfaction), internal (regulatory compliance), growth (competitive advantage, reputation)
- FAIR: productivity, cost of response, replacement cost, competitive advantage, fines & judgements, reputation
You don't use all of these - pick a handful that reflect your strategic priorities. The usage pattern is always: "If `<event>` happened, the impact on `<criterion>` would be `<value>`."
###### Building a taxonomy in practice (the exercise pattern)
Two-step process:
1. Identify strategic priorities. For a hospital: patient safety, healthcare quality, resource efficiency. For an automotive manufacturer: innovation, customer satisfaction, operational quality. For a parliament: MP satisfaction, budget performance, reputation.
2. Translate those into measurable impact criteria with rating scales. Aim for an even number of rating levels (0-5, say) so people are forced to commit to a side instead of parking on the middle.
Each rating level should be tied to a different mitigation action, so you're already thinking about response when you build the scale.
### Risk appetite, tolerance, capacity
Three terms that get confused constantly:
- Risk appetite - how much risk you want to take in pursuit of your mission. A board decision.
- Risk tolerance - acceptable variation around appetite for a specific risk type.
- Risk capacity - how much risk you could actually absorb without dying. An objective fact about the business, not a preference.
Appetite < Capacity always. If appetite ever exceeds capacity, the company is gambling with its existence.
You can express appetite in two ways:
- Risk-objective view: as unacceptable combinations of impact/likelihood, usually qualitative
- Asset view: as a monetary amount of acceptable loss
Tolerance often varies by risk type. Financial loss is often tolerated more than reputation loss, for example.
### The risk map
Plot every risk scenario on a 2D grid of frequency vs impact. Colour the zones:
- Red - unacceptable, must be addressed
- Yellow - elevated, not happy but not catastrophic
- Green - normal, risk exists but it's acceptable
- Black - black swan territory (high impact, extremely rare)
Defining the colour zones is how you operationalise risk appetite. A sanity check: if all your risks land in the red zone, your criteria are calibrated wrong - the scale doesn't match the actual range of impacts.
A second sanity check: red-zone risks shouldn't sit there for years. If they do, either they aren't actually red, or your organisation has accepted unacceptable risk and is in denial.
***
# Chapter 6 - risk management
This is APO12 "Managed risk" - the operational counterpart to EDM03. Where EDM03 was about deciding how much risk is okay, APO12 is about actually finding, measuring, and dealing with it.
### APO12 has six practices
Each one feeds the next. Think of it as a pipeline:

APO12.01 Collect data - gather the raw material. Build a method for collecting and classifying IT risk data. Capture internal incidents, external industry events, and contributing factors. Adopt a risk taxonomy (the one from chapter 5) so everything's classified consistently.

APO12.02 Analyse risk - turn data into decisions. Define risk scenarios, estimate frequency and impact, compare current risk to appetite/tolerance, propose responses. Activity 8 (analysing cost/benefit of response options: avoid, reduce, transfer, accept) is a capability level 5 activity - the most mature.

APO12.03 Maintain a risk profile - keep a running inventory. Document which IT services and infrastructure resources are critical, find weak links, aggregate scenarios by business line, capture the status of action plans. The risk profile is the single source of truth about what risks exist and what state they're in.

APO12.04 Articulate risk - communicate it to stakeholders. Report results in formats decision-makers can use. Probabilities, ranges, confidence levels where possible. Also: identify upside opportunities, not just downsides.

APO12.05 Define a risk management action portfolio - decide what to actually do. Maintain an inventory of controls. Define a balanced set of projects to reduce risk and enable strategic opportunities.

APO12.06 Respond to risk - execute when something happens or is about to. Maintain response plans, apply them when events occur, categorise incidents against tolerance thresholds, do root cause analysis on past incidents and near-misses.
### Accountability shifts
For governance (EDM03), the board was Accountable. For management (APO12), accountability moves to the Chief Risk Officer. The board still cares but isn't running the process.
### Capability levels - measuring how well a process runs
COBIT 2019 uses a CMMI-style 0-5 capability scale for each process. Quick translation:
- 0 - no basic capability, process barely exists
- 1 - initial, intuitive, disorganised - it kind of happens
- 2 - basic but complete set of activities, performed reliably
- 3 - well-defined, organised, using shared organisational assets
- 4 - quantitatively measured for performance
- 5 - continuously improved based on measurement
Each activity inside a practice is tagged with the capability level it represents. To reach level 3, you need everything at levels 2 and 3 in place. Less formal assessments rate achievement as Fully (>85%), Largely (50-85%), Partially (15-50%), Not (<15%).
The point of capability levels: they tell you what to work on next. If you're at level 2 on APO12.02, look at the level 3 activities to see how to mature further.
# Chapter 7 - risk identification
This is where you actually figure out what risks to put on the map. The mechanism is the risk scenario.
### What a risk scenario looks like (COBIT 2019)
A risk scenario describes a potential loss event using six components:
- Actor / threat community - who triggers it? Internal (staff, contractors, privileged vs non-privileged) or external (outsiders, competitors, regulators). Not all threats have an actor - natural disasters and process failures don't.
- Intent / motivation - malicious, accidental, or natural?
- Threat event - what happens? Disclosure, interruption, theft, destruction, ineffective execution of a process, regulatory change, etc.
- Asset / resource - what's affected? People, organisational structures, physical infrastructure, IT infrastructure, applications, information. Critical assets matter more and attract more threats, so distinguishing critical from non-critical is essential.
- Effect - the outcome, usually negative.
- Time - duration of the event, timing (does it hit at a bad moment?), detection speed, lag between event and impact.
### Loss events vs threat events vs vulnerability events
A critical distinction the source emphasises:
- Loss event - the risk actually materialises. Money was lost, data was leaked, the system went down.
- Threat event - something happens that could trigger a loss event. An attacker probes your network.
- Vulnerability event - the underlying weakness changes. A control is weakened, a new vulnerability is discovered.
Weak passwords are a vulnerability, not a risk by themselves. The risk is "attacker compromises customer database by guessing weak passwords." Don't conflate these three types of events into one undifferentiated list - they behave differently and need different responses.
### The 18 COBIT risk scenario categories
COBIT publishes a list of ~100 generic risk scenarios grouped into 18 categories. Use this as a starting checklist when you're identifying risks from scratch. Categories include investment decision-making, project lifecycle management, IT cost & oversight, expertise/skills/behaviour, enterprise architecture, IT operations, user access rights, software adoption, IT hardware, internal/external security threats, third-party incidents, non-compliance, geopolitical, industrial action, acts of nature, emerging technologies, environmental, data & information management.
Important lecturer note: risk and security overlap but are not the same. Roughly a third of these categories are security-focused, a third are security-adjacent, and a third are non-security. A risk management programme that only looks at security misses half the picture.
### FAIR risk scenarios - the alternative formulation
FAIR (Factor Analysis of Information Risk) defines a scenario with four elements during "scoping":
- Asset
- Threat
- Effect
- Method / vector (this is the big difference vs COBIT - FAIR forces you to name how the threat reaches the asset, which makes the scenario quantifiable)
Useful trick: always phrase a scenario as "Analyse the risk from `<actor>` `<doing what>` to `<asset>` via `<method>` causing `<effect>`."
Examples from the source:
- "Analyse the risk from malicious insiders attacking our order processing system" - BAD. Effect and method are missing.
- "Analyse the risk we face from supply chain disruption" - BAD. Threat and method are missing.
- "Analyse the risk the university's learning support system is unavailable for more than 48 hours because of a successful ransomware attack by organised criminals" - GOOD. Every component is named.
### Suitable and relevant
Two qualities a scenario needs:
- Suitable - concrete enough to actually analyse
- Relevant - describes a risk that actually matters to this organisation
"Information integrity is compromised by an IT operational incident" is probably relevant for most organisations but isn't suitable - it's so vague you can't analyse it.
Number of scenarios in a portfolio: typically more than 5, fewer than 100. Below 5 means you're missing things; above 100 means you can't manage it.
### The nine guidance principles for working with scenarios
Distilled into the actual practical advice:
1. Keep scenarios current. Review at least annually, more often if the environment changes fast. Old scenarios stop being relevant as tech and the business evolve.
2. Start generic, refine when needed. Use the 18 COBIT categories as a base structure. Only build detailed sub-scenarios where the risk profile demands it.
3. Make the number of scenarios reflect reality. Enough to cover your real exposure, few enough to be manageable. Risk management exists to reduce complexity, not generate it.
4. Use the generic structure for reporting. Analyse at detailed level, report at aggregated level. Decision-makers don't want 47 sub-scenarios on project quality - they want one.
5. Use scenario-building to get organisational buy-in. Don't have one risk analyst do it in isolation. Workshop the scenarios with business lines, IT, finance, compliance. The discussion itself raises awareness.
6. Involve the first line of defence. The people doing the actual operational work know the real vulnerabilities better than anyone at the top. Don't skip them. Also: don't focus only on rare worst-case events - frequent moderate incidents are usually where the real damage accumulates.
7. Build complex scenarios from simple ones. Once you have basic scenarios, combine them to show cascading effects. A major hardware failure plus a failed disaster recovery plan is qualitatively worse than either alone.
8. Consider systemic and contagious risk. Systemic = something happens at a major shared partner that hits many companies at once (the nationwide air traffic control going down). Contagious = events ripple through your business partners in a short timeframe (the clearinghouse with no transactions to clear).
9. Use the building process to improve detectability. Asking "would we even notice if this scenario happened?" is one of the most valuable questions you can raise. Detectability has two layers: visibility (can we observe the event?) and recognition (can we identify it as a problem when we see it?).
# How the pieces fit together
Pulling everything back together so the structure sticks:
1. The board sets risk appetite, tolerance, and accepts capacity as a hard constraint (EDM03)
2. The CRO and team build a risk taxonomy with impact criteria and rating scales calibrated to the business (chapter 5.1)
3. They identify risk scenarios using the COBIT 18 categories and/or FAIR scoping, ensuring each is suitable and relevant (chapter 7)
4. They analyse each scenario for frequency and impact, plot them on the risk map, compare to appetite (APO12.01-02)
5. They maintain the risk profile, articulate risk to stakeholders, define a response portfolio, and respond when events occur (APO12.03-06)
6. The board monitors all of this and adjusts direction as needed (EDM03.03)
The whole chain only works if the first step is solid. If risk appetite is undefined or fuzzy, everything downstream is just busywork.
# Things worth remembering for the exam
- Governance = Evaluate + Direct + Monitor (ISO 38500). Applied to IT risk = EDM03.
- Management = APO12. Six practices: Collect, Analyse, Maintain profile, Articulate, Define portfolio, Respond.
- Risk = f(Frequency, Impact). Risk taxonomy is the vocabulary for both.
- Risk appetite (want) < Risk capacity (can survive). Tolerance = acceptable wiggle room.
- Frequentist vs Bayesian: historical data vs judgement + updating. Bayesian wins in fast-moving environments.
- Loss event ≠ threat event ≠ vulnerability event. Weak passwords are a vulnerability, not a risk.
- COBIT scenario components: Actor, Intent, Threat event, Asset, Effect, Time.
- FAIR adds Method/Vector to make scenarios quantifiable.
- Scenarios must be Suitable and Relevant. Aim for 5-100 in your portfolio.
- The board is Accountable for governance (EDM03), the CRO for management (APO12).
- Capability levels are 0-5, CMMI-style. Each activity tagged with its level.