Finalizes notes from the course lectures and other students' notes organized into different topics:
- [[DRS - Introduction + Risk and Security Standards]]
- [[DRS - COBIT refresher]]
- [[DRS - The risk and security function]]
- [[DRS - Risk Governance, Management, Identification]]
- [[DRS - Risk Analysis]]
- [[DRS - Risk Response]]

***
### Exam prep: FAIR taxonomy classification exercise
- [[DRS - Introduction + Risk and Security Standards#6. FAIR - Factor Analysis of Information Risk]]
You need to classify items according to the FAIR taxonomy.
Key definitions to remember:
- Asset: a thing of value
- Threat: something with the ability to actively cause harm (a threat needs agency)
- Threat actor: an entity that can act (person, group, nation state)
- Threat event: an action that may result in loss
- Vulnerability: a condition that makes loss more likely (in FAIR terms, a probability)
- Risk: the combination of loss event frequency and loss magnitude
##### Exercise 1: Threat, vulnerability, or risk?
```
1. Disgruntled employee          → Threat (actor with ability to harm)
2. Non-encrypted backup tape     → Vulnerability (a weakness/condition)
3. Non-compliance with change    → Vulnerability (a weakness/condition)
   management policy
4. Unpatched internet-facing     → Vulnerability (a weakness/condition)
   server
5. Customer database with PII    → Asset (thing of value)
6. Fine for privacy              → Risk (probable future loss)
   non-compliance
```
##### Exercise 2: Threats or threat actors?
```
1. Hacktivist                    → Threat actor (a person/group)
2. The cloud                     → Asset (infrastructure, not a threat)
3. Social engineering            → Threat event (an action)
4. Organised crime               → Threat actor (a group)
5. State-sponsored attacks       → Threat event (an action)
6. Social networking             → Neither (just a thing/platform)
7. Mobile devices                → Neither (just a thing/asset)
8. DDoS                          → Threat event (an action)
```
The key distinction: threat actors are WHO can cause harm (people, groups, organizations). Threat events are WHAT they do (the action itself). Vulnerabilities are the CONDITIONS that make harm possible. Assets are WHAT has value and needs protection. Risk is the overall combination of frequency and magnitude of potential loss.
### Exam prep: case study
- [[DRS - Caselets Case Study]]
###### Business impact criteria
You need to pick what kinds of impact matter to your organisation - I cannot assess everything, I need to chose (usually based on the business impacts that are important for the company). Two example sets:
- ISACA Risk IT: financial (share value, profit, revenue, cost of capital), client (market share, customer satisfaction), internal (regulatory compliance), growth (competitive advantage, reputation)
- FAIR: productivity, cost of response, replacement cost, competitive advantage, fines & judgements, reputation
You don't use all of these - pick a handful that reflect your strategic priorities. The usage pattern is always: "If `<event>` happened, the impact on `<criterion>` would be `<value>`."
###### Building a taxonomy in practice (the exercise pattern)
Two-step process:
1. Identify strategic priorities. For a hospital: patient safety, healthcare quality, resource efficiency. For an automotive manufacturer: innovation, customer satisfaction, operational quality. For a parliament: MP satisfaction, budget performance, reputation.
2. Translate those into measurable impact criteria with rating scales. Aim for an even number of rating levels (0-5, say) so people are forced to commit to a side instead of parking on the middle.
Each rating level should be tied to a different mitigation action, so you're already thinking about response when you build the scale.
### Exam prep: tools for assessing risk
Two free FAIR tools the course expects familiarity with:
- App.fairu.net - online tool
- Open Group's tool at publications.opengroup.org/i181 (has issues on macOS)
Practical notes from the lecture: fill in every level explicitly, put zero rather than leaving blank. The Excel-based tool is handier for business cases where you compare current state vs proposed solution - exam-relevant.

***

**Disclaimer**: many formulations in the notes are my "own explanations" which helped me to understand the topics. I don't know, if those explanations are 100% correct, but at least they form a different view on the course material. Consulting official materials is recommended.

If you find something wrong, don't hesitate to contact me - [[Rozcestník#Disclaimer]]