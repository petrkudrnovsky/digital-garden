### Chapter 4: the risk function and the security function
This chapter is about the practical side: how do you actually organize risk management and information security inside a company?

COBIT provides two Focus Area publications for this - one for I&T Risk, one for Information Security. Both follow the same 7-component structure from the COBIT core model, but add specific guidance tailored to risk and security.
- [[DRS - COBIT refresher#What's Inside Each Box? (The 7 Components)]]

The COBIT 2019 Information & Technology Risk Focus Area aims to:
- Give stakeholders real data on the current state of risk
- Guide risk management within the organization's risk appetite
- Help build a proper risk culture
- Enable quantitative risk assessments where possible (cost of mitigation vs. loss exposure)
The COBIT 2019 Information Security Focus Area aims to:
- Describe security in an enterprise context (not just the IT department)
- Ensure continuous availability of services
- Build cybercrime resilience
- Comply with laws, regulations, and internal policies
- Do all of the above without blowing the IT budget

---
# 4.1 The risk function
The risk function is described through all 7 COBIT governance components. Let's walk through each one.
### 4.1.1 Organizational structures for risk
There are two categories of roles/structures needed for risk management:
- Core structures - people/groups who spend most of their time on risk governance and management. These are the dedicated risk roles.
- Supporting structures - people whose main job is something else (HR, finance, business owners), but who have risk management as a smaller part of their responsibilities.

Core roles and structures suggested by COBIT:
- Enterprise Risk Management (ERM) Committee - a group of executives accountable for enterprise-level ERM. They collaborate and build consensus on risk decisions. Chaired by the CEO, with members drawn from the board. The internal audit manager may attend as a guest. An IT risk council may sit underneath this committee.
- Chief Risk Officer (CRO) - the most senior person accountable for all aspects of risk management across the enterprise. An I&T risk officer function may exist underneath the CRO, taking direction from the ERM committee.
- Audit department - provides internal audit reports on risk and control gaps. Considered the third and last line of defense.
- Compliance department - responsible for insight into regulatory, legal, and policy-related risk.
- I&T risk officers/managers - the professionals who actually identify, assess, and report on I&T risk day-to-day. They provide oversight and support to I&T risk owners.

Not every organization will have all of these - it depends on size, industry, risk profile, and other design factors.
Supporting roles include the Board, CEO, Strategy (IT executive) committee, COO, Data Privacy Officer, and various business owners. Each has a specific stake in the risk function - for example, the Board provides oversight and sets the tone at the top, while business owners accept ownership of specific risks in their domain.
### 4.1.2 Processes for risk
COBIT distinguishes two types of processes relevant to risk management:
- Core risk objectives - specifically EDM03 (Ensured Risk Optimisation) and APO12 (Managed Risk). These are the primary risk processes, elaborated in detail in the I&T Risk Focus Area publication. They're covered in depth later in the course.
- Supporting objectives - many other COBIT processes also contribute to risk management. For each of these, the Focus Area adds risk-specific activities, inputs/outputs, and metrics on top of the generic COBIT content.
For example, APO02 (Managed Strategy) gets additional risk-specific activities like "understand how the I&T risk function should support enterprise objectives" and produces risk-specific outputs like "listing of potential risk function coverage gaps."
- so in summary, these focus areas take a lot of existing processes from the original COBIT and they enhance them, add the specific context and provide practical guidelines ("I&T Risk-specific activities/inputs/outputs")
	- in the focus area, the original process is mentioned and then I&T risk-specific activities which are listed in addition to the core process (along with the capability level)
### 4.1.3 Culture, ethics and behavior
This is where the "soft" stuff lives - and it matters enormously. Risk management fails without the right culture.
Three layers of behavior shape risk culture:
- Organizational ethics - the values the enterprise defines for itself
- Individual ethics - each person's own values (shaped by background, religion, experience, etc.)
- Individual behaviors - what people actually do, which collectively creates the culture
Four levers to influence behavior:
1. Communication, enforcement, and rules
2. Incentives and rewards
3. Awareness programs
4. Monitoring actual behavior vs. desired behavior
##### General behaviors the organization should demonstrate
- A risk-aware and compliance-aware culture
- Policies that actually drive behavior (not just shelfware)
- Positive attitude toward negative outcomes (learn from them, don't hide them)
- Recognition that risk has value - trying to eliminate all risk means you also eliminate all opportunity
- Transparent, participative culture with mutual respect
- Business owners accept ownership of risk - especially for IT risk, someone needs to be the explicit owner who says "if this fails, here's the impact on my business"
- Risk acceptance is treated as a valid option (accepting risk is not the same as ignoring it)
##### Management behaviors
- Senior management visibly supports risk practices (tone at the top)
- Decisions are made with stakeholder engagement
- Resources are actually committed (not just lip service)
- Policies and actions are aligned with the defined risk appetite
- Risk trends are regularly reported to management
- Effective risk management is rewarded (difficult in practice, but important)
##### Professional behaviors
- Effort to understand what risk means for each stakeholder
- Awareness and understanding of risk policies
- Demonstrated compliance with policies
- Two-way communication during risk identification, assessment, and response - if you see something wrong, you communicate it
##### How to influence behavior in practice
Tone at the top is the most critical lever. If management doesn't follow the rules they set, it's a lost cause. Specific mechanisms include:
- Leading by example
- Embedding risk considerations into business planning
- Enforcing compliance requirements with clear consequences
- Zero-tolerance for unethical behavior
- CEO communicating to business unit heads about the importance of risk analysis and root cause analysis
- Documented risk policy, approved by top management, shared with all employees
- Whistleblowing mechanisms - a way to skip the hierarchy to report unacceptable behavior. Legally protected in Belgium and many EU countries, but in practice it often doesn't end well for whistleblowers.
Incentives and rewards for risk behavior include:
- Risk-aligned personal objectives
- Compensation that includes risk criteria
- Career progression that considers risk engagement
- Praising desired behavior
Awareness mechanisms include:
- Visible support for training (not just flyers)
- Senior management reminders
- Risk awareness workshops with exercises and simulations
- Incident response awareness
- Providing relevant risk information
### 4.1.5 Information items for risk
Several key information artifacts drive the risk function. You should know what each one contains:
- Risk profile - a description of the overall identified risk the enterprise is exposed to. It includes the risk register, risk analysis results, loss events (historical and current), risk factors, and assessment findings.
- Risk register - the detailed inventory of identified risks. For each risk: the risk owner, scenario details, affected stakeholders, causes/indicators, risk ratings, risk response details (who's accountable, what actions), and risk tolerance level.
- Risk action plan - a prioritized plan of risk mitigation actions. Documents which scenarios will be mitigated, root cause analysis results, reasons for selecting specific controls, who's accountable and responsible, resource requirements, cost vs. risk reduction benefit, and monitoring requirements.
- Risk taxonomy - the common language for discussing risk across the enterprise. Must be communicated and used consistently.
- Risk indicator - a metric that shows the enterprise is subject to, or likely subject to, a risk exceeding its appetite. A Key Risk Indicator (KRI) is a risk indicator specifically identified as highly relevant and predictive.
- Risk factor - a condition that can influence frequency and impact of risk events. Categories include external context, internal context, risk management capabilities, and IT-related capabilities.
##### Risk appetite, tolerance, and capacity
These three terms are related but different:
- Risk appetite - how much risk the organization is willing to accept in pursuit of its mission. This is a management decision. Can be expressed quantitatively (as a number/euro amount) or qualitatively ("this event can only occur once in 5 years").
- Risk tolerance - the acceptable variation around the risk appetite for any particular risk. Also a management decision. You might temporarily allow extra risk for a justified reason.
- Risk capacity - the objective amount of loss the enterprise can absorb without risking its continued existence. This is NOT a management decision - it's a financial reality (how much money do you have, how well are you insured).
Key insight: if your actual risk consistently sits between appetite and capacity, you're operating dangerously. If it exceeds capacity, you're risking the survival of the organization.
Risk appetite and tolerance change over time as technology, organizational structures, market conditions, and strategy evolve. The cost of mitigation can also force changes - if the cost of mitigating a risk exceeds your capabilities, you may be forced to accept a higher risk level. For example, if regulation requires encrypting all sensitive data at rest but no feasible solution exists, the enterprise may choose to accept the regulatory non-compliance risk as a trade-off.
### 4.1.6 Services, infrastructure, and applications for risk
COBIT lists specific services the risk function should provide:
- Program/project risk advisory services
- I&T incident management services
- Architecture advisory services (ensuring business/data/tech architecture supports risk management)
- I&T risk intelligence services (threat, vulnerability, and asset intelligence)
- I&T risk management services (expertise for risk programs)
- Crisis management services (responding to I&T crises, including BCP activation)
Supporting applications include GRC tools (dashboards, risk matrices, scorecards), analysis tools (qualitative and quantitative), risk communication/reporting tools, knowledge repositories, and business continuity tools.
### 4.1.7 People, skills, and competencies for risk
Risk professionals need a broad mix of skills across 12 areas:
- Leadership skills - navigating different stakeholders with different opinions
- Analytical capability - breaking down complex risk factors
- Critical thinking - judging when enough analysis has been done, articulating risk scenarios
- Interpersonal capabilities - obtaining timely and accurate information from stakeholders with different backgrounds
- Communication - translating risk into the language and priority of each stakeholder
- Influencing - persuading people to adopt risk practices
- Lateral thinking - approaching risk differently depending on type, borrowing from other disciplines
- Technical understanding - you need enough technical knowledge to understand IT vulnerabilities and how systems connect. Not expected to be an expert, but having zero technical understanding is a problem.
- Organizational and business awareness - knowing the org chart, business units, escalation paths
- Risk expertise - deep knowledge of threat sources, scenarios, vulnerabilities, and impact
- Training and coaching - capability to train and coach others on risk

---
# 4.2 The security function
The security function follows the same 7-component structure but with security-specific content.
### 4.2.1 Organizational structures for security
Two categories, parallel to the risk function:
- Security-specific roles and structures - internal to the information security function
- Security-related roles and structures - outside the security function but involved in security topics (users, business process owners, etc.)

The core security decision-making roles and entities come from APO13 (Managed Security) and DSS05 (Managed Security Services):
- CISO (Chief Information Security Officer)
- ISSC (Information Security Steering Committee)
- ISM (Information Security Manager)
- Information owner
- Information custodian

Larger enterprises may add information security administrators, security architects, and security compliance/auditing officers.
##### The CISO role in detail
The CISO has overall responsibility for the enterprise information security program.
Reporting line: the CISO may report to the CEO, COO, CIO, CTO, CDO, CRO, or other senior executive. There is no single "correct" reporting line - it depends on the organization.
The CISO must:
- Understand the business strategic vision
- Communicate effectively
- Build relationships with business leaders
- Translate business objectives into security requirements
CISO responsibilities:
- Developing the IS strategy
- Establishing and maintaining an Information Security Management System (ISMS)
- Monitoring and reviewing the ISMS
- Defining and managing a security risk treatment plan
- Reporting to the board and audit committee
- Developing the security budget
- Chairing the ISSC and liaising with the ERM committee
The CISO delegates tasks to information security managers and business people, and escalates key risk-related issues to their direct supervisor and/or the ISSC.
##### The independence problem
This is an important structural issue:
If information security reports directly to IT, it creates a conflict of interest. IT provides service to the enterprise, while security manages risk related to protecting information. When these conflict - and they will - IT may override security practices in the name of customer service or project deadlines.
Therefore, a certain degree of independence between IT and information security should be established.
Each reporting line has trade-offs:
- CISO → CEO: highest visibility, but the CEO has too many responsibilities to manage security in detail
- CISO → CIO: good alignment with IT initiatives, but potential conflict of interest (IT priorities may override security)
- CISO → CFO: good financial perspective on security impact, but financial priorities may dominate
- CISO → CRO: natural fit (strategic, financial, operational, reputational risk views), but CRO role doesn't exist in most enterprises (mostly financial services)
- CISO → Board (indirect): highest-level visibility, but may become too abstract to be relevant
There is no universally correct answer - you need to evaluate case by case.
### 4.2.2 Processes for security
The two core security objectives highlighted in the COBIT 40-box model are:
- APO13 - Managed Security (strategic: establish and maintain an ISMS, define security risk treatment plan, monitor/review the ISMS)
- DSS05 - Managed Security Services (operational: protect against malware, manage network security, manage endpoint security, manage identity and access, manage physical access, manage sensitive documents, manage vulnerabilities and monitor for security events)
Many other COBIT objectives also support security. For each, the Information Security Focus Area adds security-specific activities, metrics, and capability level guidance. For example, APO08 (Managed Relationships) gets a security-specific activity: "understand the business and how information security enables/affects it."
### 4.2.3 Culture, ethics and behavior for security
COBIT defines 9 beneficial security behaviors:
1. Everyone is accountable for information protection
2. Security is practiced in daily operations (not a separate afterthought)
3. People respect security policies and principles
4. People are given sufficient security guidance and encouraged to challenge the current situation
5. Stakeholders know how to identify and respond to threats
6. Management proactively supports security innovation
7. Cross-functional collaboration is fostered for effective security programs
8. Executive management recognizes the business value of security (revenue, reputation, competitive advantage)
9. Management provides clear security communication, awareness, and training

Champions and leadership are critical for driving security culture:
- Champions are enthusiastic people who carry changes throughout the enterprise. They can be at any level - risk managers, security professionals, C-level executives, HR heads.
- Leadership influences behavior through three aspects:
  1. Communication, enforcement, rules, and norms
  2. Incentives and rewards (bonuses for security achievement, career progression tied to risk criteria, praise for desired behavior)
  3. Raising awareness (training, workshops, regular updates on threat landscape changes)

Important note on security metrics: raw numbers in isolation are meaningless. If "network attacks observed" went up, that could mean your detection improved (good) or that you're actually getting attacked more (bad). Metrics only have value when you track their evolution over time AND interpret the underlying reasons.
### 4.2.4 Information items for security
Key security information artifacts tied to APO13 and DSS05:
- Information security strategy
- Information security budget
- Information security plan/program
- Information security requirements
- Information security review report
- Information security management report (should contain all events, be stored centrally as a dashboard, with extracts for different stakeholders. Incidents need immediate updates; action plan status may be monthly.)
- Information security service catalog
### 4.2.5 Services and tools for security
Core services recommended by COBIT for APO13 and DSS05:
- Configuration management tools
- Directory services
- Email filtering systems
- Identity and access management (IAM) systems
- Security and privacy awareness services
- SIEM tools (Security Information and Event Management)
- SOC services (Security Operations Center)
- Third-party security assessment services
- URL filtering systems
Additional tools to consider (extensive list - see COBIT 2019 Information Security Focus Area pp.55-56):
- Anti-malware, anti-phishing, anti-spyware
- Biometrics, browser protection
- CASB systems (Cloud Access Security Broker)
- Code scanners, compilers
- Deep packet inspection, DLP (Data Loss Prevention) tools
- Endpoint detection and response
- File-integrity monitoring
- Firewalls, honeypots
- And many more