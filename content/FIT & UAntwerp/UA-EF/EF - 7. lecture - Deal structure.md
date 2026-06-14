This lecture is about what's in a term sheet beyond the valuation. Two weeks ago we talked about price (valuation). Now we talk about TERMS. The big takeaway: valuation alone is meaningless. The terms in the contract often matter more than the headline price.

> "You name the price, I'll name the terms."

This is a common phrase among VCs. They are fine paying a high price (great for the press release) IF they can get terms that protect their return. From the entrepreneur's side, accepting strict terms in exchange for a high valuation is often a much worse deal than accepting a lower valuation with friendlier terms.

Fun fact illustrating this: 31% of unicorns globally are valued at exactly $1 billion. Not 800M, not 1.2B - exactly 1B. Why? Because being a "unicorn" gets you press coverage, talent attraction, and prestige. So entrepreneurs accept brutal terms to push the headline number to exactly $1B. It's mostly cosmetic.
### What VCs are actually trying to achieve with terms
- Protect their downside (if the firm fails, get money back first)
- Enhance their upside (if the firm grows modestly, capture a disproportionate share)
- Reduce information asymmetry (monitoring rights, board seats)

Terms split into two categories:
- Economic terms: payout structure, type of security, dividends, liquidation preferences, option pool
- Control terms: protective provisions, board composition, information rights, vesting, voting rights
### Three rules of financing (recap)
- Reduce the need for external financing as much as possible
- Use the cheapest financing option available
- All investors look for return - even "impact investors" usually want returns. If you raise money, you owe something to the other party.
# Investment staging
Staging is when the investor splits their commitment into multiple tranches, each released only when specific milestones are achieved. It's not technically a "term" but it's the first protective mechanism.

Two benefits for the VC:
- Reduces risk: if you commit 1M but only release 500k now and 500k later, and the company fails after 6 months, you only lose 500k
- Boosts IRR: the second 500k stays invested elsewhere (earning returns) until the milestone is hit

Benefit for the entrepreneur:
- Less dilution: only the first tranche dilutes you now. The second tranche dilutes you later, when (hopefully) the valuation is higher.
### Q-Bizz example - simple case (no future dilution)
Setup:
- Founder owns 1M shares
- Company will be sold for 25M euros in 4 years (terminal value)
- Q-Bizz needs 3M euros now
- VC (Vulture Ventures) requires 50% IRR

Step 1 - Post-money value today: $\frac{25M}{(1+50\%)^4} = 4.94M$
Step 2 - Pre-money value today: $4.94M - 3M = 1.94M$
Step 3 - VC ownership: $\frac{3M}{4.94M} = 60.75\%$
Step 4 - Number of shares: founder keeps 1M = 39.25%, so total shares = $\frac{1M}{0.3925} = 2.55M$. VC gets $2.55M - 1M = 1.55M$ shares. Price per share = $\frac{3M}{1.55M} = 1.94$ euros.

The price per share is the number to track - it determines whether future rounds will be "down rounds" (which trigger anti-dilution provisions).
### Q-Bizz example - with future dilution (the realistic case)
Q-Bizz now realizes they actually need 4.6M, not 3M. They split it:
- Round A: 3M from Vulture Ventures now
- Round B: 2M from Slowtrain Investors in 2 years (also wants 50% IRR)

Slowtrain's calculation (working backward from year 4):
- Post-money for Slowtrain at year 2: $\frac{25M}{1.5^2} = 11.11M$
- Slowtrain's stake: $\frac{2M}{11.11M} = 18\%$
- Existing shareholders' retention ratio: $1 - 18\% = 82\%$

This means whatever stake Vulture Ventures owns after round A, they'll keep only 82% of it after round B.

If Vulture Ventures ignored future dilution:
- They bought 60.75% in round A
- After round B dilution: $60.75\% \times 82\% = 49.8\%$ at exit
- Their proceeds: $49.8\% \times 25M = 12.5M$
- IRR: $\sqrt[4]{\frac{12.5M}{3M}} - 1 = 43\%$ - NOT the 50% they wanted

To actually hit 50%, Vulture Ventures needs to compensate for future dilution upfront. They calculate what % they need at EXIT, then divide by the retention ratio:
- Required at exit: $\frac{3M \times 1.5^4}{25M} = 60.75\%$
- Required NOW: $\frac{60.75\%}{82\%} = 74.09\%$

So Vulture Ventures buys 74% today. After Slowtrain dilutes everyone by 18%, Vulture's stake drops to $74\% \times 82\% = 60.75\%$ at exit, and they hit their target IRR.

This whole adjustment can be done by anti-dilution clauses (instead of demanding a bigger initial stake), which is the next topic.
# The term sheet
A term sheet is a non-binding offer from the VC to the entrepreneur. Key features:
- It's a starting point for negotiation, not a final contract
- It IS binding for some clauses: exclusivity (you can't talk to other VCs for 30 days to 10 weeks), fees and expenses, confidentiality
- Conditional on due diligence (DD): the VC will dig through your contracts, customers, suppliers, employee agreements, etc. If they find something they don't like, they can withdraw
- Lots of legal items, mostly standardized
### Types of shares
- Common (ordinary) shares: held by founders, management, employees. One share, one vote, no special rights.
- Preferred shares: held by VCs and outside investors. "Preferred" means there are special clauses attached. The clauses are negotiated.
- Preferred shares are SENIOR to common - they get paid first if the company is liquidated or sold.
- Preferred shares typically convert to common at IPO (cleaner balance sheet, public investors don't like complex share structures).
- 80% of VC investments use preferred shares. Angels barely use them (~30% of the time) - they invest earlier and don't bother with the legal complexity. Late-stage rounds almost always use preferred (lower expected returns mean you must reduce risk).
# The Arctic Technologies case
Real Swedish startup making a reprogrammable silicon chip for mobile phones. The chip can act as memory or computing depending on what the phone needs.

Funding history:
- March 2014: founders invest at common shares
- December 2014: 16M SEK round, Viking Venture Partners (VVP) invests 14M, two angels invest 1M each. Convertible preferred shares, 10% option pool. Price per share = 100 SEK.
- May 2016: VVP gives a 4M SEK bridge loan (convertible into next round at 20% discount)
- July 2016: Tech Invest Fund (TIF) sends a term sheet for round B

Round 1 valuation: post-money = 33M SEK, pre-money = 17M SEK.
### TIF's offer
- Type: Series B PARTICIPATING preferred shares (the word "participating" matters a lot)
- Price: 400 SEK per share - 4x higher than round 1
- TIF invests 36M, VVP invests 12M (4M bridge converts at 20% discount, so converts at 320 SEK/share = 12,500 shares; remaining 8M at 400 SEK = 20,000 shares)
- Total raised: 48M SEK

The bridge loan conversion: 4M SEK / 320 SEK = 12,500 shares (the 320 = 400 x 80% because of the 20% discount on the new round price).

New cap table results:
- Post-money valuation: 48M / 25.3% = ~190M SEK (so around 20M euros)
- Pre-money: 140M SEK
- Multiple from round A post-money: 4.22x in 1.5 years

In the press, this looks amazing - quadrupling valuation in 18 months. But the press doesn't know the terms.
# The 12 most contentious terms
What follows is the standard list of negotiation flashpoints, applied to the Arctic case.
### 1. Valuation
Already covered in lecture 5. Key practical advice: get more than one VC bidding ("a horse race"). Watch Dragons' Den episodes - when one investor expresses interest, others often follow because of FOMO, then they bid each other up. Pure psychology.
### 2. Company control - shareholder leverage (protective provisions)
Protective provisions are veto rights. The VC can block the company from doing certain things without their approval. Common items:
- Issuing new shares (block dilution that you didn't authorize)
- Taking on debt (debt is senior to equity, so it threatens your stake)
- Amending the articles of incorporation
- Changing the number of directors
- Paying dividends (or forcing them)
- Redeeming shares
- Mergers, acquisitions, liquidations
- Selling crucial assets (e.g., the IP/patents)
- Hiring/firing/compensating executives (controversial)

In Arctic's term sheet: 75% of preferred shareholders must consent to any of these actions. Since neither TIF nor VVP alone has 75% of preferred, they have to agree together - which gives them mutual veto power.

Frequency in practice: board representation appears in 85-95% of deals.
### 3. Company control - board composition
Boards are typically 5-7 directors. Larger boards correlate with worse stock returns (too much chit-chat, too little action). Typical balance: some VCs, some founders, some independent directors agreed by both sides.

The VC almost always (~94% of deals) gets the right to appoint at least one director. Sometimes they take an "observer" role instead - shows up to meetings, gives feedback, but no voting rights.

Boards are where major decisions are made: firing management, executive compensation, strategy. If you're an investor and you're not on the board, you're in the dark.

Arctic case: max 7 directors. TIF gets 1 director + 1 observer. VVP gets 1 director. CEO + 2 other execs from the company. Plus 2 independent directors mutually agreed. That's the structure.
### 4. Dividends
Dividends in startups aren't really "paid out" - the company has no cash. Instead, dividends accrue and get paid at exit. Often used as a "kicker" to make a deal more attractive at the last minute.

Variants:
- Cumulative: 5% per year just adds up. After 7 years you're owed 35%.
- Compounded: dividends earn dividends. After 7 years at 6% compounded, you're owed ~50%.

Arctic case: 6% per annum, compounded annually, with priority over all other share classes. Not paid until liquidity event. After 7 years at 6% compounded, the dividend obligation alone = 24M SEK on the original 48M investment.
### 5. Anti-dilution provisions
This is one of the two most impactful term categories. Two flavors:
- Non-price-based anti-dilution: e.g., right of first refusal
- Price-based anti-dilution: e.g., full ratchet, weighted-average ratchets
##### Right of first refusal (non-price-based)
When a new round happens, existing investors get the right to participate in the new round at the same conditions as the new investor, pro-rata to their current stake. If you own 20% and the company raises 1M from a new investor, you can chip in up to 200k yourself at the same valuation.

This protects your percentage but not your IRR (you have to put up new money). It's an OPTION, not an obligation. Doesn't apply to shares issued to employees or for acquisitions or for an IPO.

This is in Arctic's term sheet and is very standard.
##### Price-based anti-dilution
Activated only if a future round happens at a LOWER price per share than what the protected investor paid (a "down round"). The protected investor gets free shares to compensate.

The mechanics: it adjusts the conversion ratio of the preferred shares. Initially preferred convert 1:1 to common. If a down round triggers anti-dilution, the conversion ratio increases - so when they convert, they get MORE common shares per preferred share than they originally paid for.

Three strengths of price-based protection:
- Full ratchet (very investor-friendly, brutal for entrepreneur)
- Narrow-based weighted average (medium)
- Broad-based weighted average (most entrepreneur-friendly)
##### Full ratchet - the toxic version
Logic: "If you sell new shares at a lower price than I paid, treat it as if I had paid that lower price all along." The investor gets enough free shares so the value of their original investment is fully restored.

Example - investor A bought at $1/share and gets fully diluted with full ratchet:
- ABC Inc. has 6M shares total: 3M common + 1M options + 2M preferred A (bought at $1, total $2M, 33% stake)
- Round B: 2M new shares sold at $0.50 (= 1M raised, post-money 4M)
- Conversion ratio: $\frac{Old\ price}{New\ price} = \frac{1}{0.5} = 2$
- A's preferred convert at 2x: 2M preferred -> 4M common
- New cap table: 3M common + 1M options + 4M (A converted) + 2M (B preferred) = 10M total
- A's stake: 4/10 = 40% (UP from 33%!)

So even in a down round, A's percentage went UP from 33% to 40%. The price per share is now $0.50 across the board, so A's holdings = 4M x $0.50 = $2M (their original investment, fully preserved).

The disaster scenario: if the down round is even worse (e.g., post-money 3M instead of 3.8M), the founder gets WASHED OUT. The math forces so many free shares to A that the founder's percentage drops to ~0.
##### Why full ratchet is bad even when there's no actual down round
Full ratchet can pressure entrepreneurs into accepting bad rescue financing or selling early because the threat of activation is too dangerous. It also makes the valuation meaningless - it's like the VC saying "fine, I'll pay 4x the previous price, but if anything goes slightly wrong, I get my money back via free shares."

Modern stats: full ratchet is used in only 1% of deals today. Used to be the standard 20-30 years ago. Entrepreneurs got smarter.
##### Narrow-based weighted average
Better for the entrepreneur. Adjusts the conversion ratio based on how big the down round actually was relative to existing preferred shares.

Formula:
$Revised\ conversion\ price = Old\ price \times \frac{PreMoney\ Preferred + new\ shares\ at\ OLD\ price}{PreMoney\ Preferred + new\ shares\ at\ NEW\ price}$

Same ABC example: old price = $1, pre-money preferred = 2M shares. At old price ($1), new investor would have gotten 1M shares for the 1M they invested. At new price ($0.50), they get 2M shares.
$Revised\ price = 1 \times \frac{2M + 1M}{2M + 2M} = 1 \times 0.75 = \$0.75$
Conversion ratio = $\frac{1}{0.75} = 1.33$
A's 2M preferred convert to 2.67M common. A's stake: 2.67M / 8.67M = 31% (down from 33% but partially protected).
##### Broad-based weighted average
Even better for the entrepreneur. Uses ALL fully-diluted shares (common + options + preferred), not just preferred:
$Revised\ price = 1 \times \frac{6M + 1M}{6M + 2M} = \$0.875$
Conversion ratio = 1.143. A converts to 2.29M common. A's stake: 2.29M / 8.29M = 28% (down from 33%, less protection but more fair).

Comparison of the same down round under different protections:
- Full ratchet: A goes from 33% to 40%
- Narrow-based: A goes from 33% to 31%
- Broad-based: A goes from 33% to 28%
- No protection: A goes from 33% to 25%
##### How common is anti-dilution in practice?
- 60% of deals include some anti-dilution
- Of those, almost all are weighted-average (narrow or broad)
- Only 1% are full ratchet
- More common in later rounds (Series C/D) because the upside is smaller and protecting the investment matters more
### 6. Liquidation preferences
The OTHER hugely impactful term category. Determines how proceeds are split when the company is sold or liquidated. NOT triggered by capital increases - only by exits.

Two components: the initial preference, and the participation rights.
##### Initial liquidation preference
"I get my money back first." Variants:
- 1x: get original investment back before anyone else
- 2x or 3x: get 2-3x your original investment back before anyone else
- May include accrued dividends
##### Participation rights
What happens to whatever's left AFTER the initial preference is paid?
- Full participation: investor gets the preference AND a pro-rata share of the rest. "Double dip."
- Capped participation: same as full, but capped at a multiple (e.g., 3x total return).
- Non-participating: investor gets EITHER the preference OR pro-rata, whichever is bigger. Single dip.
##### The 4-scenario example
A VC invests 5M for 40% of the company. So post-money = 12.5M. Founders own 60%.

Company sold for 8M, 15M, or 30M. What does the VC get?

Scenario 1 - Common shares (no preference):
- 8M -> 40% x 8M = 3.2M
- 15M -> 6M
- 30M -> 12M
- VC just takes their pro-rata share. Loses money on a down exit.

Scenario 2 - 1x non-participating preferred (convertible preferred):
- VC chooses each time: take the 5M preference, OR convert to common and take 40%.
- 8M sale: 40% = 3.2M, vs preference of 5M -> takes 5M.
- 15M sale: 40% = 6M, vs preference of 5M -> takes 6M (converts to common).
- 30M sale: 40% = 12M, vs preference of 5M -> takes 12M.
- The breakeven: 40% x exit = 5M -> exit = 12.5M (post-money). Above 12.5M VC always converts.

Scenario 3 - 1x full participating preferred:
- VC ALWAYS takes the 5M preference, THEN takes 40% of the remaining.
- 8M: 5M + 40% x (8M - 5M) = 5M + 1.2M = 6.2M
- 15M: 5M + 40% x 10M = 9M
- 30M: 5M + 40% x 25M = 15M
- The VC always wins. Founders always lose 5M off the top.

Scenario 4 - 1x participating preferred with 2x cap:
- Same as scenario 3, but capped at 2x = 10M total.
- 8M: 5M + 40% x 3M = 6.2M
- At what point does the cap kick in? 5M + 40% x (exit - 5M) = 10M -> exit = 17.5M
- After 17.5M, the cap binds at 10M.
- Above 25M, just taking 40% beats the cap (25M x 40% = 10M, then > 10M). VC converts.
##### The zone of indifference
With capped participation, there's a range where the VC's payout is FLAT. In the example, between 17.5M and 25M, the VC gets exactly 10M no matter what.

This creates misaligned incentives: from 17.5M to 25M, the founder's payout grows but the VC's stays flat. The VC's IRR DROPS over time in this zone (same payout, longer wait). So the VC wants to sell IMMEDIATELY at 17.5M, while the founder wants to wait and grow. This is exactly when drag-along rights become dangerous.
##### Stats on liquidation preferences
- Almost all VC deals have at least 1x preference
- 1x is by far the most common multiple
- Full participation is fading: was >50% pre-COVID, now ~10-12%
- Stacked preferences across multiple rounds get insanely complicated. Each round of liquidation preferences eats through the exit value before common shareholders see anything.
##### Arctic case liquidation preferences
TIF's offer:
- 2x liquidation preference on the 48M investment
- 6% annual compounded dividend, accrued
- After 7 years, dividends owed: $48M \times (1.06^7 - 1) = 24M$
- Liquidation preference: $48M \times 2 = 96M$
- Total before anyone else gets anything: 120M SEK
- THEN Series A's 1x preference kicks in: another 16M
- Total before common gets anything: 136M
- AND Series B is participating, so they ALSO take 25.6% of whatever goes to common

So if Arctic sells for less than 136M SEK (about 14M euros), the founders get ZERO. Even though they nominally own ~30% of the company. The valuation is a fiction - the terms are everything.
### 7. Stock option pool
A reserve of shares set aside for employee compensation. Typically 10-25% of equity.

The dilution trick: when does the pool get topped up? VCs usually demand it happens BEFORE their investment, so all existing shareholders get diluted to make room for the pool, but the new VC doesn't. If it were topped up after, the VC would also be diluted. Standard in ~50% of deals.

Distribution within the pool (typical US tech mid-stage):
- CEO: 5-10% of company
- CFO: 1-2%
- VPs (each): 1-3%
- Directors (each): under 0.5%
- Other employees: under 0.25%

Arctic case: TIF demands 25,000 more shares added to the option pool, taking it from 33,000 to 58,000.
### 8. Vesting for founders
Vesting = the founder doesn't actually own all their shares yet. They earn them over time by sticking around.

Standard structure: 4 years with a 1-year cliff. Cliff = if you leave before year 1, you get nothing. After year 1, you've earned 25% (or 1/4 of all shares). Then it vests monthly until year 4.

Why VCs demand it: if a founder owns 50% of the company and walks away after 1 year, the VC is stuck with a company that has no operator but still 50% locked up with someone who's not contributing. Vesting solves this.

Founders hate it (emotionally): "I built this company, why do I have to earn back shares I already own?" But standard practice. VCs won't invest unless 20-30% of founder shares are subject to vesting.

Arctic case: 75% of founder shares vest monthly over 4 years. Each month they get 1/48 of 75%.
##### Bad leaver clause
What happens if the founder leaves for "bad" reasons?
- Gross misconduct (fraud, etc.)
- Joining a competitor
- Lying

Bad leaver shares can be repurchased at nominal value (essentially zero - 0.1% of share value, like 1 cent per share) or at the original price paid. A "good leaver" (illness, mutual agreement) typically gets bought out at 50-70% of value.
### 9. Pay-to-play
"Keep paying to keep playing." If an existing investor doesn't participate pro-rata in future rounds, they LOSE their preferred rights and their preferred shares get converted to common.

Used to force existing investors to keep supporting the company. Especially common when the current round includes a less-known fund, because:
- A well-known VC has plenty of dry powder; they're likely to follow on
- A lesser-known VC might run out of money - pay-to-play forces them to commit
- If a VC declines to follow on, it signals BAD news to other VCs ("they know something we don't")

Arctic case: TIF's full ratchet only applies if the Series B investor invests their pro-rata share in any down round. So pay-to-play is conditioned on actually playing.

Trending up in usage, especially with the rise of smaller VC funds.
### 10. Drag-along and tag-along
##### Drag-along
Lets a majority (or in some cases just the preferred shareholders) FORCE all other shareholders to sell on the same terms.

Why VCs want this: imagine an acquirer wants to buy 100% of the company at 80M, but the founder thinks the company is worth more and refuses. Without drag-along, the deal dies. With drag-along, the VC can force the founder to sell.

Combined with the zone of indifference from capped participation, drag-along becomes really painful for founders: the VC is incentivized to sell at the LOW end of the indifference zone, drags everyone with them, and the founder gets a worse outcome than waiting.

Used in ~83% of deals. Sometimes the threshold is 50% of preferred shareholders, sometimes 65%.

Arctic case: 65% of preferred shareholders required. Since TIF + VVP together have 100% of preferred, they can drag-along the founders.
##### Tag-along
The opposite. Lets MINORITY shareholders join a sale that majority shareholders are doing. Protects the small guys.

Example: a big VC sells their 40% to another VC at great terms. The new buyer doesn't want to buy YOUR shares because they want you to keep running the company. With tag-along, you can force the buyer to buy your shares too, or call off the deal.

In ~66% of deals.
### 11. Milestones and tranches
Already discussed under staging. Capital is committed but disbursed only on hitting milestones.

Problems with tranching:
- What milestones are realistic for an early-stage company? Revenue can be gamed (do consulting that doesn't actually advance the product). Product milestones can be reached while the strategy obviously needs to pivot.
- Creates uncertainty for employees about real runway
- Distracts the entrepreneur from real strategy work
- Milestones change as the business evolves
- "Just missed" outcomes are messy

Tranching works best for biotech (clear stage gates) and is used in 20-25% of deals.

Arctic case: TIF offers 30M now + 18M in tranche 2 IF milestones are met. The milestones are NOT specified in the term sheet, which is a major red flag.
### 12. Exit provisions
Various rights related to forcing or shaping an exit:
- Redemption rights: company must buy back the preferred shares at original price + carry, often used by funds nearing end of life who need to exit
- IPO rights: minimum offering size, choice of underwriter, piggyback rights (investors can register their shares whenever the company does an IPO)
- Approve/block sale of company
### Other terms (briefly)
- Information rights: more detailed financial info than what's published
- Non-compete clauses
- Right of first refusal (already covered)
- Director's fees (covers VC's expenses for attending meetings)
- Prohibition on management share sales
- Exclusivity period (TIF asked for 10 weeks, which is long - 30 days is more typical)
- Liability if company pulls out: should have a CAP on the amount, otherwise dangerous
- Reps and warranties: founders warrant they're telling the truth. Reasonable.
# What happened with Arctic Technologies?
The Arctic founders + VVP rejected the TIF term sheet. Even though the valuation was 4x the previous round, the terms were too harsh:
- Full ratchet anti-dilution (toxic)
- 2x participating liquidation preference + 6% compounded dividend (founders get nothing under 136M SEK exit)
- Tranching with unspecified milestones
- 10-week exclusivity (long)

Instead, VVP did another internal round at a lower valuation but with friendlier terms (no dividends, no liquidation premium). The company kept growing, attracted a different institutional investor later, and is still operating today with strong reputation in the industry.

The lesson: the headline valuation doesn't matter. A 4x valuation increase with these terms was worse than a smaller increase with clean terms.
# Value adding and monitoring (post-investment)
Once the VC has invested, what do they actually do? Studies asked many VCs, ranked answers:
1. Meet entrepreneurs regularly, give feedback and direction (depends entirely on quality of VC)
2. Sit on the board for monitoring + influence
3. Help with raising additional financing (network, intros)
4. Recruit key executives (CEO, CFO especially)
5. Bring in new R&D talent (less common)

This is why entrepreneurs care about VC reputation - bad VC meetings provide negative value.
# Exit routes
- Trade sale to another company: most common. Strategic buyer integrates the company.
- Sale to another PE firm: financial buyer (often "buy and build" strategy).
- IPO: maybe 10% of exits, declining over time.
- Write-off: company fails.
- Management buyback: founders buy back the VC's stake.

Trade sale is the dominant route - the company stays private but changes ownership.
# Wrap-up
- Value is not static. The same valuation can produce wildly different payouts depending on terms.
- Anti-dilution and liquidation preferences are the two biggest impact terms. They can completely shift who gets what.
- Preferred shares typically shift value FROM entrepreneur TO investors.
- Saying Belgium has 7 unicorns means little without knowing the term sheets behind them.
- Always seek multiple offers - bargaining power is everything.
- Get legal advice. Term sheets are dense, technical documents and one bad clause can wipe you out.
- Try to align incentives across all parties. A term sheet that destroys the founder's incentive to push past 17M ultimately hurts everyone.