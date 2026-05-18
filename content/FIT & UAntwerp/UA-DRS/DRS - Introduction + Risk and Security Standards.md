# 1. What Is Risk?
Everyone defines risk slightly differently, but the core idea is simple:
- **Risk = uncertainty about bad (or good) things that might happen.**

Specifically, you don't know:
- **What** will happen
- **When** it will happen
- **How bad** (or good) the impact will be

The catch? You can't just avoid everything risky. Organizations have missions to accomplish - they have to move forward despite uncertainty. Risk management is about navigating that uncertainty intelligently, not eliminating it.
### Key properties of risk
**Risk is tied to objectives.** A risk only matters relative to what you're trying to achieve. If your goal is to cross a bridge safely and dry, rain is a risk. If your goal is to have fun, rain might not matter at all. Always ask: "risk _to what_?"
- so even before finding and quantifying the risks, the business objectives need to be defined first

**Detectability matters.** You need to know what to look for. If you can't identify what could go wrong (your "risk scenarios"), you can't analyze, control, or monitor anything. Detection comes first; everything else follows.

**Communication is essential.** Risk information needs to flow in both directions:
- **Top-down:** Leadership communicates risk appetite (how much risk is acceptable) and policies, so everybody is on the same "wave".
- **Bottom-up:** Operational teams report incidents, near-misses, and emerging risks.
- Good risk communication is: complete, relevant, correct, timely, secure, and understandable.

**Integration, not afterthought.** Risk management should be woven into daily activities, not bolted on at the end. If it feels like a separate bureaucratic exercise, you're doing it wrong.
- it should be something that employees do on a normal basis, not something "additional" to do
- that is also why the risk management needs to be involved at the strategic planning 
	- cannot be "added" to a finished strategy 

**Cost/benefit balance.** Controls (measures to reduce risk) cost money and effort. They cannot be more expensive than the risk they mitigate, and they shouldn't block normal business from functioning.
- and also the risk countermeasures cannot interfere with the company normal processes too much (to still allow employees to do effective work)

**The human factor.** People are both the biggest asset and the biggest vulnerability. Human behavior contributes to risk, and controls need to account for human error.
- there should be controls "against" other employees (e.g. defensive programming, even if the function is being used only internally, validate all inputs and do not rely on other programmers to do it right)
### Risk vs. Incident
```
Incident = past event, 100% certain it happened, known impact
Risk = future possibility, uncertain timing, uncertain impact
```
# 2. Risk Management in Context
### The Big Picture
At the highest level, organizations exist to accomplish missions. Two disciplines help ensure IT supports that mission:
1. **IT Governance** - directing and controlling IT to align with business strategy
2. **Enterprise Risk Management (ERM)** - identifying and controlling risks across the whole organization

IT Risk Management sits at their intersection. It's about optimizing risk in IT-related areas - not eliminating it - so the organization can create value from IT.
### IT Governance - What Is It?
Multiple definitions exist, but they all converge on three themes:

|Theme|What it means|
|---|---|
|**Direction setting**|IT should support enterprise strategy and objectives|
|**Decision mechanisms**|Clear leadership, structures, and processes for making IT decisions|
|**Monitoring**|Track performance and compliance against agreed goals|

Every major IT governance framework explicitly includes risk management as an integral component. The conclusion: **risk management is not optional - it's a core governance activity.**

> Risk always exists, whether or not the organization has detected it.
# 3. Enterprise Risk Management Frameworks
Two major ERM frameworks dominate: COSO ERM and ISO 31000
### COSO ERM
**COSO** = Committee of Sponsoring Organizations of the Treadway Commission. Their ERM framework is the landmark standard.

**Key definitions from COSO ERM 2017:**
- **Risk:** The possibility that events will occur and affect the achievement of strategy and business objectives.
- **ERM:** The culture, capabilities, and practices, integrated with strategy-setting and execution, that organizations rely on to manage risk in creating, preserving, and realizing value.

Note the word _culture_ - ERM isn't just a process you install. It requires the right mindset, people, and organizational habits.
#### The 2004 → 2017 shift
This is an important conceptual change:

```
COSO 2004:  Strategy → Objectives → Manage risks to objectives
COSO 2017:  Consider risk WHEN SETTING strategy → Objectives → Still manage risks
```

The old approach treated risk as something you manage _after_ deciding on a strategy. The new approach says: **risk should inform strategy selection itself.** Two additional considerations:
1. **Strategy-mission alignment risk:** Is the chosen strategy actually aligned with your mission and values? If not, that's a risk in itself.
2. **Strategy implications risk:** Every strategy has its own risk profile. Choosing strategy A over B means accepting A's specific set of risks.
#### COSO ERM's 5 Components (20 Principles)

| Component                                     | What it covers                                                                                                                                                                 |
| --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **1. Governance & Culture**                   | Tone at the top, oversight structures, ethical values, desired behaviors around risk. If the top management does not take risk seriously, then nobody does.                    |
| **2. Strategy & Objective-Setting**           | Analyze business context, define risk appetite, evaluate alternative strategies, set objectives with risk in mind. The risk appetite should be the same for the whole company. |
| **3. Performance**                            | Identify risks, assess severity, prioritize, implement responses, build a portfolio view                                                                                       |
| **4. Review & Revision**                      | Assess changes, review risk and performance over time, pursue continuous improvement. It is a continuous loop.                                                                 |
| **5. Information, Communication & Reporting** | Leverage IT systems for ERM, communicate risk info, report on risk/culture/performance                                                                                         |

Components 1-2 map roughly to **governance** processes in COBIT.  
Components 3-4 map roughly to **risk management** processes in COBIT.
#### Benefits of ERM (according to COSO)
1. **More opportunities** - considering both upside and downside of risk reveals new possibilities
2. **Entity-wide risk visibility** - risks in one department can affect others; ERM connects the dots
3. **Fewer negative surprises** - better identification and response = less firefighting
4. **Less performance variability** - anticipate disruptions, plan accordingly
5. **Better resource allocation** - know where to invest limited resources for maximum protection (and then save money in the future by reducing the risk)
6. **Greater resilience** - ability to not just survive change, but thrive through it
#### COSO's challenges for the future
- Dealing with increasing data volume
- Leveraging AI and automation for risk management
- Managing the cost of risk management (cost should never exceed benefits)
- Building stronger, more resilient organizations
### ISO 31000
The other major ERM standard, it is more high-level, more philosophical (many companies use both COSO ERM and ISO 31000)

Simpler definition:
- **Risk:** Effect of uncertainty on objectives
- **Risk Management:** Coordinated activities to direct and control an organization with regard to risk

ISO 31000 has three pillars:
1. **Principles for managing risk** - e.g., creates value, is integrated, systematic, based on best info, tailored, considers human/cultural factors
2. **Framework for managing risk**  - mandate, design, implement, monitor, improve
3. **Process for managing risk** - establish context → risk assessment (identify risk → analyze it → evaluate) → risk treatment, with communication and monitoring throughout
# 4. IT-Specific Risk & Security Frameworks
- COSO ERM and ISO 31000 are aimed at the whole company (on the Board level), these are specialized on the I&T and related areas

| Framework                         | Notes                                                                                                                                                                                    |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **COBIT family**                  | COBIT 2019 + Focus Areas for Risk and Security + RiskIT 2nd Ed. It has free general guidelines, details are behind a big paywall (as all frameworks like this).                          |
| **ISF Standard of Good Practice** | Very detailed, but expensive and closed audience                                                                                                                                         |
| **ISO/IEC 27000 series**          | Information security management. ISO 27001 is certifiable (you can get audited and certified). Covers ISMS requirements, code of practice, implementation, measurement, risk management. |
| **NIST CSF**                      | US-origin cybersecurity framework. Widely used, especially in American contexts.                                                                                                         |

### Two Different Risk "Languages"
An important distinction that can cause confusion:

```
View 1: Risk = NOT meeting objectives
  → Used by: COSO ERM, ISO 31000
  → Language: objectives, inherent risk, residual risk, controls
  → More business-oriented, intuitive for management

View 2: Risk = Damage to assets  
  → Used by: ISO 27001, security standards
  → Language: threats, vulnerabilities, assets
  → More technical, intuitive for IT/security people
```

Both views should ultimately lead to the same conclusions, but the framing is different. In practice, you'll encounter both.
### Pain Points in Practice
Real organizations struggle with risk management because of:
- **No one owns it.** Responsibility isn't assigned, or it's assigned too low in the hierarchy. Sometimes there's not even a CRO (Chief Risk Officer).
- **Senior management doesn't engage.** They see risk management as conflicting with performance.
- **Inconsistent methods.** Different teams use different approaches, no common taxonomy, no clearly defined risk appetite.
- **Wrong incentives.** Risk management is seen as pure overhead and cost, so nobody is rewarded for doing it well.
	- If the risk does not fulfill (because of a good risk management), no "presentable" results are visible
# 5. Key Definitions
### Risk Definitions Across Standards

| Source        | Definition                                                                                                                                                      |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ISO 31000** | Effect of uncertainty on objectives                                                                                                                             |
| **COBIT**     | A probable situation with uncertain frequency and uncertain magnitude of loss (or gain). IT Risk = business risk associated with use/ownership/operation of IT. |
| **FAIR**      | The probable frequency and probable magnitude of future loss                                                                                                    |
| **COSO ERM**  | The possibility that events will occur and affect strategy and business objectives                                                                              |

Common elements across all definitions:
- A **future event or scenario** that could impact the organization
- Impact on **strategic objectives**
- **Uncertainty about frequency** (how often?)
- **Uncertainty about impact** (how bad?)
### Information Security vs. Cybersecurity
**Information Security** = maintaining three properties of information:
```
CIA Triad:
  C = Confidentiality   → only authorized people can access it
  I = Integrity         → information is complete and unmodified
  A = Availability      → information is accessible when needed
```
- **Information Security Risk** = the risk that C, I, or A is breached.
- **Cybersecurity** is essentially the same thing, but specifically focused on threats coming from the internet/cyberspace. The terms overlap heavily; "cybersecurity" is just trendier and helps get budget approval.
## 6. FAIR - Factor Analysis of Information Risk
FAIR is a **risk ontology** - a structured model that breaks risk down into measurable components. This is crucial because it enables **quantitative risk analysis** (putting actual numbers on risk, not just "high/medium/low").
### The FAIR Risk Tree

```
                          RISK
                     ┌─────┴─────┐
              Loss Event       Loss
              Frequency      Magnitude
              ┌───┴───┐      ┌───┴────┐
         Threat     Vulner-  Primary  Secondary
         Event      ability   Loss      Risk
         Freq.               ┌───┴────┐
         ┌──┴──┐            Sec.Loss  Sec.Loss
      Contact  Prob.of      Event     Magnitude
      Freq.    Action       Freq.
                    ┌──┴──┐
               Threat    Difficulty
               Capability
```

### Breaking it down
**Risk = Loss Event Frequency × Loss Magnitude**
- Both components are necessary for a meaningful risk assessment. A risk that happens often but has tiny impact might be less important than one that's rare but catastrophic.
#### Loss Event Frequency (LEF)
_How often does the loss actually occur?_
- Expressed as annualized values (e.g., "5–15 times per year, most likely 9"). Can be less than 1 (e.g., once every 10 years = 0.1/year).
- LEF is driven by two sub-factors:
	- **Threat Event Frequency (TEF)** - how often a threat agent _acts_ in a way that _may_ result in loss.
	- **Vulnerability** - the probability that a threat event becomes a loss event (in percentages).

Important distinction:
```
Threat Event: A hacker attacks your website
Loss Event:   The hacker actually steals data or causes damage

Threat Event: Deploying a new software version to production  
Loss Event:   The deployment causes downtime
```

Not every threat event becomes a loss event. TEF ≥ LEF always. There could be a threat, but no loss.

TEF is driven by:
- **Contact Frequency** - how often threat agents come into contact with your assets (randomly, regularly, or intentionally)
- **Probability of Action** - given contact, how likely is the threat agent to act? Depends on perceived value, effort/cost, and risk to the attacker.

FAIR defines vulnerability differently from most standards:

```
Other standards: vulnerability = a condition (e.g., "weak password")
FAIR:            vulnerability = a probability (e.g., "weak password is 50% vulnerable to brute force attack")
```

Vulnerability is driven by:
- **Threat Capability** - how skilled/powerful the attacker is (scale 1–100)
- **Difficulty** - how hard your defenses are to overcome (measured against the same scale)
#### Loss Magnitude
_How bad is it when a loss event happens?_
- Split into **primary loss** and **secondary risk**.

**Primary Loss** - direct damage to the primary stakeholder (the organization being analyzed):
- FAIR defines 6 forms of primary loss (all expressed in money):

|#|Form|Example|
|---|---|---|
|1|Productivity loss|Revenue lost during outage|
|2|Cost of response|Incident response team, forensics|
|3|Replacement cost|Replacing damaged hardware/data|
|4|Competitive advantage|Lost market position|
|5|Fines and judgments|Regulatory penalties|
|6|Reputation|Brand damage (quantified financially)|

**Secondary Risk** - the "fallout" from the primary event. This is about how _other stakeholders_ react.
- Example: Your company has a data breach (primary event). Then:
	- Customers sue you (secondary loss)
	- Regulators fine you (secondary loss)
	- Business partners cut ties (secondary loss)
	- You spend on PR crisis management (secondary loss)
	- You spend on legal defense (secondary loss)

Secondary risk = Secondary Loss Event Frequency × Secondary Loss Magnitude  
(i.e., what percentage of primary events trigger secondary effects, and how costly are those effects?)
## Quick Reference Card

|Concept|One-liner|
|---|---|
|Risk|Uncertainty about future events affecting objectives|
|Risk Appetite|How much risk is the organization willing to accept?|
|Inherent Risk|Risk level before any controls are applied|
|Residual Risk|Risk level after controls are applied|
|Control|A measure that reduces risk (detective, preventive, corrective)|
|Threat|Something that could cause harm|
|Vulnerability|A weakness that a threat can exploit (or in FAIR: probability of exploitation)|
|CIA Triad|Confidentiality, Integrity, Availability|
|LEF|Loss Event Frequency - how often losses actually occur|
|TEF|Threat Event Frequency - how often threats act (≥ LEF)|
|FAIR|Factor Analysis of Information Risk - a quantitative risk ontology|
|COSO ERM|The landmark enterprise risk management framework|
|COBIT|IT governance and management framework (our main tool)|
|ISO 27001|Certifiable information security management standard|
|NIST CSF|US cybersecurity framework|