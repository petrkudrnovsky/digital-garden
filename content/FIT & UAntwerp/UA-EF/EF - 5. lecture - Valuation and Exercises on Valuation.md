This lecture is about how to put a price tag on an unquoted (private) entrepreneurial company. Valuation is just one item on a term sheet (alongside funding amount, anti-dilution, board seats, liquidation preferences, drag/tag-along, etc.), but it's the one that gets all the attention. The others come in lecture 6.

> "Price is what you pay, value is what you get." - Warren Buffett

The key thing to understand upfront: there is no "true" value of a startup. It's not a science, it's an art. You can build a 50-tab Excel model and still be wildly wrong (Beyond Meat IPO'd, the stock multiplied 7x on day one, then went to ~zero). For a company that has existed for 1 year and sold 200 products, accurate valuation is essentially impossible. The goal is to be approximately correct, not precisely incorrect.
### Status of the VC market in 2026
- During COVID (2020-2022), interest rates were near zero or negative. Investors had no alternative ("TINA") - bonds gave nothing, so money flowed into stocks and into private companies. Valuations exploded.
- Since 2022-2023, rates went back up to fight inflation. Bonds give 2-3% again. Money is no longer "free". Valuations of private companies have come down.
- 65% of new dollars invested in private companies in 2025-2026 are going to AI businesses. Lots of "AI washing" (companies pretending to be AI to get funding).
- Shift from growth-at-all-costs valuations (multiples on revenue) toward EBITDA-driven valuations (actual profitability).
- Secondary market is booming: VC funds that invested in 2018 want to sell now (their fund lifecycle is ending) but can't find buyers, so they create new "secondary funds" that buy companies from their own older funds. KKR does this a lot.
### Why valuation is hard for startups
There is no single true value because:
- The future is unpredictable
- The business may go bankrupt (binary outcome)
- Assumptions drive everything, and assumptions are uncertain
- Market sentiment shifts (a HelloFresh-type business is valued differently if Ozempic kills demand for meal kits)
- Many of the inputs (size of TAM, customer acquisition cost, churn) are guesses

Three myths to drop:
- Myth 1: Valuation is an objective search for true value. (No - it depends on perception, time, and willingness to pay.)
- Myth 2: A good valuation gives you a precise number. (No - it gives you a range.)
- Myth 3: A more quantitative model is a better model. (No - garbage in, garbage out. The assumptions matter more than the spreadsheet.)
### The 3-step valuation process
1. Understand the business. The business model, KPIs, revenue and cost forecasts (lecture 3 stuff).
2. Pick the right valuation method. Different methods fit different stages.
3. Apply the method to the forecast and get a range. Then negotiate.
	- the forecast could be made on sales, EBITDA, earnings, net working capital, capital expenditures...

The valuation has to align with the entrepreneur's narrative (TAM, conquering plan, product-market fit). In public markets there's almost no narrative - it's mostly financial math. In entrepreneurial finance, narrative drives the numbers.
- TAM = Total Addressable Market
### Unicorns
A unicorn is a private company worth more than $1 billion at its latest valuation. Belgium has 7 unicorns: Collibra (the first), Team Blue (Combell + TransIP merger), Odoo (Wallonia), Deliverect, Aiko, iCare (predictive maintenance using AI on industrial machines), and a cybersecurity firm.

OpenAI just raised $110 billion in their latest round, putting their post-money valuation at around $800 billion. For comparison, the biggest IPO ever was Saudi Aramco at $25 billion. So OpenAI raised more than 4x the biggest IPO of all time. This is absurd by historical standards.
### Equity value vs. enterprise value
Important distinction, will come back many times:
- Equity value = value of all the shares (what shareholders own)
- Enterprise value (EV) = value of the entire business operation (assets and operations), regardless of how it's financed
- Formula: $EV = Equity + Debt - Cash$
- Or: $Equity = EV - Debt + Cash$

Why the formula works: if you wanted to buy the entire business, you'd pay for the equity AND take on the debt (so you add debt). But the company also has cash on hand, which you'd get for free as the new owner (so you subtract cash). The result is what the operating business itself is worth.

Market capitalization is just the equity value of a publicly traded company (number of shares x current price).
### Capitalization tables (cap tables)
A cap table is a spreadsheet (or in Belgium, a paper book at the notary - by law) that tracks who owns what:
- Who owns which shares
- What type of shares (common, preferred series A, preferred series B, ...)
- How many shares
- At what price they were bought

Equity is divided into classes of stock. Some classes have different voting rights, different liquidation preferences, etc. Each new investment round usually creates a new class.

Typical structure of a cap table after a few rounds:
- Common stock: founders, employees (one share, one vote, no special rights)
- Series A preferred: investors from the first round, with special clauses
- Series B preferred: investors from the second round
- Options outstanding: shares not yet issued but reserved for employees (vesting, stock options)
- Fully-diluted shares: outstanding shares + all shares that could be created by converting options/convertible debt
### Pre-money vs. post-money valuation
This is one of the most important distinctions in entrepreneurial finance.
- Pre-money value = the value of the company BEFORE the new money is injected
- Post-money value = pre-money + the new investment
- Always: $Post\ money = Pre\ money + Investment$

If you're an entrepreneur and an investor says "I'll invest 250k at a 1M valuation":
- If 1M is pre-money: post-money = 1.25M, investor gets 250k/1.25M = 20%
- If 1M is post-money: pre-money = 750k, investor gets 250k/1M = 25%
- As the entrepreneur, you ALWAYS want pre-money (you give up less equity)

Example - the Bakelite company (Leo Baekeland, 1909): 
- Total starting capital was 330k euros. Baekeland put in 110k for the IP, his friend put in 220k cash and got 43% of the company.
- Post-money value = 220k / 43% = 512k (this is the "implied" 100% value)
- Pre-money value = 512k - 220k = 292k (what the company was worth before the friend's money)
- Value of just the IP = 292k - 110k (Baekeland's own cash) = 182k
### Some terminology to know
- Market cap: shares outstanding x price per share = equity value
- Pre-money / post-money: see above
- Price per share: equity value / total number of shares
- Shares outstanding: all shares issued and held by investors
- Fully-diluted shares: shares outstanding + everything that could become a share (options, convertibles)
- Cash-on-cash multiple: total cash returned at exit / total cash invested. Investors brag about this ("3x cash-on-cash"), but it ignores time. A 2x in 1 year is much better than a 3x in 10 years - that's why IRR (internal rate of return) is the better metric.
### Dilution
Dilution = your percentage ownership of the company decreases because new shares are issued to new investors.

Example: you own 100 shares = 100% of the company. New investor buyed s 25 new shares. Now there are 125 shares total, you still own 100 of them, but your percentage droppfrom 100% to 80%.

The Travis Kalanick (Uber) story: he raised so aggressively that he diluted himself massively. When investors wanted him gone, his ownership wasn't enough to defend himself, and the board voted him out of his own company. In Belgium, if you hold less than 25% of voting rights, investors can vote you out.

But dilution is not necessarily bad. Owning 20% of a $1B company is much better than owning 100% of a $1M company. The key question: is the price per share going up between rounds? If it is, you're being diluted in percentage but enriched in absolute value.

Practical observation: dilution shrinks at later stages. Series A typically dilutes ~27%, Series B ~20%, Series C less, Series D ~12%. Why? Because as the company derisks (it has a product, it has revenue, it scales), valuation goes up dramatically. To raise the same amount of money you give up less equity.
### Why valuation determines how much equity you give up
Once you know the value of your company and how much you need to raise, you automatically know what % equity you should give up:
- Company worth 1M, raise 100k -> you give 10%
- Company worth 1M, raise 250k -> you give 20% (post-money: 1.25M, 250k/1.25M = 20%)
# Valuation methods
Five methods exist (one is useless):
- Book value method - useless for startups (they don't own much)
- Discounted cash flow (DCF) - widely used for mature firms, problematic for startups
- Probability-adjusted DCF - DCF improved for stage-gated businesses (especially biotech)
- Multiples (relative valuation) - quick and easy, the most widely used "rule of thumb"
- Venture capital (VC) method - the practitioner's method, the best one for startups
### 1. Discounted cash flow (DCF) method
Core idea: a company is worth the sum of all its future free cash flows, discounted back to today. Money in the future is worth less than money today (inflation, opportunity cost, risk).

Formula: $Firm\ value = \sum_{t=1}^{\infty} \frac{FCFF_t}{(1+r)^t}$
- $FCFF$ = Free Cash Flow to the Firm (operations cash minus investments)
- $r$ = discount rate (the WACC)
- $t$ = the year in the future
- This gives the enterprise value. To get equity value, subtract debt and add cash.
##### How to compute FCFF
Same structure as in lecture 3:
- EBIT
- + Depreciation and other non-cash expenses
- = EBITDA
- - Change in net working capital (NWC)
- = Cash flow from operations
- - Investments (CapEx)
- = Free cash flow to the firm (FCFF)
##### What is the discount rate r?
$r = WACC$ (Weighted Average Cost of Capital).

A company is financed by equity and debt. These have different costs:
- Equity holders demand high returns (VCs want 30-50%+, public investors want ~10%)
- Debt holders demand lower returns (banks want 2-5%) because they get paid first if the company goes bankrupt

WACC formula: $WACC = \frac{E}{E+D} \cdot r_E + \frac{D}{E+D} \cdot r_D \cdot (1-T)$
- $r_E$ = required return on equity
- $r_D$ = interest rate on debt
- $T$ = tax rate
- The $(1-T)$ part is the tax shield. Interest is tax-deductible: if you pay 100 in interest, your taxable profit drops by 100, so you save $100 \cdot T$ in taxes. So the effective cost of debt is lower than the headline interest rate.

Example: equity 200k at 30% required return, debt 100k at 10% interest, tax rate 40%.
$WACC = \frac{200}{300} \cdot 30\% + \frac{100}{300} \cdot 10\% \cdot (1-40\%) = 22\%$
##### Step-by-step DCF example
Forecast: 5 years of free cash flows. WACC = 22%.
- Year 1: -50k (year 1 we lose money)
- Year 2: -250k
- Year 3: 100k
- Year 4: 200k
- Year 5: 500k

Discount each one back to today:
$Value\ at\ t_0 = \frac{-50k}{1.22} + \frac{-250k}{1.22^2} + \frac{100k}{1.22^3} + \frac{200k}{1.22^4} + \frac{500k}{1.22^5} = 121k$

So the pre-money value WITHOUT terminal value is 121k euros.
##### The terminal value (continuing value)
The company doesn't stop after year 5. It continues making cash flows forever. We need a way to capture all future cash flows after year 5 in a single number.

The mathematicians have a formula (assumes constant growth rate g forever after year 5):
$TV = \frac{FCF_5 \cdot (1+g)}{WACC - g}$

This gives the terminal value AT THE END OF YEAR 5. To bring it back to today, you discount it again by 5 years.

Example: $FCF_5 = 500k$, growth rate g = 2%, WACC = 22%.
$TV = \frac{500k \cdot 1.02}{22\% - 2\%} = 2.55M$
Then discount: $\frac{2.55M}{1.22^5} = 943k$

Total pre-money valuation = 121k (first 5 years) + 943k (terminal value) = ~1.06M.

If a VC invests 200k, post-money = 1.26M, the VC gets 200k / 1.26M = ~16%.
##### The big problem with DCF for startups
Notice in the example: the first 5 years contributed 121k of value, the terminal value contributed 943k. The terminal value is roughly 90% of the total. For startups this gets even worse.

This means you're saying "this startup is worth X today because in year 6+, it will be making this much money forever." But for a startup, it's nearly impossible to predict year 6 cash flows. You don't even know if the company will exist in year 6.

For mature firms (Coca-Cola, McDonald's), the first 5 years dominate the valuation, so DCF works fine. For startups, DCF puts almost all the weight on a far-future scenario you can't reasonably predict.

Other DCF problems:
- It's static - assumes the firm always succeeds (no failure scenarios)
- It doesn't model future dilution. If you'll need to raise more capital, your future ownership of those terminal cash flows will be smaller than today's, and DCF ignores this completely.
### 2. Probability-adjusted DCF method
This is DCF, but better. Instead of assuming "the firm will succeed and produce these cash flows", you model "the firm has a probability p of succeeding, and a probability (1-p) of failing (cash flow = 0)."

This is especially natural for biotech, where drug development happens in distinct phases:
- Phase 1: lab work + small animal testing
- Phase 2: small-scale human testing (5-30 patients)
- Phase 3: large-scale human testing (300-3000 patients)
- FDA approval
- Product launch

Each phase has a known historical success rate. You can't skip phases - testing on 3000 healthy people without prior validation could just kill them.
##### Conditional vs. unconditional probabilities
Conditional probability: "given that I am in phase 2, the chance I'll succeed and reach phase 3 is 45%."

Unconditional probability: "starting from phase 1, the chance I'll eventually reach phase 3 is 30% (= 67% chance to pass phase 1 x 45% chance to pass phase 2)."

For valuation, you use the UNCONDITIONAL probabilities. They are what an investor today (before any phase has happened) actually faces.
##### How to compute it
For each year's expected cash flow:
- Risk-adjusted CF = expected CF x unconditional probability of reaching that phase
- Then discount with the discount rate

Drug development example:
- Costs: phase 1 = $9M (1 year), phase 2 = $22M (2 years), phase 3 = $80M (3 years), FDA approval = $3M (2 years), launch = $90M
- Success rates: phase 1: 63%, phase 2: 42%, phase 3: 85%, FDA approval: 95%
- After launch, the drug generates rising sales for ~10 years (during patent protection), peaks, then declines as generics enter.
- Required return: 20% (lower than usual VC hurdle, because we're already accounting for failure risk in the probabilities).
##### Why the discount rate is lower with probability adjustment
Normal VC required return: $r = r_F + \beta(r_M - r_F) + effort + illiquidity + optimism\ premium$

The "optimism premium" exists in normal DCF because the entrepreneur's forecasts are systematically too rosy. With probability-adjusted DCF, you've already discounted for the chance of failure, so you don't need to add the optimism premium on top. Otherwise you'd be double-counting risk.
##### Value evolution across phases
The value of the company GROWS as you successfully pass each phase:
- Before phase 1: low value (lots of failure risk ahead, lots of expenses still to incur)
- After phase 1: higher value (one risk eliminated, you're closer to revenue)
- After phase 2: even higher
- After launch: peak value (no more development risk, sales start)
- After year ~10: value declines (less remaining patent life, less future revenue ahead)

This is exactly why later-stage rounds dilute less - the company is genuinely worth a lot more at each stage.
### 3. Multiples method
The "comparable companies" / "rule of thumb" method. Quickest and most widely used, especially for sanity checks.

Core idea: how do you value a house? You look at the price-per-square-meter of similar houses in your neighborhood and apply it to yours. Same logic for companies, but using different metrics:
- Profit (earnings) -> P/E multiple
- EBITDA -> EV/EBITDA multiple (most common for private companies)
- Sales -> P/S multiple or EV/Sales multiple
- Operational KPIs (number of users, ARR for SaaS, etc.)

The 3-step approach:
1. Find comparable companies (peer group)
2. Compute their multiples (e.g., what's the average EV/EBITDA for SaaS companies?)
3. Apply that multiple to your company's metric to get its value

Example: forecast $5M sales and $0.5M earnings in year 5. Find similar companies with $200M sales and $40M earnings, sold for $400M. Their P/S = 2, P/E = 10. Applied to our forecast: P/S valuation = $10M, P/E valuation = $5M. The truth is somewhere in this range. Then discount this back to today.
##### Why multiples differ across industries and time
- Industry differences: tech companies trade at higher EV/EBITDA than construction companies. Tech has low capex, high margins, scalable. Construction has heavy depreciation eating into net profit. Pharma has patents that protect them from competition for 20 years - investors pay big multiples for that.
- Time differences: in 2009 (post-crisis) the S&P 500 traded at P/E ~10. Today it's ~22. Same companies, very different prices, depending on market mood.
- Company-specific drivers of higher multiples: hot sector (AI), strong serial-entrepreneur team, working product, customer traction.
- Drivers of lower multiples: declining sector, commoditized product, no differentiation, weak team, running out of cash.
##### Trading multiples vs. transaction multiples
- Trading multiples = multiples of publicly listed companies (you can compute these yourself from stock prices). Useful for valuing companies close to IPO.
- Transaction multiples = multiples paid in actual M&A deals or VC rounds. Better for private startups, but harder to find data (deals are often confidential).
##### The peer group game
The choice of comparables can swing the valuation enormously. If you're an investment banker valuing a car startup, do you include Tesla in your peer group?
- Tesla trades at ~5.4x sales (markets value it as a robotics/AI company, not just a car maker)
- Ford trades at ~0.2x sales
- Including Tesla pulls the average multiple way up

If you're working FOR the seller, you include Tesla. If you're working FOR the buyer, you exclude it. This is why "the right peer group" is heavily contested.
##### Problems with multiples
- Earnings of startups are often negative (P/E doesn't work)
- Revenue may be tiny or zero (P/S barely works)
- Cash flow patterns are too volatile to compare to mature firms
- "Comparable companies" are never truly comparable
- Multiples drift over time with market sentiment
### 4. Venture capital method
This is the method VCs actually use. It blends the discounting logic of DCF with the practicality of multiples, and accounts for dilution.

Core question: what is a VC willing to pay today to get a target return on a future exit?

The 3-step logic:
1. Pick an expected exit year (typically 5-7 years).
2. Estimate what the company will be worth at exit (using a multiple on projected revenue/EBITDA at that point).
3. Discount that exit value back to today using the VC's target return (the "hurdle rate"), NOT the WACC.

The hurdle rate (target IRR) is a flat number the VC chooses, like 30%, 40%, 50% per year. It already bakes in everything (risk, illiquidity, effort, optimism). You don't compute a CAPM beta.
##### Simple VC method example
- Year 5 sales projection: 10M euros
- Industry P/S multiple: 5
- Exit value in year 5: 50M euros
- Required hurdle rate: 50%/year
- Discount back: $\frac{50M}{(1+50\%)^5} = 6.6M$ -> this is the post-money value today
- VC invests: 2M
- VC's ownership: 2M / 6.6M = 30% of the company
- Pre-money value: 6.6M - 2M = 4.6M
##### Required IRR by stage (US data)
Higher risk = higher required return:
- Seed/startup: 50-70%
- Early stage: 40-60%
- Expansion: 30-50%
- Mezzanine/later: 25-35%
- The required return drops as risk drops at later stages.
- A 50% required return means the VC expects company value to grow ~7x over 5 years.
##### Full VC method with dilution (the 6-step approach)
This is the real version. The simple version above ignored the fact that more funding rounds will happen between now and exit, which will dilute the VC's ownership.

Setup:
- Estimated exit: year 7
- Net income at exit: $25M
- Industry P/E: 11
- Existing shares: 1M of common stock
- VC target return: 50%/year
- VC's investment now: $3.5M

Step 1 - Estimate terminal value:
$Terminal\ value = 25M \times 11 = 275M$

Step 2 - Discount terminal value to today:
$DTV = \frac{275M}{(1+50\%)^7} = 16.1M$

Step 3 - Required Final Percentage (RFP) at exit, IGNORING future dilution:
$RFP = \frac{Investment}{DTV} = \frac{3.5M}{16.1M} = 21.74\%$

Step 4a - How many new shares does the VC need (no dilution)?
You can't just take 21.74% of 1M existing shares = 217k shares, because then the VC would own 217k out of 1.217M total = 17.8%, not 21.74%. You have to ADD shares, and the VC's new shares need to equal 21.74% of the NEW total.
$New\ shares = \frac{ownership\ \%}{1 - ownership\ \%} \times old\ shares = \frac{0.2174}{0.7826} \times 1M = 277,778\ shares$

Or equivalently: $Total\ shares\ after = \frac{old\ shares}{1 - ownership\ \%} = \frac{1M}{0.7826} = 1,277,778$
##### Step 4b - Allowing for future dilution (the retention ratio)
Between now and exit, the company will probably do another round and grant more options. Each dilutes the current VC.

Assume:
- Series B will dilute by 20% (new shares = 20% of existing)
- Future option pool will be 25% of common stock

Retention ratio = the fraction of today's ownership that survives all future dilutions:
$Retention = \frac{1}{1.2} \times \frac{1}{1.25} = 0.667$ (66%)

Meaning: if the VC owns X% today, they will only own 0.66 * X% at exit.

Step 4c - Required CURRENT ownership (so the VC ends up with 21.74% AT EXIT after dilution):
$Required\ current\ \% = \frac{21.74\%}{66\%} = 32.94\%$

The VC needs to buy 32.94% today so that after dilution they're at 21.74% at exit.

Step 4d - Number of new shares with dilution:
$New\ shares = \frac{0.3294}{0.6706} \times 1M = 491,202\ shares$

Step 5 - Price per share:
- Without dilution adjustment: $\frac{3.5M}{277,778} = \$12.60$ per share
- With dilution adjustment: $\frac{3.5M}{491,202} = \$7.13$ per share

So the price per share is almost half when you account for dilution. The VC pays the same total ($3.5M) but gets more shares because they need a bigger initial percentage.

Step 6 - Pre and post money valuations (with full dilution):
- Pre-money = old shares x diluted price = 1M x $7.13 = $7.13M
- Post-money = total shares x diluted price = 1.491M x $7.13 = $10.63M

Note: post-money = pre-money + investment, always. $7.13M + $3.5M = $10.63M. Checks out.
##### Acceptable vs. unacceptable dilution
Dilution is OK if the price per share keeps going up between rounds. You own a smaller % of a much bigger pie - the absolute value of your stake grows.

Dilution is BAD ("smashdown") if new shares are sold at the same or lower price than the previous round. Your percentage drops and the value drops too. This is what happens in down rounds when valuations crash.

The entrepreneur's goal: drive the company's value up faster than the share count goes up. Total pie grows faster than number of slices.
# Wrap-up and conclusions
- DCF in early rounds overestimates value because it ignores the probability of failure. The 35% return that VCs claim to want only compensates for business risk, illiquidity, and effort - not failure.
- Probability-adjusted DCF is much better for staged businesses (biotech, deep tech). Still limited.
- Multiples are great for sanity checks and quick estimates, but the choice of peer group can swing the result hugely.
- The VC method is the practitioner's go-to for startups because it explicitly handles staging and dilution. But it still relies on multiples to estimate the exit value, so the same peer-group problems sneak in through the back door.
- There is no one-size-fits-all method. Use the one that fits the stage and context.

Final reminders:
- Valuation is as much an art as a science. Experience matters a lot.
- Intangibles (team track record, IP value, market size, narrative) drive everything for early-stage companies.
- The actual price paid in a deal depends on bargaining power and market sentiment, not just the model.
- Valuation is just one part of deal structuring. The legal terms (liquidation preferences, anti-dilution, etc.) often matter more than the headline price - that's lecture 6.