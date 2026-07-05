---
name: Personal Financial Planner
description: Builds a complete personal financial plan — a prioritized order of operations across budgeting, emergency savings, debt payoff, and goals — from the user's actual numbers, then routes each piece to the right specialist skill. Use when someone asks "help me get my finances in order", "where should my money go first", "should I pay off debt or save", "am I doing okay financially", or "build me a financial plan". Do NOT use for constructing the monthly budget itself — use budget-builder instead; for sequencing multiple debts — use debt-payoff-planner; for sizing the cash cushion — use emergency-fund-planner; for investing fundamentals — use investment-basics; for retirement readiness math — use retirement-projection; for lowering a tax bill — use tax-optimization; for evaluating one large purchase — use big-purchase-decision.
---

# Personal Financial Planner

This is the umbrella skill: it turns a messy money situation into one prioritized plan and dispatches the detailed work to specialist skills. The costly mistake it prevents is optimizing the wrong piece first — contributing to a brokerage account while carrying a 24% APR credit card, or attacking debt with zero cash buffer so the first surprise expense goes straight back on the card. Order of operations is the whole game; be clear, practical, and non-judgmental about wherever the user is starting from.

## Operating procedure

### Step 1: Gather inputs

Collect these before recommending anything. If a number is a guess, label it a guess and proceed; refine later.

1. Monthly take-home income (net, all sources). For variable income, use the average of the last three months.
2. Essential monthly expenses: rent or mortgage, utilities, groceries, insurance, minimum debt payments, transportation.
3. Every debt: creditor, balance, APR, minimum payment.
4. Current cash savings and where it sits.
5. Employer retirement plan and match terms, if any.
6. Goals with rough timelines: a purchase, a payoff date, retirement.

### Step 2: Compute three diagnostic numbers

- **Monthly surplus** = take-home income minus all current spending. If unknown, take-home minus essentials minus debt minimums is the ceiling; actual surplus is usually less.
- **Debt-to-income** = total monthly debt payments divided by gross monthly income. Under 36% is workable; above 36% is strained; above 43% is the red line lenders use and a signal that payoff must dominate the plan.
- **Cash runway** = liquid savings divided by essential monthly expenses, in months.

### Step 3: Place the user on the order of operations

Walk this sequence top to bottom and stop at the first unmet step. That step is where every surplus dollar goes. The order exists because each step protects the ones below it from being undone.

1. **Pay all minimums, every month.** Non-negotiable; missed payments compound the problem through fees and credit damage.
2. **Capture the full employer match.** An unclaimed match is a 50–100% immediate return; nothing later in the sequence beats it.
3. **Starter emergency fund: 1 month of essential expenses.** Without it, any surprise re-creates high-interest debt. Route sizing details to emergency-fund-planner.
4. **Kill high-interest debt (above 7–8% APR).** No safe savings vehicle outpaces a 20%+ card. Route the avalanche-vs-snowball sequencing to debt-payoff-planner.
5. **Full emergency fund: 3–6 months of essential expenses** (more for variable income — emergency-fund-planner has the risk tiers).
6. **Tax-advantaged investing** beyond the match. Route to investment-basics and tax-optimization.
7. **Named goals**: translate each into a monthly amount = target ÷ months to deadline. For a large purchase, run big-purchase-decision first; for retirement adequacy, run retirement-projection.

Low-interest debt (below ~7%) is paid on schedule alongside steps 5–7, not attacked ahead of them, unless the user strongly prefers being debt-free — note the trade-off and respect the preference.

### Step 4: Build the budget that funds the plan

Hand construction to budget-builder (zero-based, 50/30/20 starting split). The plan from Step 3 dictates the "20" — where savings and extra debt dollars go each month.

### Step 5: Reality-check and write the plan

If the goals don't fit the surplus, say so plainly and adjust timelines or amounts — an honest 30-month plan beats a fictional 12-month one. Then fill the snapshot below.

## Worked example

Take-home $5,000/mo; essentials $3,100; debts: card $4,000 at 22% (min $120), student loan $12,000 at 5% (min $140); savings $1,200; employer matches 100% up to 4% of salary (~$180/mo); surplus ~$600/mo.

- Diagnostics: DTI ≈ 4% of gross on minimums (fine); runway = $1,200 ÷ $3,100 ≈ 0.4 months (thin).
- Order of operations: minimums covered → match NOT captured → fix that first ($180/mo). Starter fund needs $3,100 − $1,200 = $1,900 → ~$420/mo for 5 months. Then all surplus to the 22% card (~$600 extra + $120 min clears it in ~7 months). Student loan at 5% stays on schedule. Then build the fund to 3 months ($9,300) and start Roth IRA contributions.
- Verdict: fully invested-and-funded position in roughly 24 months, with the match captured from month one.

## Deliverable

Produce a one-page plan snapshot containing: the three diagnostic numbers, the user's current step on the order of operations, the single next action with a monthly dollar amount, a dated milestone list (starter fund by ___, card cleared by ___, full fund by ___), and the specialist skills to run for each stage.

```
FINANCIAL PLAN SNAPSHOT — [FILL: name] — [FILL: month]
Take-home: $[FILL]/mo   Essentials: $[FILL]/mo   Surplus: $[FILL]/mo
DTI: [FILL]%   Cash runway: [FILL] months
CURRENT STEP: [FILL: 1-7 from the order of operations]
THIS MONTH'S MOVE: $[FILL] to [FILL]
MILESTONES: [FILL: milestone — target date, one per line]
```

## Do NOT

- Do not recommend investing while the user carries debt above 7–8% APR and no starter fund — the math loses and the behavior collapses at the first emergency.
- Do not skip the employer match to pay debt faster; a 50–100% instant return beats any card APR.
- Do not build the plan on gross income; only take-home pays bills.
- Do not present a plan the surplus cannot fund — flag unrealistic goals honestly and re-scope.
- Do not do the specialists' jobs here; route depth to budget-builder, debt-payoff-planner, emergency-fund-planner, investment-basics, retirement-projection, tax-optimization, and big-purchase-decision.

## Quality bar

- Every number in the snapshot traces to a user-provided figure or is labeled a guess.
- The plan names exactly one current step and one funded next action — not seven parallel priorities.
- Milestones have dates derived from the actual surplus, with the math shown.
- The order-of-operations placement is justified against the diagnostics.

## Escalation

This is general financial education, not individualized financial, tax, or investment advice. Recommend a licensed professional — a CFP for complex planning, a CPA or enrolled agent for tax questions, a nonprofit credit counselor when DTI passes 43% or debts are in collections — for any decision with legal, tax, or estate consequences.
