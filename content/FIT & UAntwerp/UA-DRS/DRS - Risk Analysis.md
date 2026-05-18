# The big picture
You've already identified risk scenarios in chapter 7. Now you need to actually estimate how bad each one is and how often it'll happen, so you can plot them on the risk map and decide what to do. That's risk analysis.

Two methods, both legitimate, different trade-offs:
- Qualitative - score each scenario on the impact criteria you defined in your taxonomy (chapter 5), score its likelihood, plot it. Fast, subjective, good for getting started.
- Quantitative (FAIR) - decompose the risk into measurable components, collect calibrated estimates from experts, run a simulation, get a monetary loss number. Slower, more rigorous, gives you numbers you can compare across scenarios and aggregate at the enterprise level.

The chapter mostly assumes you'll use both - qualitative to do a first pass on everything, quantitative on the scenarios that matter most.
# Qualitative risk analysis
### The analysis flow
Risk analysis is: assess the probable impact when a risk scenario materialises, and the likelihood of that happening, taking into account the state of controls and all other risk factors.

The key ingredients feeding into the analysis are risk factors - things that influence either impact or likelihood. COBIT groups them into four buckets:
- External context - market and economic conditions, rate of change in the market/product lifecycle, industry and competition, geopolitical situation, regulatory environment, technology status and evolution, threat landscape
- Internal context - enterprise goals, strategic importance of IT, complexity of IT and the entity, change management capability, operating model, strategic priorities, culture, financial capacity
- Risk management capabilities - how mature your risk governance and risk management actually are
- IT-related capabilities - how mature your IT processes are (EDM, APO, BAI, DSS, MEA)

The point: context changes the likelihood and impact estimates. High threat landscape pushes impact up. Poor IT delivery processes push likelihood up. You don't analyse scenarios in a vacuum - you analyse them in your actual environment.
### Three flavours of risk
This is one of the most confused distinctions in the whole course, worth nailing down:
- Inherent risk - the risk that would exist if there were no controls at all. Purely hypothetical and hard to estimate because mentally subtracting all your controls is not trivial.
- Current risk (or "actual risk") - the risk as it stands today, with all your existing controls in place. This is what you actually plot on the risk map.
- Residual risk - the risk that will remain after you've executed your planned risk action plan. The scenario moves from wherever it is today to (hopefully) the green zone after mitigation.

The arrow goes: inherent → current → residual, as more controls get added.
### How qualitative analysis actually works
For each scenario, you score it against every impact criterion in your taxonomy (Safety, Client Satisfaction, Financial Cost, Innovation & Quality, Compliance, or whatever you defined). Each criterion gets a rating 0-5 based on the rating scales you built.

Then you need to collapse multiple criterion ratings into one impact number. Two options:
- Max value - take the worst score across all criteria. Preferred, because if a scenario is catastrophic on one dimension, that catastrophe doesn't get diluted by being mild on others.
- Average - smooths everything out. Loses important signal.

A sanity check: the impact criteria aren't truly independent - a serious incident usually hits multiple dimensions at once. If your scores are wildly inconsistent across criteria for the same scenario, your taxonomy is probably miscalibrated.

For likelihood, you score on a scale like `<5%`, `<10%`, `<25%`, `<50%`, `<80%`, `>80%` chance of occurring in a year. Estimate based on your knowledge of the controls in place, staff awareness, threat environment, etc.

Combine impact and likelihood, plot on the risk map, see which zone the scenario lands in.
### The GHSP homework - assessing capability against a target
This worked example is worth understanding because it's the pattern for any capability assessment question.

The task: evaluate whether GHSP's proposed risk management process would reach capability level 3, and identify gaps.
The method:
1. Question targets level 3, so only consider activities tagged with capability level 2 and level 3 (you need everything below your target).
2. Walk through every APO12.0X.Y activity in COBIT one by one.
3. For each activity, read GHSP's description and rate how well they actually do it:
   - Fully - more than 85% achieved
   - Largely - 50-85% achieved
   - Partially - 15-50% achieved
   - Not - less than 15% achieved
1. Improvements needed = the activities that aren't fully achieved at your target level.

Walking through GHSP's actual results gives you a feel for it. They're partially or largely achieving most level 2 activities, but mostly not achieving level 3 activities - particularly anything involving formal risk taxonomy, articulation to stakeholders, balanced project portfolios, or tested response plans. Conclusion: a lot of work needed to reach level 3.

The general pattern of this exercise: it's not enough to "have a risk process." Each capability level demands specific, named activities. If those activities aren't happening, the level isn't reached, regardless of how much the organisation feels it has a process.
***
# Quantitative risk analysis - FAIR
This is the big shift in the chapter. FAIR (Factor Analysis of Information Risk) is the dominant quantitative method for IT and security risk.
### The FAIR taxonomy
- [[DRS - Introduction + Risk and Security Standards#6. FAIR - Factor Analysis of Information Risk]]

FAIR decomposes risk into a tree. From the top down:
- Risk = Loss Event Frequency × Loss Magnitude
- Loss Event Frequency = Threat Event Frequency × Vulnerability
- Threat Event Frequency = Contact Frequency × Probability of Action
- Vulnerability = Threat Capability × (Resistance Strength, sometimes called "Difficulty")
- Loss Magnitude = Primary Loss + Secondary Risk
- Secondary Risk = Secondary Loss Event Frequency × Secondary Loss Magnitude

The course typically focuses on the third level from the top - Threat Event Frequency, Vulnerability, Primary Loss, Secondary Risk. You estimate values at that level, and the tool computes everything upward.

A practical note: you can choose how deep into the tree to estimate. Going deeper (Contact Frequency and Probability of Action separately, instead of just Threat Event Frequency) gives more rigour but takes more time. You don't always need to go to the leaves.
### Measurement as reduction in uncertainty
The philosophical foundation FAIR rests on - and it's an important shift for technical people:
"Measurement is a reduction in uncertainty to a useful level of precision."
You don't need precise answers. You need to know enough less than "I have no idea" to make better decisions. Example: "How long is the US shoreline?" Answer: "about 12,000 miles." Actual: 12,283. That estimate is useful even though it's not exact.
The corollary - single point estimates are always wrong. "When will you be home?" "20:00" is going to be wrong. "Between 19:00 and 20:30" is going to be right. Always express estimates as ranges.
### The four parameters of every FAIR estimate
Every value you plug into the FAIR model has four components:
- Minimum value
- Maximum value
- Most likely value
- Confidence level (High, Moderate, Low)

High confidence is rare. Most real estimates are Moderate or Low. That's fine - the simulation handles uncertainty by sampling from the range, weighted by the most-likely value and adjusted by confidence.

The key principle: accuracy is more important than precision. Better to give a wide range that's definitely right than a narrow range that's probably wrong.
### Calibration - the technique that makes this work
Douglas Hubbard's calibration method claims to get estimates to 90% accuracy. The steps:
1. Decompose the question if needed - break "how much will this cost?" into "how many incidents?" times "cost per incident."
2. Start with the absurd - is it 1 million? 100 million? Knocking out absurd values shrinks your range fast.
3. Eliminate highly unlikely values - work in from both ends.
4. Reference any available data - even a single data point beats pure guessing.
5. Play the calibration game.
###### The equivalent bet test (the calibration game)
You're offered two ways to win 1000 euros:
1. The range you give contains the true value
2. Spin a wheel with a 90% chance of winning
If you'd rather pick the range, you're more than 90% confident in it - probably too wide, narrow it. If you'd rather spin the wheel, you're less than 90% confident - widen the range. When you genuinely can't decide between the two, you're calibrated at 90%.
###### Estimation obstacles to watch for
- Anchoring - starting from a "correct" guess and adding buffer either side. Produces ranges that are too narrow.
- Groupthink - social reinforcement makes the group more confident than any individual should be.
- Ostrich effect - avoiding bad-news data.
- Conservatism / cognitive bias - sticking with prior beliefs even with new evidence.
- "I don't know" - the refusal to estimate at all. Counter this by starting with absurd values to anchor on something concrete.
### The four-step FAIR analysis process
1 - Scoping
Same as chapter 7. Define each scenario with Asset, Threat, Effect, Method/Vector. Start the scenario name with "Analyse the risk from..."
When generating the initial list, consider a broad range of threat communities - nation states, cyber criminals, privileged insiders, non-privileged insiders - and threat types - malicious, error, mechanical, process failure, natural. Not all will be developed into full scenarios, but you should have considered them.
A representative set is good enough. Don't try to enumerate every possible scenario.

2 - Collecting data and estimates
This is the labour-intensive part. The phases:
- Prepare context-specific questions ("audit work program") that translate FAIR's generic variables into your specific scenario. Example: instead of asking "what's the Loss Event Frequency?" ask "Over the next year, how many times will our booking system experience an outage due to a DDOS attack by malicious outsiders that results in inability to take payments?"
- Decide how deep into the FAIR tree to analyse. Deeper = more abstract questions, more time, more rigour. Meet somewhere in the middle.
- Identify and meet subject matter experts. Overcome the usual objections ("you can't predict the future," "we don't have enough data") by explaining how the method actually works.
- Run the estimation session. Game plan: introduce team and purpose, brief FAIR intro, describe the scenario clearly, walk down the model to the variable you need, ask context-specific questions, collect calibrated estimate and document rationale, close.
- For each estimate, ask the question, identify an upper or lower boundary and why, interrogate the boundaries to capture rationale, play the calibration game to 90% confidence, identify the most likely value within the range, document confidence and any uncertainty sources and controls considered.

3 - Running and validating analysis
The tool does the Monte Carlo simulation. You QA the output:
- Are the results reasonable compared to estimates and known historical data?
- Is the rationale adequate? (Names of SMEs, data considered, why the range is what it is, controls considered, sources of reducible uncertainty)
- Did we achieve the analysis' purpose?

4 - Reporting
The main output is ALE - Annualized Loss Exposure. How much money you should expect to lose from this scenario over a year.
- When Loss Event Frequency > 1 (event happens multiple times a year), ALE is the straightforward sum.
- When LEF < 1 (event happens less than once a year, maybe once in 10 years), ALE corresponds to "the amount to reserve each year" - if a 10-million loss happens once a decade, ALE is 1 million per year.

Report ALE with multiple data points: Min, 10th percentile, Most Likely, Average, 90th percentile, Max. People often care more about the max than the average, because that's what they'd want to be insured against.
###### The loss exceedance curve
A graph with loss exposure on the x-axis and "probability of loss being at least this amount" on the y-axis. Reading it: pick a loss amount, the curve tells you the probability of exceeding it. So a point at (1M euros, 60%) means there's a 60% chance the annual loss will be 1 million euros or more.
###### Beyond ALE - what else to report
ALE is necessary but not sufficient. Also report:
- Maximum single event loss - the worst case for a single occurrence
- Fragile conditions - high dependency on a single control to keep vulnerability low. If that one control fails, the whole picture changes. Multiple independent controls reduce fragility.
- Unstable conditions - low frequency combined with high vulnerability. You're depending on luck for the threat event frequency staying low. If the threat environment shifts, you're exposed.
# Risk aggregation
Once you have analysed individual scenarios, you usually need to combine them for board reporting and enterprise-level decision-making. That's risk aggregation.
### Why aggregate
Managing risks one at a time is incomplete. The enterprise-level view:
- Lets you actually compare current exposure to enterprise risk appetite
- Prevents false sense of security ("each individual risk is fine") or false sense of urgency ("this one risk looks huge")
- Reveals patterns - if multiple business areas are reporting the same control deficiency, an enterprise-wide initiative is justified
- Required by regulators in some industries (Basel Committee for banks, for example)
### How to aggregate
The simplest approach: add up the Annualized Loss Expectancies. If everything is in monetary terms, the math is straightforward.
A few rules to follow:
- Use a uniform, consistent, agreed method for frequency and impact, so the underlying values are actually comparable
- Be cautious with the math. Only aggregate data of the same nature. Don't mix control status with operational metrics with loss exposure - they aren't the same thing
- Don't aggregate so much that you hide actionable detail. Root causes must stay visible to whoever has to manage them
- Aggregate along multiple dimensions where useful - organisational units, types of risk, business processes. The business-process view in particular reveals weak links to business outcomes
- Aggregate at enterprise level to combine with non-IT risks (ERM)
- Respect the organisational structure so the aggregation cascade is meaningful
### Aggregation obstacles
Why this is hard in practice:
- Inconsistent terminology across the enterprise
- Complex enterprises with sub-cultures - different entities describe risk differently
- Mostly qualitative data with limited reliability or incompatible scales
- Unknown dependencies between reported risks - several entities each reporting medium risk might collectively represent major enterprise risk
- Scale mismatch - a risk that's important to one entity may be trivial at enterprise level
### Disjoint vs shared risk
Two cases for combining risk maps:
- Disjoint risk - the risks are independent. You can plot all of them on the same map at their original positions. The combined map just shows more dots.
- Shared risk - the risks interact. Combining changes the positions because events amplify each other. There's no general rule - you have to interpret.
Examples of amplification (where aggregation is more than addition):
- One data centre down is acceptable. Two data centres down at the same time is catastrophic. The combined risk is much higher than the sum.
- Disk failure plus failing backups is qualitatively worse than disk failure alone.
- A delayed architecture project delays every application project that depends on it. The architecture delay's impact aggregates through the dependency chain.
The benefit of doing this properly: enterprise-level visibility makes a cost-efficient enterprise response justifiable. You can fund one initiative that addresses the root cause of multiple medium-level risks, instead of running parallel mitigations for each one separately.
# How the pieces fit together
End-to-end flow for risk analysis:
1. Identify scenarios using COBIT categories and/or FAIR scoping (chapter 7)
2. For each scenario, decide qualitative or quantitative analysis
3. Qualitative path: rate impact against your taxonomy criteria (take max), rate likelihood, plot on risk map, compare to appetite
4. Quantitative path: scope, collect calibrated estimates from SMEs, run FAIR simulation, get ALE and exceedance curve, report with fragility and stability context
5. Aggregate analysed risks at the enterprise level, accounting for dependencies, to produce a board-level view
6. Compare aggregated exposure to risk appetite and capacity, decide on responses (chapter to come)
The reason both qualitative and quantitative methods coexist: qualitative is fast and gets the whole portfolio onto a map quickly; quantitative is slow but gives you defensible numbers for the scenarios that matter. Most mature organisations use qualitative as a screen and FAIR on the top-priority scenarios.
# Things worth remembering for the exam
- Inherent risk (no controls) → Current risk (existing controls) → Residual risk (after planned actions)
- Qualitative analysis: rate against taxonomy criteria, take MAX (not average), combine with likelihood, plot on map
- Capability assessment method: pick the activities at and below your target level, rate each Fully/Largely/Partially/Not, gaps are the improvement plan
- FAIR taxonomy: Risk = LEF × LM, LEF = TEF × Vuln, LM = Primary + Secondary
- Every FAIR estimate has Min, Max, Most Likely, Confidence (H/M/L)
- Measurement = reduction in uncertainty, not certainty. Accuracy > precision.
- Calibration aims for 90% confidence intervals. Equivalent bet test is the technique.
- Estimation obstacles: anchoring, groupthink, ostrich effect, conservatism, "I don't know"
- FAIR process: Scoping → Collecting estimates → Running/validating → Reporting
- ALE = Annualized Loss Exposure. For LEF < 1, it's the annual reserve amount.
- Loss exceedance curve: probability of annual loss being at or above a given amount
- Report ALE alongside max single-event loss, fragile conditions, unstable conditions
- Aggregation: disjoint risks combine simply, shared risks amplify and need interpretation
- Tools: App.fairu.net and the Open Group tool - expect to use them on the exam