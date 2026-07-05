---
name: Tax Optimization
description: Works through the legal tax-reduction sequence — tax-advantaged account priority, standard-vs-itemized decision math, tax-loss harvesting, income timing, and Roth-vs-traditional rules — and produces a personal tax checklist. Use when someone asks "how do I pay less in taxes", "should I itemize", "Roth or traditional", "what is tax-loss harvesting", or is doing year-end planning or reviewing withholding. Do NOT use for the broader ordering of savings and debt priorities — use financial-planner instead; for investing fundamentals beyond account tax treatment — use investment-basics; for projecting retirement account balances — use retirement-projection.
---

# Tax Optimization

Tax optimization is not aggressive schemes — it is using every legal mechanism already built into the tax code, in the right order. Most people leave significant money on the table not through bad decisions but through unmade ones: an uncaptured employer match, a default standard deduction never compared against real itemizable expenses, losses in a brokerage account never harvested. Work the sequence; skip nothing silently.

## Operating procedure

### Step 1: Gather inputs

Label uncertain figures as guesses; the user's latest return and pay stub resolve most of them.

1. Filing status and approximate gross income (which sets the marginal bracket).
2. Access to employer retirement plans and the match formula; current contribution rate.
3. Eligibility for a health savings account (enrollment in a qualifying high-deductible health plan).
4. Potential itemizable expenses: mortgage interest, state and local taxes, charitable contributions, large medical expenses.
5. Taxable brokerage holdings and any positions at a loss.
6. Control over income timing: freelance/business income, year-end bonus, equity compensation.

### Step 2: Fill tax-advantaged accounts in priority order

This order exists because each rung pays more per dollar than the one below it:

1. **Employer match first.** Contribute at least enough to capture the full match — an immediate 50–100% return that no other strategy approaches.
2. **HSA if eligible.** Triple tax-advantaged (deductible going in, grows untaxed, untaxed out for qualified medical costs) and rolls over indefinitely — the only account with all three; treat it as a stealth retirement account, paying current medical costs out of pocket when cash flow allows.
3. **Max the retirement account** if cash flow allows, using the Roth-vs-traditional rule in Step 5.

Look up the current year's contribution limits from IRS tables rather than assuming remembered figures.

### Step 3: Run the standard-vs-itemized comparison with real numbers

Most filers are better off with the standard deduction. Itemizing wins only when the sum of deductible expenses exceeds it. Never assume either way — fill the worksheet:

```
ITEMIZE-OR-NOT WORKSHEET — tax year [FILL]
Mortgage interest paid:                       $[FILL]
State and local taxes (income/sales + property, capped — check current cap): $[FILL]
Charitable contributions (with receipts):     $[FILL]
Medical expenses ABOVE the AGI-percentage floor: $[FILL]
ITEMIZED TOTAL:                               $[FILL]
Standard deduction for [FILL: filing status] (from current IRS tables): $[FILL]
DECISION: itemize only if itemized total > standard deduction
Margin: $[FILL]
```

If the itemized total lands close to the standard deduction, consider **bunching**: concentrate two years of charitable giving into one year (a donor-advised fund makes this clean), itemize that year, take the standard deduction the next.

### Step 4: Harvest losses in taxable accounts

In a taxable brokerage account, selling positions at a loss realizes losses that offset capital gains dollar for dollar; losses beyond gains offset ordinary income up to the annual threshold, and the remainder carries forward indefinitely. The **wash-sale rule** disallows the loss if the same or a substantially identical security is bought within **30 days before or after** the sale — maintain market exposure by buying a similar but not identical fund (a different index tracking a comparable market segment). Harvest against long-term gains last; short-term gains are taxed at the higher ordinary rate, so offsetting them is worth more.

### Step 5: Apply the Roth-vs-traditional rule

- **Traditional (pre-tax)** when in a high bracket now and expecting a lower bracket in retirement — take the deduction at the high rate, pay tax later at the low one.
- **Roth** when in a low bracket now and expecting higher taxes later, or when future rates are genuinely uncertain and tax diversification has value.
- A **Roth conversion in a low-income year** — early retirement, career gap, sabbatical — is often the single most efficient window: the conversion fills the low brackets that would otherwise go unused.

### Step 6: Time income and deductions near bracket boundaries

For users with control over timing — freelancers, business owners, year-end bonuses — deferring income into next year or accelerating deductions into this one can keep income under a bracket boundary. This is worth real money only near a boundary; in the middle of a bracket, timing moves little. Check proximity before recommending it.

### Step 7: Produce the checklist

## Worked artifact: filled checklist

Married filer, ~$140,000 household income, HDHP-eligible, $9,000 mortgage interest, SALT at the cap, $4,000 charitable giving:

```
TAX CHECKLIST
[x] Employer match: captured in full (was at 3%, match goes to 5% — raised)
[x] HSA: opened, contributing; receipts saved, paying medical from cash flow
[ ] Itemize check: mortgage $9,000 + SALT (capped) + charity $4,000 vs standard
    deduction → short by a small margin → BUNCH: shift next year's giving into
    this year via donor-advised fund, itemize this year, standard next
[x] Harvest: sold bond fund at a $2,100 loss, bought a different-index bond
    fund same day (not substantially identical); offsets short-term gain first
[x] Roth/traditional: 22-24% bracket now, expects lower in retirement →
    traditional for new contributions
[ ] Timing: freelance invoice for December work — defer to January (near
    bracket boundary this year)
[ ] Book CPA review: equity comp vesting next year
```

## Deliverable

Produce a personal tax checklist containing: match status, HSA eligibility and action, the completed itemize-or-not worksheet with the decision and margin, any harvestable losses with a wash-sale-safe replacement named, the Roth-vs-traditional call with its one-line rationale, any timing moves, and the items that require a professional.

## Do NOT

- Do not cite specific dollar figures for contribution limits, standard deductions, or the SALT cap from memory — they change; direct the user to current IRS tables and do the comparison with those numbers.
- Do not recommend itemizing without running the worksheet; assumption is the most common error in both directions.
- Do not harvest a loss and rebuy the identical fund inside the 30-day window — the wash-sale rule erases the benefit.
- Do not chase deductions that require spending a dollar to save a fraction of one; a deduction is a discount, not a rebate.
- Do not let tax treatment drive the investment choice; route asset selection to investment-basics.

## Quality bar

- The account priority order is applied explicitly, with the match confirmed captured before anything else is discussed.
- The itemize decision shows both totals and the margin, not a conclusion.
- Every harvesting recommendation names the replacement security and confirms the 30-day window.
- Any figure that varies by tax year is referenced to current IRS tables, never stated as a number.
- The checklist separates DIY items from CPA items.

## Escalation

This is general tax education, not individualized tax advice, and outcomes depend on filing status, state, income type, and life events. Route to a CPA or enrolled agent for self-employment tax, stock compensation (options, RSUs), rental income, multi-state filing, inheritance, or any marriage/divorce-year return — and before executing a Roth conversion of meaningful size.
