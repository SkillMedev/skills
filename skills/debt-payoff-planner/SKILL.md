---
name: Debt Payoff Planner
description: Builds a sequenced multi-debt payoff plan - avalanche or snowball chosen by explicit decision rules - with the rollover schedule, total-interest math, and a debt-free date. Use when someone asks "which debt do I pay first", "avalanche or snowball", "how do I get out of credit card debt", or "when will I be debt-free". Do NOT use for sizing the cash cushion that prevents new debt - use emergency-fund-planner instead; for building the monthly budget that produces the extra payment - use budget-builder; for the overall save-vs-invest-vs-payoff ordering - use financial-planner.
---

# Debt Payoff Planner

Carrying multiple debts without a sequenced plan means paying more interest than necessary and losing momentum. This skill produces a concrete, ordered repayment plan with a debt-free date. The costly mistake it prevents is spreading extra payments evenly across all debts - which feels fair and maximizes total interest paid.

## Operating procedure

### Step 1: Inventory every debt

Collect four fields per debt: creditor name, current balance, APR, minimum monthly payment. Include everything - credit cards, personal loans, medical debt, student loans, car loans. Exclude the mortgage from the active payoff list; it is managed separately. Label estimated APRs as guesses and confirm from statements.

Also collect gross monthly income and compute **debt-to-income** (total monthly debt payments ÷ gross monthly income). Under 36% is workable. Above 43% is the red line: the plan likely needs professional restructuring, not just sequencing - see Escalation. If unsecured debt exceeds annual income, escalate immediately.

### Step 2: Find the extra payment

Extra payment = total monthly amount the user can dedicate to debt minus the sum of minimums. Get this from a real budget (budget-builder), not optimism. Even an extra $50/month shortens a payoff timeline significantly. If the extra is $0, the plan is income or expenses, not sequencing - route to budget-builder first.

Precondition: a starter emergency fund of at least one month of essential expenses exists before going aggressive (emergency-fund-planner). Without it, the first surprise expense lands back on the highest-APR card and undoes months of progress.

### Step 3: Choose the method by rule, not vibe

- **Avalanche** (mathematically optimal): order debts highest APR to lowest. Minimizes total interest.
- **Snowball** (behaviorally effective): order debts smallest balance to largest. Wins come faster; research shows snowball users pay off debt more consistently than avalanche users.

Decision rules:

1. If the smallest debts can each be cleared in under 3 months, or the user has previously abandoned a payoff attempt, choose snowball - momentum is the binding constraint.
2. If APRs span a wide range (e.g. a 24% card alongside a 6% loan) and the user is motivated by the math, choose avalanche - the interest savings are real money.
3. If balance differences are small or the highest-APR debt is also small, the methods converge - choose avalanche.
4. Always show both totals (as below) and let the user see the price of the behavioral option. A method the user sticks with beats the optimal one they quit.

### Step 4: Build the rollover sequence

List debts in chosen order and mark the first target. Pay minimums on everything; every extra dollar goes to the target. When a debt clears, roll its entire payment (minimum + extra) onto the next debt. Total monthly outlay never changes; intensity on each remaining debt increases. Produce the payoff schedule with a month number for each debt and the debt-free date.

### Step 5: Handle interruptions

If an emergency forces a pause on the extra payment, resume as soon as possible - a one-month pause does not ruin the strategy. Never pause minimums. If pauses recur, the emergency fund is undersized (emergency-fund-planner), not the plan wrong.

## Worked example: avalanche vs snowball on the same debts

Four debts, $850/month total budget ($590 minimums + $260 extra):

| Debt | Balance | APR | Minimum |
|---|---|---|---|
| Store card | $850 | 17% | $35 |
| Credit card | $6,300 | 24% | $190 |
| Personal loan | $3,500 | 11% | $110 |
| Car loan | $9,000 | 6% | $255 |

**Avalanche** (order: credit card → store card → personal loan → car loan): credit card clears month 17, store card month 18, personal loan month 21, car loan month 27. Total interest: **$2,606**.

**Snowball** (order: store card → personal loan → credit card → car loan): store card clears month 3, personal loan month 12, credit card month 22, car loan month 27. Total interest: **$3,050**.

Both are debt-free in 27 months. Avalanche saves **$444**; snowball delivers a first win in month 3 instead of month 17. Per the decision rules: the store card clears in under 3 months, so a motivation-fragile user takes snowball and pays $444 for the momentum; a numbers-driven user takes avalanche. Either beats no sequence.

## Deliverable

Produce a payoff plan containing: the debt inventory table, the DTI figure, the chosen method with the decision rule that selected it, both methods' total interest and debt-free dates, the ordered rollover schedule (target debt, payoff month, payment that rolls forward), and the single number the user must automate - total monthly debt payment.

```
DEBT PAYOFF PLAN - [FILL: date]
Total debt: $[FILL]   DTI: [FILL]%   Monthly budget: $[FILL] ($[FILL] min + $[FILL] extra)
Method: [FILL: avalanche/snowball] - chosen because [FILL: decision rule]
Order: 1. [FILL: debt, payoff month] 2. [FILL] ...
Debt-free date: [FILL]   Total interest: $[FILL] (vs $[FILL] on the other method)
```

## Do NOT

- Do not split the extra payment across multiple debts; concentration is the entire mechanism.
- Do not pick avalanche by default for a user who has quit plans before - an abandoned optimal plan pays more interest than a completed suboptimal one.
- Do not include the mortgage in the sequence; its rate and term put it in the pay-on-schedule bucket.
- Do not start aggressive payoff with zero cash buffer; the first emergency refills the card.
- Do not project the debt-free date from balances ÷ payments - interest accrues monthly; do the amortized math.
- Do not treat a paused month as failure; resuming is the plan.

## Quality bar

- Every debt has all four fields, with guessed APRs flagged.
- Both methods' total interest and timelines are computed and shown, whichever is chosen.
- The rollover schedule names a payoff month per debt and a debt-free date.
- The method choice cites one of the four decision rules.
- DTI is computed and, if above 43%, the plan says escalate rather than sequencing anyway.

## Escalation

This is general financial education, not individualized financial advice. For debts in collections, wage garnishments, DTI above 43%, or total unsecured debt exceeding annual income, route to a nonprofit credit counselor (NFCC-affiliated) or bankruptcy attorney before committing to a DIY plan - consolidation and negotiated settlements require professional evaluation.
