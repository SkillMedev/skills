---
name: Growth Model
description: Builds a driver-based growth model — acquisition, activation, retention, monetization, and a loop factor — that projects users and revenue bottom-up, runs base/upside/downside scenarios, and names the one constraint to work on next. Use when someone asks "build me a growth model", "what should our user projection be", "which lever moves growth most", "model our viral loop", or wants projections driven by real inputs instead of a hockey-stick guess. Do NOT use for the SaaS MRR bridge, NRR, and revenue-forecasting mechanics — use revenue-modeling. Do NOT use for decomposing historical active-user change into new, retained, resurrected, and churned — use growth-accounting. Do NOT use for per-customer CAC/LTV math — use unit-economics.
---

# Growth Model

A growth model is not a revenue forecast with a hopeful curve. It is a driver-based system that shows how inputs (traffic, conversion, retention) compound into output (users, revenue) — and where the leverage is. The costly mistake this prevents: drawing the output curve first and back-filling assumptions to justify it, which produces a plan nobody can steer because no team owns an input.

## Operating procedure

Order matters: the loop must be identified before the equation is written, and the equation before any projection — otherwise the model quietly becomes a fixed-percent guess with extra columns.

### Step 1: gather inputs

- Current active users and the definition of "active" (a meaningful action, not a login).
- New users per period by channel, with each channel's volume and cost.
- Activation rate: the share of new users reaching first value. From real funnel data where it exists.
- Retention: the share of actives who stay each period — from cohort curves, never a flat assumption. If only aggregate churn exists, label the retention figure a guess.
- Revenue per retained user, including expansion.
- Any referral or loop evidence: invites sent, invite conversion, content-driven signups.

Use real cohort data wherever it exists (wire it from product-analytics); label everything else a guess and flag it for replacement.

### Step 2: identify the primary loop

Funnels are linear and leak; loops compound — the output of one cycle feeds the input of the next. Name which loop is the primary engine:

- **Viral loop**: users invite users; each user brings k more.
- **Content loop**: usage creates content that attracts new users (SEO, user-generated pages).
- **Paid loop**: revenue funds acquisition that funds more revenue — works only if payback is shorter than the reinvestment cycle time.
- **Sales loop**: customers refer or expand, funding more sales capacity.

A business with no loop rents all its growth and stalls when spend stops. If no loop exists yet, say so in the model rather than inventing a loop factor.

### Step 3: write the driver equation

Make the structure explicit before touching a spreadsheet. The canonical period-over-period form:

```
signups(t)   = paid(t) + organic(t) + k × active(t-1)        ← loop term
activated(t) = signups(t) × activation_rate
active(t)    = active(t-1) × retention + activated(t)
revenue(t)   = active(t) × ARPU
```

Every symbol maps to a driver a team can own. If a term in the model has no owner and no data source, it is decoration — delete it.

### Step 4: project bottom-up and run the sensitivity

Project period over period from the equation — never draw the output curve first. Then run the sensitivity that decides where the roadmap points: retention compounds while acquisition is a one-time add, so a 5-point improvement in monthly retention usually beats a 20% increase in acquisition over a 12-month horizon. Model it explicitly (the calculator below does) so the team invests where the leverage is, not where it feels busy.

### Step 5: build base / upside / downside scenarios

Flex only the two or three most sensitive drivers (usually retention, activation, and the loop factor) — not every input. Downside must include retention decay; assuming everyone acquired stays forever is the classic silent error.

### Step 6: name the constraint and set driver targets

Identify the one driver that, if improved, unlocks the most growth, and point the roadmap there. Set targets per driver — not just a top-line number — so teams own inputs they control. Re-forecast monthly against actuals; a model never compared to reality is fiction.

## Calculator

Self-contained Node script. Save as growth_model.js, edit the inputs block, run node growth_model.js. No dependencies.

```javascript
// Growth model projection. Edit the inputs, then: node growth_model.js
const inputs = {
  startingActive: 10000,
  months: 12,
  paidNewPerMonth: 2000,     // signups from paid channels
  organicNewPerMonth: 1000,  // signups from organic/content
  activationRate: 0.6,       // share of signups reaching first value
  monthlyRetention: 0.80,    // share of actives who stay each month
  loopFactor: 0.2,           // k: new signups per active user per month
  arpu: 8,                   // monthly revenue per active user
}

function project(i, overrides = {}) {
  const p = { ...i, ...overrides }
  let active = p.startingActive
  const rows = []
  for (let m = 1; m <= p.months; m++) {
    const loopSignups = p.loopFactor * active
    const signups = p.paidNewPerMonth + p.organicNewPerMonth + loopSignups
    const activated = signups * p.activationRate
    active = active * p.monthlyRetention + activated
    rows.push({ m, signups, activated, active, revenue: active * p.arpu })
  }
  return rows
}

const fmt = n => Math.round(n).toLocaleString('en-US')
const base = project(inputs)
console.log('BASE CASE (retention 80%, k=0.2)')
for (const r of base.filter(r => [1, 3, 6, 12].includes(r.m)))
  console.log(`  month ${String(r.m).padStart(2)}: active ${fmt(r.active).padStart(7)}  revenue $${fmt(r.revenue)}`)

// Sensitivity: retention +5 pts vs acquisition +20%
const retUp = project(inputs, { monthlyRetention: 0.85 })
const acqUp = project(inputs, {
  paidNewPerMonth: inputs.paidNewPerMonth * 1.2,
  organicNewPerMonth: inputs.organicNewPerMonth * 1.2,
})
const last = rows => rows[rows.length - 1].active
console.log('\nSENSITIVITY at month 12')
console.log(`  base:                        ${fmt(last(base))} active`)
console.log(`  retention +5 pts (85%):      ${fmt(last(retUp))} active`)
console.log(`  acquisition +20%:            ${fmt(last(acqUp))} active`)
const winner = last(retUp) > last(acqUp) ? 'RETENTION' : 'ACQUISITION'
console.log(`  higher-leverage driver:      ${winner}`)
```

### Worked example output

With the inputs above the script prints:

```
BASE CASE (retention 80%, k=0.2)
  month  1: active  11,000  revenue $88,000
  month  3: active  12,766  revenue $102,131
  month  6: active  14,921  revenue $119,364
  month 12: active  17,904  revenue $143,233

SENSITIVITY at month 12
  base:                        17,904 active
  retention +5 pts (85%):      25,308 active
  acquisition +20%:            20,750 active
  higher-leverage driver:      RETENTION
```

Read it: five points of monthly retention adds ~7,400 actives at month 12, while 20% more acquisition spend adds ~2,800 — the retention gain compounds every period, the acquisition gain arrives once per period and then churns on the same curve. This is why the constraint in Step 6 is usually retention or activation, not the ad budget.

## Deliverable

Produce a driver-based growth model containing: the primary loop named (or the honest statement that none exists yet), the written driver equation with an owner per driver, a 12-month bottom-up projection with base/upside/downside scenarios, the sensitivity table showing which driver wins, and the single named constraint with its per-driver target.

## Do NOT

- Do not grow revenue by a fixed percent with no driver behind it — that is a wish, not a model, and no team can act on it.
- Do not ignore retention decay; assuming everyone acquired stays forever inflates every downstream number.
- Do not model a loop factor at or above 1 without evidence — true sustained virality is rare, and an assumed k of 1+ makes any model explode into fiction.
- Do not draw the output curve first and back-fill drivers; the direction of derivation is the entire point.
- Do not flex every input in scenarios; flex the two or three most sensitive drivers so the scenarios stay comparable.
- Do not use this to reconcile the MRR bridge or forecast SaaS revenue mechanics — that is revenue-modeling; and do not use it to explain last quarter's user change — that is growth-accounting.

## Quality bar

- Every driver in the equation has a data source (cohort data preferred) or an explicit guess label.
- The projection reconciles: active(t) recomputes from active(t-1) via the stated equation for every period.
- The sensitivity was actually run, and the named constraint follows from it, not from opinion.
- Scenarios differ only in the flexed drivers, and the downside includes retention decay.
- A re-forecast cadence (monthly, against actuals from product-analytics) is written into the deliverable.
