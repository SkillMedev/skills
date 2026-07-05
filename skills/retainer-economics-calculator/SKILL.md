---
name: retainer-economics-calculator
description: Compute per-client and agency-wide retainer profitability for a team-based agency - loaded delivery cost including account management time, gross margin per client, team utilization, and the fire-or-fix and sales-stop verdicts - with a runnable Node calculator. Use when an agency owner asks "which of my clients are actually profitable", "what's my real margin per retainer", "are we over capacity", "should we fire this client", or "can we take on another account". Do NOT use for pricing a solo freelancer's own retainer rate - use retainer-pricing-calculator instead.
---

# Retainer Economics Calculator

An agency can grow revenue every quarter and still starve, because two or three retainers quietly consume more delivery cost than they pay. The mistake this skill prevents is invisible unprofitability: treating account management time as free overhead, never loading labor cost, and only discovering the losing clients when the agency-wide margin collapses. Install this skill before the rest of the agency pack - it defines the margin thresholds that agency-positioning, agency-qbr-upsell, and client-churn-save all cite.

Work the example agency throughout: Northbeam Digital, an 8-person marketing agency with 11 retainer clients averaging $6,500/month ($71.5k MRR) and a 62 percent gross margin target.

## Operating procedure

Follow in order. Cost loading comes before any margin math, because margin computed on unloaded cost is fiction.

### Step 1: Build loaded hourly cost per role

For each delivery role: loaded hourly cost = annual salary x 1.35 (payroll taxes plus benefits) / 1,800 deliverable hours per year. Use 1,800, not 2,080 - nobody delivers 40 billable-equivalent hours a week.

Northbeam's roles: strategist $71.25/hr (from $95k salary), specialist $60.00/hr ($80k), designer $56.25/hr ($75k), account manager $52.50/hr ($70k).

The account manager rule: AM time spent on a specific client - status calls, reporting, email - is delivery cost for that client, not overhead. It is the single most common hidden cost in agency books, typically 8-14 hours per client per month.

### Step 2: Gather monthly hours per client per role

Pull from time tracking; average the last two full months. If the agency does not track time, run a two-week sampling sprint and label every number a guess until real data exists.

### Step 3: Compute per-client margin and apply the thresholds

Per-client gross margin = (retainer - loaded delivery cost) / retainer.

- Target: at or above 50 percent per client. Below that, one client is eating the profit of another.
- Agency-wide target: at or above 60 percent; Northbeam targets 62 percent. Agency-wide runs higher than the per-client floor because overhead (rent, tools, owner time, sales) still has to come out of gross margin.
- Fire-or-fix threshold: any client below 30 percent margin for two consecutive months gets a decision this month - reprice, re-scope, or exit. Not "watch another month." Two months is the rule because one bad month can be a project spike; two is a pattern.

### Step 4: Compute utilization and apply the sales stop rule

Team capacity = client-facing headcount x 120 deliverable hours/month (that is 1,800 / 12 x an 80 percent realistic ceiling already baked in). Northbeam: 7 client-facing staff x 120 = 840 hours.

Utilization = committed client hours / capacity.

- Healthy band: 60-85 percent. Below 60, the agency is overstaffed or undersold.
- Sales stop rule: above 85 percent, stop signing new retainers until capacity is added or a fire-or-fix client exits. Selling into a full team converts new revenue directly into churn - quality slips on every account at once.

### Step 5: Run the calculator and decide

Fire-or-fix clients get one of three moves, in preference order: (1) reprice to restore 50 percent margin, (2) re-scope hours down to fit the current price, (3) exit with 60 days notice and a referral. For the reprice or re-scope conversation, deliver it at the QBR using agency-qbr-upsell; if the client reacts as a churn risk, switch to client-churn-save.

## Inputs to collect

- Salaries (or contractor rates) per delivery role; default multiplier 1.35.
- Retainer fee per client.
- Monthly hours per client per role from time tracking; label untracked numbers as guesses.
- Client-facing headcount (exclude pure sales/admin).

## Calculator

Self-contained Node script. Save as `retainer_economics.js`, edit the inputs, run `node retainer_economics.js`. No dependencies.

```javascript
// Retainer Economics calculator. Edit the inputs, then: node retainer_economics.js
// Loaded hourly cost = salary x 1.35 (taxes + benefits) / 1800 deliverable hours per year.
const roles = {
  strategist: 71.25,
  specialist: 60.00,
  designer: 56.25,
  accountManager: 52.50, // AM time is delivery cost, not overhead
}

// hours = average monthly hours logged against the client, by role
const clients = [
  { name: 'Harbor & Oak',   retainer: 6500, hours: { strategist: 8,  specialist: 30, designer: 6, accountManager: 10 } },
  { name: 'Delta Freight',  retainer: 4500, hours: { strategist: 4,  specialist: 35, designer: 0, accountManager: 12 } },
  { name: 'Crestline SaaS', retainer: 9000, hours: { strategist: 10, specialist: 34, designer: 8, accountManager: 10 } },
  { name: 'Bluepine Outdoors', retainer: 7000, hours: { strategist: 6, specialist: 25, designer: 8, accountManager: 8 } },
  { name: 'Verra Health',   retainer: 8000, hours: { strategist: 8,  specialist: 28, designer: 6, accountManager: 9 } },
  { name: 'Marlow & Co',    retainer: 5500, hours: { strategist: 4,  specialist: 20, designer: 4, accountManager: 8 } },
  { name: 'Stonegate Legal', retainer: 6000, hours: { strategist: 6, specialist: 22, designer: 2, accountManager: 8 } },
  { name: 'Fairwind Travel', retainer: 5000, hours: { strategist: 4, specialist: 18, designer: 3, accountManager: 7 } },
  { name: 'Kettleworks',    retainer: 4500, hours: { strategist: 4,  specialist: 40, designer: 0, accountManager: 14 } },
  { name: 'Onyx Fitness',   retainer: 7500, hours: { strategist: 8,  specialist: 26, designer: 6, accountManager: 8 } },
  { name: 'Pillar Home',    retainer: 8000, hours: { strategist: 8,  specialist: 30, designer: 8, accountManager: 8 } },
]

const agencyMarginTarget = 0.62 // Northbeam's agency-wide target
const clientMarginFloor = 0.50  // per-client floor
const fireOrFixLine = 0.30      // below this two months running: fire or fix
const capacityHours = 840       // 7 client-facing staff x 120 deliverable hrs/month
const salesStopUtilization = 0.85

function costOf(c) {
  return Object.entries(c.hours).reduce((sum, [role, h]) => sum + h * roles[role], 0)
}
function hoursOf(c) {
  return Object.values(c.hours).reduce((a, b) => a + b, 0)
}
function verdict(m) {
  if (m < fireOrFixLine) return 'FIRE-OR-FIX'
  if (m < clientMarginFloor) return 'FIX'
  return 'HEALTHY'
}

let totalRev = 0, totalCost = 0, totalHours = 0
console.log('CLIENT                MARGIN   VERDICT')
for (const c of clients) {
  const cost = costOf(c)
  const m = (c.retainer - cost) / c.retainer
  totalRev += c.retainer; totalCost += cost; totalHours += hoursOf(c)
  console.log(c.name.padEnd(20), (m * 100).toFixed(1).padStart(5) + '%  ', verdict(m))
}
const agencyMargin = (totalRev - totalCost) / totalRev
const utilization = totalHours / capacityHours
console.log('---')
console.log('Agency MRR:            $' + totalRev.toFixed(0))
console.log('Agency delivery cost:  $' + totalCost.toFixed(2))
console.log('Agency gross margin:   ' + (agencyMargin * 100).toFixed(1) + '%  (target ' + (agencyMarginTarget * 100).toFixed(0) + '%)')
console.log('Margin verdict:        ' + (agencyMargin >= agencyMarginTarget ? 'ON TARGET' : 'BELOW TARGET'))
console.log('Committed hours:       ' + totalHours + ' of ' + capacityHours + ' (' + (utilization * 100).toFixed(1) + '% utilization)')
console.log('Sales verdict:         ' + (utilization > salesStopUtilization ? 'STOP SELLING - over capacity' : 'CAPACITY TO SELL'))
```

### Worked example output

With Northbeam's inputs the script prints:

```
CLIENT                MARGIN   VERDICT
Harbor & Oak          50.3%   HEALTHY
Delta Freight         33.0%   FIX
Crestline SaaS        58.6%   HEALTHY
Bluepine Outdoors     60.0%   HEALTHY
Verra Health          61.8%   HEALTHY
Marlow & Co           61.3%   HEALTHY
Stonegate Legal       62.0%   HEALTHY
Fairwind Travel       62.0%   HEALTHY
Kettleworks           24.0%   FIRE-OR-FIX
Onyx Fitness          61.5%   HEALTHY
Pillar Home           59.5%   HEALTHY
---
Agency MRR:            $71500
Agency delivery cost:  $31691.25
Agency gross margin:   55.7%  (target 62%)
Margin verdict:        BELOW TARGET
Committed hours:       531 of 840 (63.2% utilization)
Sales verdict:         CAPACITY TO SELL
```

Read it: nine of eleven clients are healthy, but Kettleworks at 24 percent is below the 30 percent fire-or-fix line (if last month looked the same, the decision is due now) and Delta Freight at 33 percent needs a fix plan. Those two clients alone drag Northbeam from its 62 percent target down to 55.7 percent. Utilization at 63.2 percent means there is room to sell - but only into on-niche work per agency-positioning, and only after the Kettleworks decision, or the new capacity gets eaten by the same leak.

## Deliverable

A per-client margin table with a HEALTHY / FIX / FIRE-OR-FIX verdict on every retainer, the agency-wide margin against the 62 percent target, the utilization number with a sell / stop-selling verdict, and a named action with an owner for every client below 50 percent.

## Do NOT

- Do not book account management time as overhead - it is the most common way a "60 percent margin" client is actually at 40.
- Do not use raw salary as hourly cost; load with the 1.35 multiplier and divide by 1,800 deliverable hours, not 2,080.
- Do not average away the losers: agency-wide margin can look acceptable while two clients burn cash. The per-client table is the point.
- Do not give a fire-or-fix client a third month of "monitoring" - two consecutive months below 30 percent triggers a decision, not a discussion.
- Do not keep selling past 85 percent utilization; new revenue signed into a full team becomes churn across the whole book.

## Quality bar

- Every hourly cost is loaded (x1.35, /1,800) and every untracked hours figure is labeled a guess.
- AM hours appear as delivery cost on every client, no exceptions.
- Every below-50-percent client has a named next move (reprice / re-scope / exit) and an owner.
- The sheet states both verdicts - margin vs 62 percent and utilization vs 85 percent - not just one.

## Escalation and neighbors

This skill installs first in the agency pack; its thresholds (50 percent per-client floor, 30 percent fire-or-fix line, 62 percent agency target, 85 percent sales stop) are the numbers agency-positioning, agency-qbr-upsell, and client-churn-save cite. Deliver reprices at the QBR with agency-qbr-upsell; if a reprice conversation turns into a churn risk, use client-churn-save. Scoping a fix into a contract change is a statement-of-work-writer job. This is management math, not accounting - for tax treatment or financial statements, bring in the agency's accountant.
