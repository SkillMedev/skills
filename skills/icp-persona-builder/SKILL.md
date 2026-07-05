---
name: icp-persona-builder
description: Turns a vague "who we sell to" into a weighted, filterable ICP scorecard - firmographics, technographics, and time-bound triggers expressed as literal Apollo or Sales Navigator filter values, built from closed-won and churned evidence, with hard disqualifiers and A/B/C account tiering - plus a buying-committee persona map covering economic buyer, champion, technical user, and blocker with each role's pains. Use when standing up outbound - FIRST, before any list-building or outreach - and someone asks "who is our ICP", "what filters do I put in Apollo", "we're targeting everyone and closing no one", or "score this account against our ICP". Do NOT use for pulling the actual prospect list - use prospect-list-builder instead - or for the messaging one-liner - use positioning-statement instead - or for end-user research personas - use user-persona instead.
---

# Build Your ICP & Buyer Personas

The core insight: an ICP is not a description, it is a **filter**. If you cannot type a criterion into Apollo, Sales Navigator, or a Clay table and get a finite list back, it is not part of your ICP - it is marketing prose. "Mid-market companies that value innovation" is useless. "US-based B2B SaaS, 50-500 employees, Series B+, using Salesforce + Segment, hiring SDRs" is a list you can build tomorrow. The most common trap is conflating the ICP (the *account* you target) with the persona (the *human* you email) and skipping the buying committee entirely - so reps email one champion who has no budget, or one economic buyer who has never felt the pain.

The second trap is building the ICP from aspiration instead of evidence. Your ICP is whoever already gets value and pays you, not whoever you wish would. Start from closed-won, not from the TAM slide. If you have fewer than ~10 closed deals, anchor on your best 3-5 customers by retention and expansion, and treat the result as a hypothesis to be falsified by [[buying-signal-tracker]] reply data.

## When to use this skill

- You are standing up outbound and have no documented, filterable target definition.
- Reps are spraying - pipeline is full of bad-fit deals that stall at procurement or "no budget."
- Reply rates are fine but win rates are bad: usually a persona/committee problem, not a copy problem.
- You're entering a new segment or launching a new product and need a fresh ICP hypothesis.
- You need to tier a raw account list A/B/C before [[prospect-list-builder]] and [[outreach-sequence-designer]] burn effort on it.

## The workflow

1. **Pull your evidence base first.** Export closed-won from the CRM. For each, record: industry, employee count, region, funding stage, the tools they ran, what triggered the deal, who signed, sales cycle length, and ACV. Also pull closed-LOST and churned - the disqualifiers hide there. Do NOT skip churned: a segment that buys fast and churns in 6 months is a *negative* ICP signal, not a win.

2. **Find the firmographic spine.** Look for the 3-5 attributes that separate won from lost. Express each as a literal filter value, not an adjective: `industry IN (B2B SaaS, fintech)`, `headcount 50-500`, `geo = US/CA`, `funding ≥ Series B`. If an attribute doesn't move win rate, cut it - every extra filter shrinks your list, so each one must earn its place.

3. **Layer technographics and triggers - this is where most ICPs are lazy.** Technographics ("runs Salesforce", "uses Snowflake", "no observability vendor detected") are often the sharpest fit predictor because they imply the exact pain you solve. Triggers are *time-bound* conditions that say "now, not someday": new exec hire, funding round, a relevant job posting, a competitor mention, M&A. Triggers belong in the ICP because they convert a static list into a prioritized queue - hand them to [[buying-signal-tracker]] to monitor continuously.

4. **Map the buying committee, not "the persona."** For each deal you typically need four roles: **economic buyer** (owns budget, signs), **champion** (feels the pain, sells internally for you), **technical/user** (lives in the problem daily), and **blocker** (security, procurement, legal, or a threatened incumbent owner). For each role capture: title patterns, the one pain they personally own, what they fear, and what they need to say yes. Reps who only know the champion lose at the economic-buyer stage.

5. **Write per-persona pain in their words, tied to a trigger.** Bad: "improve efficiency." Good: "the VP Eng just got headcount frozen and is on the hook for uptime - every incident now costs a weekend." Pain + role + trigger is the raw material [[cold-email-craft]] and [[outreach-sequence-designer]] turn into a first line. If you can't state the pain in the persona's own vocabulary, you haven't talked to enough of them.

6. **Define hard disqualifiers explicitly.** A negative list is as valuable as the positive one and saves more rep time. Examples: "single-product, no platform team", "regulated industry we can't get through security", "under 50 employees (no budget owner)", "open-source-only culture". These become exclusion filters in [[prospect-list-builder]] and keep your [[prospecting-metrics]] honest.

7. **Weight, score, and tier into A/B/C.** Assign weights to each criterion, score accounts, and bucket: **A** = strong fit + active trigger (work now, multithread), **B** = good fit, no trigger (nurture, wait for a signal), **C** = weak fit (do not prospect; let inbound find you). Re-tier quarterly against actual reply/win data - the ICP is a living hypothesis, not a one-time deck.

## ICP scorecard template

```
ICP SCORECARD - <product / segment>     Owner: ____   Last reviewed: ____

EVIDENCE BASE
  Closed-won analyzed: __    Best customers (retention/expansion): __________
  Churned/lost reviewed: __  (disqualifier source)

FIRMOGRAPHICS (filterable)            FILTER VALUE                WEIGHT
  Industry / vertical                 _______________________     __
  Employee count                      _______________________     __
  Geography                           _______________________     __
  Funding stage / revenue             _______________________     __
  Business model                      _______________________     __

TECHNOGRAPHICS (installed / absent)
  Must-have stack                     _______________________     __
  Disqualifying stack                 _______________________     __

TRIGGERS (time-bound, → buying-signal-tracker)
  __________________________________________________________     __
  __________________________________________________________     __

BUYING COMMITTEE
  Economic buyer  | titles: ______ | owns-pain: ______ | yes-criteria: ______
  Champion        | titles: ______ | owns-pain: ______ | sells internally on: __
  Technical/user  | titles: ______ | owns-pain: ______
  Blocker         | titles: ______ | fear/objection: ______

HARD DISQUALIFIERS (exclusion filters)
  - ______________________________________________________
  - ______________________________________________________

TIERING
  A = fit ≥ __ AND active trigger     → work now, multithread
  B = fit ≥ __ , no trigger           → nurture until signal
  C = fit <  __                       → do not prospect
```

## Worked example - "Sentinel" (fictional incident-response SaaS)

```
FIRMOGRAPHICS
  Industry        B2B SaaS, fintech                       wt 25
  Headcount       50-500                                  wt 20
  Geography       US / Canada                             wt 10
  Funding         Series B+                               wt 10
TECHNOGRAPHICS
  Must-have       runs a cloud platform (AWS/GCP) + PagerDuty OR no on-call tool   wt 15
  Disqualifying   fully managed/no-ops (Heroku-only)      (exclude)
TRIGGERS
  New VP Eng / Head of SRE in last 90 days               wt 10
  Public incident / status-page outage in last 60 days   wt 10
BUYING COMMITTEE
  Economic buyer  VP Eng / CTO  | pain: board asks "why did we go down?" | yes: proven MTTR drop, SOC2
  Champion        Eng Manager / SRE Lead | pain: weekend pages, alert fatigue | sells on: fewer 3am wakeups
  Technical/user  On-call engineer | pain: noisy alerts, no runbook
  Blocker         Security/Procurement | fear: another vendor with prod access
HARD DISQUALIFIERS
  - < 50 employees (no dedicated on-call, no budget owner)
  - Heroku-only / no infra team (no pain)
  - Already on a direct competitor < 6 months (locked in contract)
TIERING   A = fit ≥ 70 AND active trigger   B = fit ≥ 70 no trigger   C = fit < 70
```

Account scored: *Acme Payments* - fintech (25) + 220 employees (20) + US (10) + Series C (10) + AWS & PagerDuty (15) + new Head of SRE 6 weeks ago (10) = **90, active trigger → Tier A. Multithread VP Eng + SRE Lead now.**

## Runnable artifact - score an account against weighted ICP

Save as `score-account.mjs`, edit the two objects, run `node score-account.mjs`. No dependencies.

```js
// Edit ICP weights/values, then edit `account`, then: node score-account.mjs
const ICP = {
  weights: { industry: 25, headcount: 20, geo: 10, funding: 10, stack: 15, trigger: 10 },
  match: {
    industry: a => ["b2b saas", "fintech"].includes(a.industry?.toLowerCase()),
    headcount: a => a.headcount >= 50 && a.headcount <= 500,
    geo:       a => ["US", "CA"].includes(a.geo),
    funding:   a => ["B", "C", "D", "IPO"].includes(a.funding),
    stack:     a => a.stack?.some(t => ["aws", "gcp", "pagerduty"].includes(t.toLowerCase())),
    trigger:   a => Boolean(a.activeTrigger),
  },
  disqualifiers: [
    a => a.headcount < 50,                       // no budget owner
    a => a.stack?.length === 1 && /heroku/i.test(a.stack[0]), // no-ops, no pain
  ],
  tierFloor: 70, // fit score needed for A/B
};

const account = {
  name: "Acme Payments",
  industry: "fintech", headcount: 220, geo: "US", funding: "C",
  stack: ["AWS", "PagerDuty"],
  activeTrigger: "New Head of SRE (6 weeks ago)",
};

function scoreAccount(icp, a) {
  const dq = icp.disqualifiers.find(fn => fn(a));
  if (dq) return { name: a.name, fit: 0, tier: "C", reason: "disqualified" };
  let fit = 0;
  const hits = [];
  for (const [k, w] of Object.entries(icp.weights)) {
    if (icp.match[k]?.(a)) { fit += w; hits.push(`${k}+${w}`); }
  }
  const hasTrigger = icp.match.trigger?.(a);
  let tier = "C";
  if (fit >= icp.tierFloor) tier = hasTrigger ? "A" : "B";
  return { name: a.name, fit, tier, trigger: hasTrigger || false, hits };
}

const r = scoreAccount(ICP, account);
console.log(r);
console.log(
  r.tier === "A" ? "→ Work now, multithread the committee."
  : r.tier === "B" ? "→ Nurture; wait for a trigger (see buying-signal-tracker)."
  : "→ Do not prospect."
);
```

## Common failure modes

- **Adjectives instead of filters.** "Innovative", "fast-growing", "values security" cannot be queried. If [[prospect-list-builder]] can't type it, delete it.
- **ICP built from the TAM slide, not closed-won.** Aspiration ≠ evidence. Re-tier against real reply/win data quarterly.
- **One persona, no committee.** Emailing only the champion loses at budget; only the economic buyer loses at "does this actually work." Multithread Tier A.
- **No disqualifiers.** Without an exclusion list, reps rationalize every account as a fit and your [[prospecting-metrics]] rot.
- **Too many filters.** Each criterion shrinks the list multiplicatively; keep only attributes that demonstrably move win rate.
- **Static ICP.** Markets, products, and competitors move (see [[competitive-intelligence]]). An ICP reviewed once a year is wrong by month three.
