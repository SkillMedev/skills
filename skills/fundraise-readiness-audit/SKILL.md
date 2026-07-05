---
name: Fundraise Readiness Audit
description: Runs the audit an investor will run before a founder starts pitching - scoring metrics, story, team, and legal/financial hygiene red/yellow/green and returning a go, fix-first, or wait verdict with the milestones that turn each red gate green. Use when a founder asks "audit my fundraise readiness before I pitch", "run the audit an investor would run on my startup", "score my metrics, story, and team before I raise", "am I ready to start fundraising", or wants a fundraise checklist before any outreach at any stage. Do NOT use for Series A-specific metric benchmarks (ARR bar, NRR, payback) and building the Series A data room - use series-a-readiness. Do NOT use for choosing the stage, amount, and instrument of the raise - use fundraising-stage-selector. Do NOT use for writing the pitch story itself - use fundraising-narrative.
---

# Fundraise Readiness Audit

A raise that starts before the company is ready burns the best investors first - and a founder gets exactly one first meeting with each. This skill runs the audit a sharp investor will run, at any stage, so the founder finds the holes before the investor does. It is stage-agnostic: it decides go versus wait, not whether the numbers clear a specific round's benchmark - that is series-a-readiness territory when the round is an A.

## Operating procedure

Run it after fundraising-stage-selector sets the envelope (stage, amount, instrument) and before any outreach. Re-run it the week before going live to confirm nothing regressed.

### Step 1: gather inputs

- The stage envelope: round, target amount, and the milestone the money buys (from fundraising-stage-selector).
- Current metrics: the hero metric with its denominator, growth, retention or NRR, burn, runway, CAC/payback - with sources. Label any unreconciled or estimated figure as a guess.
- The narrative draft (from fundraising-narrative) and the deck if one exists.
- Cap table, option grants, prior SAFEs/notes, IP assignments, incorporation docs - or an honest statement of what is missing.
- Runway in months. Opening a process with under 6 months of runway means negotiating from desperation; flag it before anything else.

### Step 2: score the four readiness gates

Score each gate red / yellow / green with evidence. **Any red means delay, not push** - word travels in investor networks, and a botched first pass resets only by waiting.

**Gate 1 - Metrics.** Can the founder state the one hero metric with a denominator, and is it improving? Are growth, retention/NRR, burn, runway, and CAC/payback current and reconciled to the bank? Is there a cohort or retention curve that survives a skeptic? Weak retention is the #1 silent round-killer. Red if the hero metric has no denominator, the numbers do not reconcile, or the retention curve decays toward zero instead of flattening.

**Gate 2 - Story.** Does the fundraising-narrative survive being read aloud in six sentences? Is the named risk answered, not hidden? Does the ask (amount, milestone) match the evidence? Misalignment between ask and evidence reads as not knowing the business. Red if the ask and the metrics tell different stories.

**Gate 3 - Team.** Does the team slide answer "why this team wins" without hand-waving? Are the obvious gaps named with a hiring plan (see fundraise-team-hiring)? Founder vesting in place, no absent co-founder holding dead equity? Red if there is unresolved dead equity - it surfaces in every diligence pass.

**Gate 4 - Legal and financial hygiene.** Clean cap table, no handshake equity, all option grants documented (see cap-table-manager). IP assigned to the company by every contractor and every founder. Incorporation, board consents, and prior SAFEs/notes organized. Books close monthly, and a data room could be produced in days, not weeks (see data-room-builder). Red if any equity or IP exists only as a verbal agreement.

### Step 3: run the kill questions

Ask these exactly as an investor would. Any answer that is a flinch converts its gate to red:

- "Show me the retention cohorts." (Do they exist and hold?)
- "Walk me through the cap table." (Any surprises?)
- "What did you believe a year ago that you no longer believe?" (Is there a real insight?)
- "What breaks if I give you the money?" (Does the founder know the constraint?)

### Step 4: issue the verdict and the gap plan

- All green → **go**: open outreach on the stage-selector timeline.
- Yellows only → **fix-first**: rank the yellows by how fast an investor would find each, close them, re-run the audit.
- Any red → **wait**: name the specific milestone that turns each red green (e.g. "three consecutive cohorts with flattening retention", "signed IP assignment from the departed contractor"), and hold all outreach until then.

## Scorecard template

```
FUNDRAISE READINESS SCORECARD - [FILL: company], [FILL: stage/amount from fundraising-stage-selector]

GATE 1 METRICS   [FILL: R/Y/G]  hero metric: [FILL + denominator]  trend: [FILL]
                 retention curve: [FILL: flattens / decays]  reconciled to bank: [FILL: y/n]
GATE 2 STORY     [FILL: R/Y/G]  six-sentence read-aloud: [FILL: pass/fail]
                 named risk answered: [FILL]  ask matches evidence: [FILL: y/n]
GATE 3 TEAM      [FILL: R/Y/G]  why-this-team-wins: [FILL]  gaps + hiring plan: [FILL]
                 vesting in place / no dead equity: [FILL: y/n]
GATE 4 HYGIENE   [FILL: R/Y/G]  cap table clean: [FILL]  IP assigned (all): [FILL]
                 books close monthly: [FILL]  data room in days: [FILL: y/n]

KILL QUESTIONS   cohorts: [FILL]  cap table walk: [FILL]  changed belief: [FILL]  what breaks: [FILL]

RUNWAY           [FILL] months  (under 6 = negotiating from desperation - flag)

VERDICT          [FILL: GO / FIX-FIRST / WAIT]
GAPS (ranked)    1. [FILL: gap → milestone that turns it green → owner → date]
                 2. [FILL]
RE-AUDIT DATE    [FILL: week before go-live]
```

## Deliverable

Produce the filled red/yellow/green scorecard across the four gates, a ranked list of gaps each paired with the specific milestone that turns it green, and a go / fix-first / wait recommendation with a re-audit date set for the week before outreach opens.

## Do NOT

- Do not go out to "test the market" with a red gate, expecting to fix it mid-raise. Word travels between funds; the only reset is waiting, which costs more than fixing first.
- Do not confuse activity (signups, pilots, waitlists) with proof (retention, revenue). Investors discount activity to zero.
- Do not polish the deck over a messy cap table. Diligence checks the cap table, not the gradients.
- Do not average the gates into one score. Three greens and a red is a red - the red is where the round dies.
- Do not benchmark seed metrics against Series A bars here; stage-specific thresholds live in series-a-readiness.

## Quality bar

- Every gate score cites evidence, not vibes - a number, a document, or an explicit "missing".
- Every red and yellow has a named milestone that would flip it, with an owner.
- The kill questions were actually asked and the answers recorded verbatim, flinches included.
- The verdict is one of exactly three words - go, fix-first, or wait - and follows mechanically from the gate colors.

## Escalation

This is preparation guidance, not legal or financial advice: cap table cleanup, IP assignment, and prior-instrument questions belong with a startup lawyer before any investor sees the data room. When the verdict is go, move to investor-targeting and fundraising-narrative for outreach, data-room-builder for diligence prep, and term-sheet-negotiation once paper arrives. When the round is specifically a Series A, run series-a-readiness for the metric benchmarks and data-room build on top of this audit.
