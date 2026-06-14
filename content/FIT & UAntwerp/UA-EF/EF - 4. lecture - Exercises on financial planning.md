All financial planning starts with the Sales forecast (I need to know what I plan to sell to be able to plan the finances).
### P&L statement
- is period-based (monthly, quarterly or yearly)
- is based on accrual principle
	- revenue is recognized, when earned (not paid)
		- so sale from March that will be paid in May is in P&L in March and in the cash flow statement in May
	- cost is recognized, when consumed (not when paid)
		- so if I buy 100 units of materials in January and use only 60 of them to manufacture my products, the P&L recognizes 60 units, whereas cash flow statement recognizes 100 (when paid to the supplier)

- the formula:
	- revenue (sales, turnover) in the defined period
		- - variable costs or COGS (materials used (not bought), direct labor, direct services)
	- = gross margin
		- - fixes cash costs (costs that exist regardless of how much I produce: salaries, marketing, rent, IT costs etc.)
	- = EBITDA (Earnings before interests, taxes, depreciation and amorization)
		- - depreciation and amortization (non-cash expense, more like accounting thing)
			- depreciation: of physical things (trucks, machinery etc.)
			- amortization: of intangible things (patents, SW licenses etc.)
	- = EBIT (operating profit), before financial decisions and taxes muddy the picture
		- - interest charges (cost of debt financing)
			- only the cost of the debt (interests), the principal repayment does not appear here (I am just returning what I got, no loss there)
		- + interest revenues
	- = Earnings before taxes OR pre-tax income (this is taxable profit)
		- - taxes
	- = Earnings after taxes OR Net income
		- this is the actual profit that shareholders earned this period

- the extra earnings are then transferred to the next period (if the company pays dividends, they are paid from this amount)
### Cash flow statement
- over the defined period: how much money has moved through the bank account and where did it come from?
- it is based on cash (if the cash did not move, it did not happen)
- a company with good-looking P&L could go bankrupt, because it may have too much cash tied up in inventory or in orders that were made, but not paid yet 

- there are two ways to build it:
	- structure 1 (theoretically correct):
		- EBIT
			- - operational taxes (basically EBIT x tax_rate) (this will probably be deducted via tax advantages below)
		- = Operational earnings
			- + deprecations (we paid them in advance, so no real money is moved - only an accounting thing) and it has been deducted from the EBIT (it needs to be "returned")
		- = OCF (Operating Cash Flow)
			- +/- change in WCR (Working Capital Requirement)
				- this is money that the operations are tying up
				- accounts receivable = customers owe us (money did not move (yet))
				- inventory = cash turned into stuff in the warehouse
				- accounts payable = free financing from suppliers (we owe them - money did not move (yet))
				- we measure the change (delta) in WCR:
					- $\Delta$WRC = AR + Inventory - AP
					- delta increases (more money are tied up) => subtract it
					- delta decreases (less money are tied up) => add it
			- - capital expenditures (CapEx = investing into new machines, construction of new buildings, upgrading trucks etc.)
				- this is the money leaving the account (in P&L it does not leave at once, but through deprecation)
		- = Free OCF (Operating Cash Flow)
			- - interest
			- + tax advantages (if we have debt, it is tax-deductible and we have to add it back)
				- tax_rate \* interest payment for the period
			- - principal repayment (repaying back the original amount borrowed (interests are a separate category))
		- = Change in cash (over the period)
			- + beginning cash (from the last period)
		- = Ending cash balance

	- structure 2 (easier, used in practice):
		- Net income (all taxes are already involved)
			- + deprecations (this is accounting thing, money was already moved when buying the asset)
		- = OCF (Operating Cash Flow)
			- $\Delta$WCR (Working Capital Requirement) - working only with the changes (deltas)
				- + increase in A/P (suppliers financing more of our operations (cash leave on our account))
				- - increase in A/R (customers owing us more = cash we don't have)
				- - increase in inventory (more cash tied in stock)
		- = Operating sources (uses) of cash
			- - net investment outlays (CapEx)
		- = Free OCF
			- + net cash from financing activities (new long-term debt raised (new money), new equity issued (investors)) minus principal repayments, minus dividends paid ...
		- = Change in cash
			- + beginning cash
		- = Ending cash balance

- differences:
	- structure 1 starts from EBIT (before financing decisions). It then explicitly shows how operations create cash, how investments consume cash, and how the financing structure (interest paid, tax shield earned, principal repaid) affects cash. The interest tax shield is its own visible line.
	- structure 2 starts from net income (after financing decisions). Interest expense is already deducted and the tax shield is already implicit (because taxes were computed on EBIT minus interest, not on EBIT alone). So those effects are hidden inside "net income." All financing-related cash movements (new debt raised, principal repaid, dividends paid) are bundled into one "net cash from financing activities" line.
##### Working capital formulas
- $Accounts\ Receivable=Sales*\frac{Days\ to\ fulfill\ credit}{365}$ 
	- e.g. 45 credit days (customers have 45 days to pay for the service)
- $Accounts\ Payable=(COGS+\Delta_{inventory})*\frac{Days\ to\ pay}{365}$
	- e.g. 30 days to pay the supplier
- $Inventory=COGS*\frac{365/Turnover\ rate\ (e.g.\ 10x)}{365}$
	- e.g. 10 turnover (the products will "rotate" in the inventory 10 times in the given period)
##### Categories of cash flow
Operating activities:
- Cash from running the business. Customer payments coming in, supplier and wage payments going out, taxes paid. If the operating cash flow is consistently negative, the core business is broken - no amount of clever financing will save it.

Investing activities:
- Cash spent on or received from long-term assets. Buying new equipment is a cash outflow here. Selling old machinery is an inflow. Acquiring another company would also fall here.

Financing activities:
- Cash from or to financiers (banks and shareholders). New loans, loan repayments, equity issuance, dividends, share buybacks.

Free cash flow = Operating cash flow - Investments:
- This is what's left for financiers (to repay debt, pay dividends, or accumulate). It's the most-watched single number in corporate finance.
	- If the free cash flow is negative, there is a need for a new debt or some kind of raising money (new equity)
	- If the free cash flow is positive, debts could be repaid, dividends paid or it could be used to accumulate/invest back to the company
### Balance sheet
- an overview snapshot of what the company owns and what the companes owes at this specific moment?
- always satisfied: assets = liabilities + equity
	- because everything that the company owns (assets) had to be paid for somehow (though own financing (equity) or the company borrowed money (liabilities))

Assets (what we have):
- Fixed assets (long-lived: buildings, machines, vehicles, less accumulated depreciation)
- Current assets (turnover within a year: inventory, accounts receivable, cash)

Liabilities + Equity (where it came from):
- Equity (owners' money: common stock + retained earnings)
- Long-term debt (loans repaid over more than a year)
- Short-term debt (credit lines, payables, accrued wages and taxes)