# The big picture
The four moves you can make when a risk exceeds appetite are sometimes called the "four T's" or "four A's" depending on the framework. COBIT uses:
- Avoid - exit the activity that creates the risk
- Accept - acknowledge the risk and consciously do nothing
- Share / transfer - push some of the financial consequence onto another party
- Mitigate - reduce frequency or impact by adding controls

The flow from analysis to action is: risk analysis produces a risk map → identify risks exceeding appetite → for each one, select a response based on a set of parameters (effectiveness, efficiency, exposure, implementation capability) → prioritise the responses → produce a risk action plan.
There is no fifth option. "Ignore" is not on the list. If you don't decide on one of the four, you've defaulted to acceptance without thinking, which is the worst version of acceptance.
# Risk response options
### Avoid
Exit the activity or conditions that create the risk. The nuclear option. Use it only when nothing else works:
- No cost-effective response can drag frequency or impact below appetite
- The risk cannot be shared or transferred
- Management cannot accept the exposure
IT examples: relocating a data centre out of a flood plain or earthquake zone; declining to take on a project whose business case shows a serious risk of failure.
### Accept
Recognise the exposure, decide consciously to do nothing about it. The course is emphatic about one distinction: acceptance is not the same as being unaware. Acceptance is an informed decision made by qualified people, usually business management with IT support, against pre-established acceptance authority levels.
Two situations where acceptance is the right call:
- A project might miss its deadline but management decides to proceed anyway because the business value of moving fast outweighs the risk
- A risk is extremely rare but catastrophic, and the cost of reducing it would be prohibitive
The course flags that people often shy away from acceptance because it feels irresponsible. It isn't - sometimes acceptance is the most rational choice. What matters is that the right people make it, with full visibility, and document the decision.
### Share / transfer
Reduce frequency or impact by handing part of the risk to someone else. Two main techniques:
- Insurance - pay a third party to bear the financial consequence if the loss materialises
- Outsourcing - delegate an activity to a provider, transferring some of the operational risk to them (fixed-price contracts and shared-investment arrangements are variations)
The critical caveat: you transfer financial consequence, you do not transfer risk ownership. If your outsourced data processor leaks customer data, you - not them - are still on the hook with regulators and customers. The same applies to insurance: the cheque doesn't undo the reputational damage.
### Mitigate
Add controls to reduce frequency, impact, or both. This is the most common response by far, because most risks aren't extreme enough to demand avoidance and aren't acceptable as-is.
Two main mechanisms:
- Strengthen overall IT risk management practices (process maturity)
- Introduce specific control measures
In COBIT 2019, "controls" means the 40 governance and management objectives themselves. Each generic risk scenario maps to a subset of these objectives that, if properly implemented, will mitigate that scenario.
### Ignore is not on the list
"Ignore" is what happens when nobody makes a decision. It's not a fifth response option, it's the absence of one. If your organisation's apparent response to a risk is "we're not doing anything about it," check whether that's a deliberate Accept (someone qualified made the call, documented the rationale, owns the consequences) or an Ignore (nobody looked, nobody decided, nobody's accountable). The first is fine. The second is governance failure.
# Risk response parameters - how to choose
Picking the right response isn't intuition. COBIT names four parameters to weigh:
- Effectiveness - does this response actually work? Will it reduce the risk?
- Efficiency - is the cost worth the reduction? What's the benefit-to-cost ratio?
- Exposure - how far above appetite is the current risk? More exposure justifies more aggressive (and more expensive) responses
- Response implementation capability - can the organisation actually execute the response? A theoretically perfect response that exceeds your organisation's capability isn't usable
### The effectiveness / efficiency split (worth internalising)
These two are constantly confused. The distinction:
- Effective = it works. The response actually reduces the risk to (or below) appetite.
- Efficient = it works without burning unjustifiable amounts of resource. Worth the cost.
A response can be effective without being efficient (works, but ruinously expensive), or efficient without being effective (cheap, but doesn't actually fix the problem). Good responses are both.
### Worked examples
Ransomware attack making the client database inaccessible:
- Avoid: shut down the client application entirely. Very effective (no app, no attack), terribly inefficient (no app, no revenue)
- Accept: leaves the high exposure in place, not effective
- Mitigate (backups, segmentation, monitoring): typically effective and efficient
- Share: cyber insurance helps with the financial impact but not with the operational disruption
Earthquake destroying hospital IT in Limburg:
- Avoid: not realistic - the hospital has to exist somewhere
- Mitigate by building a backup site: effective but expensive, so not very efficient
- Accept: zero cost (very efficient) but limited effectiveness if it actually happens
- Transfer (insurance): probably the best balance here, since the event is rare but catastrophic and money is the main loss vector
The pattern: the right response depends on the shape of the risk. Rare-but-catastrophic favours transfer. Frequent-but-moderate favours mitigation. Existential and unmanageable favours avoidance.
# Mitigation in detail - how controls actually work
This is where COBIT becomes a practical tool rather than a vocabulary. The framework explicitly maps generic risk scenarios to the governance and management objectives that mitigate them.
### The mapping table
COBIT 2019 Design Guide includes Mapping Table D, which scores each governance/management objective (rows) against each generic risk category (columns) on a 0-4 scale:
- 4 = critical for mitigating this risk
- 3 = strongly relevant
- ... down to ...
- 0 = irrelevant
For any given risk scenario, you look up which objectives score 3 or 4 and those become your candidate control set.
### Why this is double-useful
Knowing the risk-to-control mapping helps you in two directions:
- During risk response - which controls should we strengthen to mitigate this risk?
- During risk analysis - which controls should we look at to estimate how severe this risk currently is? If those controls are weak, frequency and impact go up
The lecturer's important point: always use the mapping table (or an equivalent for whatever framework you're working in). It anchors your analysis in established practice. If your organisation is only implementing half the controls a mature framework would expect, your risk analysis and your response will both be incomplete in predictable ways. The mapping is a starting point, not a recipe.
### Worked example - unauthorised access to ICU laptop
Scenario: an unprotected laptop in an Intensive Care Unit was used by unknown visitors to tamper with patient health data, temporarily endangering the patient's life without permanent damage.
Looking up the relevant COBIT objectives via the mapping table, the key controls are:
- APO01 - Managed I&T Management Framework
- APO13 - Managed Security
- BAI06 - Managed IT Changes
- BAI10 - Managed Configuration
- DSS05 - Managed Security Services
- DSS06 - Managed Business Process Controls
Then you assess each control's current capability level at the organisation:
- APO01: CL 0
- APO13: CL 0.5
- BAI06: CL 0
- BAI10: unknown
- DSS05: CL 1
- DSS06: unknown
- Policies: largely absent
- Culture: not addressed
The risk response then becomes concrete:
- Create acceptable use policies
- Improve awareness (training)
- Raise APO13 capability level to 2
- Raise DSS06 capability level to 2 or 3
Notice the structure: the response isn't "do better security." It's a specific list of process capability targets and policy gaps to close. This is what makes it possible to write a business case, plan the work, and measure progress.
# Building a portfolio of mitigation actions
### Risks and controls have a many-to-many relationship
One risk scenario is usually mitigated by multiple controls. One control usually mitigates multiple risk scenarios. This many-to-many structure is the key to building a cost-effective response portfolio:
- If two different scenarios both need APO13 (Managed Security) at a higher capability level, you fund APO13 once and both scenarios benefit
- Controls that appear as 3 or 4 across multiple scenarios are the highest-leverage investments
### Worked example - two scenarios, one portfolio
Scenarios needing mitigation:
- IT Infrastructure incident (operator error) - risk category 6
- Unauthorised action (tampering with software) - risk category 7
Walking the mapping table and pulling all objectives that score 3 or 4 against either scenario, you get two tiers:
- Common controls (high relevance for both scenarios): APO01, APO13, BAI06, BAI10, DSS01, DSS04, DSS05, DSS06, MEA02
- Additional controls (high relevance for one of the two): APO07, BAI07, BAI09, DSS02, DSS03, MEA01
The common controls go first - they give you "double leverage," addressing multiple risks at once. The additional controls fill the gaps.
### The 30-scenario problem
If you mitigated 30 scenarios this way, the union of "all controls with score 3 or 4 across any scenario" would approach the full COBIT catalogue. At that point you've stopped doing risk-driven prioritisation and started doing generic IT improvement.
The lesson: as your scenario list grows, you have to be more selective about which controls actually move the needle for each scenario. Otherwise the portfolio becomes "implement all of COBIT," which is technically a valid plan but not a manageable one. Risk management exists to prioritise; if it stops doing that, it's not working.
# The business case for risk response
Any mitigation initiative needs a proper business case. The course expects you to know what one looks like:
- Title
- Risk scenarios addressed (which specific scenarios from the analysis does this fix?)
- Detailed description of actions - reference all relevant governance component types (processes, policies, organisational structures, culture, people, etc.)
- Benefits - ideally expressed in the same terms as your impact criteria, so they're directly comparable to the risk being avoided. The core benefit of risk management is avoidance or reduction of loss
- Cost and resource requirements (Capex and Opex) - headcount, hardware, software, services
- Stakeholders - who's accountable and who's responsible
- Planning - timeline
- Risks - yes, your risk mitigation project itself has risks
- Critical success factors
- Dependencies and challenges
- Metrics, indicators, and targets - how you'll know it worked
### Prioritisation
The risk action plan ranks initiatives by benefit-cost ratio. The 2x2 matrix:
- High benefit, low cost - do first (high priority)
- Low benefit, low cost - normal priority
- High benefit, high cost - normal priority (the big projects)
- Low benefit, high cost - last or never (low priority)
This is the same prioritisation logic you'd apply to any project portfolio. Nothing risk-specific about it - which is itself the point. Risk responses compete with other investments for the same budget.
# Risk reporting and communication
### Why this gets a whole section
The course frames risk communication as equal in importance to risk analysis. The reason is structural: even a perfect risk analysis is worthless if decision-makers don't see it, don't understand it, or see it too late to act on. Communication is the link between knowing about a risk and doing something about it.
### Benefits of good communication
- Common understanding of actual exposure and impacts, enabling informed response decisions
- Transparency about exposure and capability, which builds stakeholder confidence
### Drawbacks of poor communication
- False sense of confidence (or false alarms going the other way)
- No clear top-down direction on what's acceptable risk
- Stakeholders can't tell where they actually stand
- Perception that the enterprise is hiding risk from regulators, investors, or clients
- Inability to respond in time when issues emerge
- Reputational damage when management is held accountable but visibly fails to act
### Three components of effective risk communication
Worth memorising as a triad - the source treats this as the structure of any risk communication strategy:
- Expectations - strategy, policies, procedures, awareness, training. What does the enterprise expect from people regarding risk? This sets the tone.
- Capability - how mature is our risk management process? How well are we actually managing risk? Are there gaps?
- Status - what's our current risk profile, what KRIs are flashing, what loss events happened recently, what's the root cause analysis showing, what mitigation options exist?
A good risk communication programme covers all three. Missing any one of them leaves stakeholders with an incomplete picture.
### Quality requirements for risk reporting
Risk information should be:
- Clear, concise, complete, accurate, timely, understandable
- Free of jargon and unexplained technical terms (especially important for security and cyber risk, where the natural failure mode is "the security team explained it perfectly to other security people")
- Not buried in excessive detail - too much information hides the signal as effectively as too little
- Timely - communication is timely when it allows action at the moments where action would still help. A correctly identified risk reported after the loss event is just an autopsy
- Adapted to the audience - the security officer needs technical intrusion data, the steering committee needs aggregated policy implications. Same underlying risk, different views
- Available when needed
- Not always formal - face-to-face conversations are valid communication. The lecturer notes that auditors hate this because they want documented evidence, but it's still valid
- Aggregated without hiding actionable detail - showing "overall average risk is medium" must not hide one scenario that's actually high exposure
- Distributed on a need-to-know basis - the risk register isn't public, because it's effectively a map of your vulnerabilities for any attacker who gets hold of it. Transparency to stakeholders, not naïveté to the world
### Stakeholders
Risk communication has a wide audience:
- External - regulators, investors, insurers, external auditors
- Internal - executive management and board, CRO and risk committee, CIO, CFO, business management and process owners, IT management (including security and service management), compliance and audit, HR, employees
Different audiences need different views of the same risk. Designing the reporting cascade is part of the risk communication strategy.
# Risk-related information items
The course covers a few specific artefacts worth knowing:
### The risk profile
A dashboard-style view of the enterprise's risk portfolio. Includes:
- Risk register - the catalogue of risk scenarios, their analyses, and where they sit on the risk map. Best maintained in three views: previous (N-1), current (N), envisaged (N+1). The three-period view shows whether you're improving or sliding
- Risk action plan - the projects responding to risks, with status, owner, and effect on risk
- Loss data - actual realised incidents
- Findings from independent assessments - audits, consulting engagements
- Risk factors - internal and external context, plus IT capabilities
### Risk factors (recap from chapter 8)
The four buckets that drive risk likelihood and impact:
- External context (market, regulation, threat landscape, technology evolution, geopolitics)
- Internal context (goals, IT complexity, change capability, culture, financial capacity)
- Risk management capabilities
- IT-related capabilities (the COBIT process domains: EDM, APO, BAI, DSS, MEA)
These appear in chapter 8 and again here because they're equally relevant during analysis (estimating frequency and impact) and during communication (explaining the context behind a reported number).
# Key Risk Indicators (KRIs)
### What they are
Metrics that report on the state of a risk. The point is to track whether a risk is being controlled, and ideally to warn before a loss event happens rather than after.
Any metric that describes a risk is a risk indicator. A Key Risk Indicator is one that's specifically chosen for high relevance and high predictive power. KRIs are the subset of all possible risk metrics that are actually worth tracking.
### Lead vs lag indicators
A genuinely important distinction:
- Lead indicators - data or capabilities that predict a future event. Stronger controls (lead) means lower probability of exposure (future event). They have upper and lower limits to flag when attention is needed before the risk materialises.
- Lag indicators - measured after an event. Did we hit our SLA target? Did the control work? Useful for root-cause analysis and improvement, but they tell you about the past.
You need both. Lag indicators tell you what actually happened so you can learn. Lead indicators tell you what's coming so you can prevent it. A KRI portfolio leaning only on lag indicators means you only ever find out about risk after the loss.
### Selection criteria for KRIs
What makes a metric "key" rather than just "an indicator":
- Impact - indicators for high-business-impact risks are more likely to be KRIs
- Effort to implement, measure, report - given equivalent sensitivity, prefer the metric that's easier to capture
- Reliability - the indicator must correlate strongly with the risk; it should genuinely predict (lead) or measure (lag) the thing
- Sensitivity - the indicator must respond to actual changes in the risk, not stay flat or jiggle randomly
### Benefits of a good KRI set
- Early warning - lead indicators flag risk before it becomes a loss
- Historical context - lag indicators support trend analysis and inform future responses
- Feedback on risk appetite - actual KRI behaviour reveals whether your appetite and tolerance levels are set correctly
### Common pitfalls with KRIs
This list is exam-relevant because it's basically a checklist of "what bad KRI programmes look like":
- No clear measurement objective - tracking metrics with no question they're meant to answer
- Repetitive collection of easy-to-obtain data instead of data that actually correlates with risk (i.e. measuring what's available rather than what matters)
- No clear logical link between the KRI and a specific risk or business objective
- Too many metrics, no clear purpose
- Aggregation processes that are too cumbersome to maintain
- Excess complexity in synthesising results at the enterprise level
The lecturer's blunt summary: don't measure because you can. Measure because the metric is actually related to the thing you want to follow up. Don't overdo it - cost / benefit applies to measurement too.
The risk environment changes, so the KRI set should be revisited regularly. Each KRI should be linked to risk appetite and tolerance, so trigger levels are defined and someone knows what to do when the metric crosses them.
### Sources of KRIs
KRIs can be drawn from:
- Metrics related to achievement of governance/management objectives (process goals, organisational structure goals)
- Metrics related to application of good practice for governance components (e.g. how well process practices are followed)
- Combined metrics, such as process capability levels
### Example KRI set for the ICU laptop scenario
The course shows what a worked-out KRI set looks like for the unauthorised access scenario:
Backward-looking (lag):
- Patient safety incidents - easy to capture, high reliability
- Percentage of staff that successfully attended security awareness training (DSS05) - easy, medium reliability
- Percentage of users with need-to-have access rights (DSS06) - medium effort, medium reliability
Forward-looking (lead) related to process quality:
- Process capability level of Managed Security (APO13) - easy, high reliability
- Process capability level of Managed Security Services (DSS05) - easy, medium reliability
- Process capability level of Managed Business Process Controls (DSS06) - easy, high reliability
Forward-looking (lead) related to other governance components:
- Success rate of social engineering tests - medium effort, high reliability
- Awareness rate of applicable policies and SOPs - medium effort, high reliability
Notice that this set has both lead and lag indicators, draws from multiple control processes, and includes some that test culture (social engineering, policy awareness) not just process. That breadth is what makes it a balanced KRI portfolio rather than a collection of metrics.
# How the pieces fit together
End-to-end flow connecting back to everything from chapters 5-9:
1. Board sets risk appetite and tolerance (chapter 5)
2. Risk scenarios are identified using COBIT 18 categories or FAIR scoping (chapter 7)
3. Each scenario is analysed qualitatively or with FAIR (chapter 8)
4. Aggregated risk view is compared to appetite (chapter 8)
5. For risks exceeding appetite, a response is chosen: Avoid, Accept, Share, or Mitigate, weighing effectiveness, efficiency, exposure, and implementation capability (this chapter)
6. For mitigations, the COBIT risk-to-control mapping identifies which governance/management objectives to strengthen
7. Responses are bundled into a portfolio, prioritised by benefit-cost ratio, written up as business cases
8. KRIs are defined to monitor whether responses are working and to provide early warning of new exposure
9. Risk profile, capability, and expectations are communicated to all relevant stakeholders, formally and informally, at the right level of detail
10. The whole loop runs continuously - environments change, scenarios go stale, KRIs need refresh, appetite gets revisited
The course covers each step of this loop in roughly one chapter. The exam will test whether you can identify which step a given situation is asking about, and apply the right tools to it.
# Things worth remembering for the exam
- Four response options: Avoid, Accept, Share/Transfer, Mitigate. "Ignore" is not on the list
- Accept is not unaware - it's an informed, documented, authorised decision
- Share/transfer moves financial consequence, not ownership. You're still on the hook
- Mitigate is the most common response, implemented through COBIT governance/management objectives as controls
- Four response parameters: Effectiveness (does it work?), Efficiency (worth the cost?), Exposure (how far above appetite?), Implementation capability (can we actually do it?)
- Effectiveness ≠ Efficiency. Memorise the difference.
- COBIT Mapping Table D links scenarios to controls on a 0-4 scale. Always use it as a starting point
- Risks and controls are many-to-many. High-leverage controls mitigate multiple scenarios at once
- Business case must include risk scenarios addressed, benefits in impact-criteria terms, costs (Capex/Opex), stakeholders, planning, dependencies, KPIs
- Three components of risk communication: Expectations, Capability, Status
- Risk reporting quality: clear, concise, complete, accurate, timely, understandable. No jargon. Adapted to audience.
- Need-to-know basis for distribution. Risk register is not public.
- Face-to-face is valid communication (despite what auditors think)
- Lead indicators predict future events. Lag indicators measure past events. Need both.
- KRI selection criteria: Impact, Effort, Reliability, Sensitivity
- KRI pitfalls: measuring what's easy rather than what matters, no link to objectives, too many metrics, no triggers
- KRI sources: governance/management objective metrics, good-practice application metrics, combined metrics like capability levels