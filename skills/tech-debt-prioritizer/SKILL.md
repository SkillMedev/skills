---
name: Tech Debt Prioritizer
description: Inventories tech debt and ranks it economically - scoring each item 1-3 on cost of delay (the interest rate) and leverage (the principal unblocked), multiplying into a priority index, and packaging the winners into a leadership proposal framed on business impact. Use when someone asks "which tech debt should we tackle first", "help me build a case for refactoring to leadership", "our backlog of debt is huge, where do we start", or is preparing quarterly planning. Do NOT use for finding and removing unused code - use dead-code-eliminator instead - for auditing third-party dependency risk - use dependency-risk-audit - or for planning the sprint the work lands in - use sprint-planning instead.
---

# Tech Debt Prioritizer

Tech debt decisions fail when made on vibes ("this code is ugly") or recency bias (whatever hurt last week). This skill applies a lightweight economic frame - debt as a loan, where cost of delay is the interest rate you pay every sprint and leverage is the principal of future work it unblocks - so the right items get funded and the rest stay parked without guilt.

## Operating procedure

The steps run in order because scoring an unfiltered inventory is what keeps the ranking honest: filtering first means the loudest complaints survive and the quiet, expensive items never get scored.

### Step 1: gather inputs and build the inventory

Timebox to 60 minutes and cap the list at 20 items - past 20, scoring fatigue produces garbage scores. Pull candidates from three sources, one pass each:

- Friction signals: team retros and chat complaints.
- Reliability signals: incident post-mortems from the last two quarters.
- Velocity signals: areas with high cycle time or frequent reverts.

Each item gets a one-line description, the system it lives in, and the person who raised it. Do not filter at this stage; resist the urge. If an estimate is a gut call ("this costs us maybe a day a sprint"), record it as a guess.

### Step 2: score each item on two axes, 1-3 each

- Cost of delay (the interest rate): what does leaving this unaddressed cost per sprint - engineer time lost, incident risk carried, opportunity cost? 1 = an annoyance under a few hours a sprint; 2 = meaningful drag, roughly half a day to two days of engineer time per sprint or a plausible incident path; 3 = actively bleeding - multiple engineer-days per sprint, a repeat-incident source, or blocking a committed date.
- Leverage (the principal): how many future features or workflows does fixing this unblock or accelerate? 1 = local cleanup, nothing downstream; 2 = speeds up one team's ongoing work; 3 = unblocks multiple roadmap items or teams.

Multiply for the priority index (1-9). Items scoring 9 are immediate candidates; 6 goes on the quarter plan; 1-2 goes to the parking lot. Score in one sitting with the whole group so the scale stays calibrated across items.

### Step 3: classify by quadrant

Plot on a 2x2 to catch what the index alone hides:

- High cost-of-delay / high leverage: do now.
- High cost-of-delay / low leverage: schedule this quarter - the interest is real even if nothing downstream is waiting.
- Low cost-of-delay / high leverage: opportunistic - fold into adjacent feature work rather than scheduling standalone.
- Low / low: document and defer to the parking lot.

The parking lot needs a quarterly review gate that drops items older than 6 months. An unbounded parking lot becomes a second backlog nobody trusts.

### Step 4: size and assign

Every "do now" and "this quarter" item gets a t-shirt size (S = days, M = 1-2 weeks, L = 3-6 weeks, XL = needs decomposition before it can be scheduled - an XL debt item is a project, not a task). Propose an owner per item, and do not assign all debt to the same two senior engineers: well-scoped debt work is a career-growth opportunity for mid-level engineers, and hoarding it on seniors makes it invisible and unpromotable.

### Step 5: write the proposal

Lead with business impact, never code quality. The sentence pattern per item: "This currently costs us X per sprint / caused incident Y / blocks initiative Z. Fixing it takes N engineer-weeks. The expected return is [time recovered, risk retired, or roadmap unblocked]." Skip the architecture lecture - leadership funds interest-rate reduction, not elegance. If stakeholders resist all debt investment, propose a debt tax: reserve 15-20% of each sprint for debt, and never negotiate below 10% - below that line the tax exists on paper only.

## Worked artifact: scored table

```
ITEM                                  SYSTEM      CoD  LEV  IDX  QUADRANT       SIZE  OWNER
Flaky CI suite (retries mask fails)   build        3    3    9   do now          M    Priya
Orders svc has no staging env         orders       3    2    6   this quarter    L    Marcus
Duplicated pricing logic (3 places)   billing      2    3    6   this quarter    M    Jen
Legacy cron with no alerting          reports      2    1    2   parking lot     S    -
Inconsistent lint config              all repos    1    1    1   parking lot     S    -
```

Read it: the CI suite scores 9 - it taxes every engineer every day (CoD 3) and every future change ships through it (LEV 3) - so it jumps ahead of the staging-env gap even though the staging gap caused the more memorable incident. That inversion of recency bias is the entire point of scoring.

## Deliverable

Produce three artifacts: the scored inventory table (all items, both axes, index, quadrant), the quarter's funded list with t-shirt sizes and proposed owners, and a leadership proposal of no more than one page where every funded item is framed as cost/return, plus a parking lot with its 6-month expiry noted.

## Do NOT

- Do not filter the inventory before scoring - pre-filtering re-introduces the vibes the scoring exists to remove.
- Do not use a finer scale than 1-3. A 1-10 scale invites false precision arguments about whether something is a 6 or a 7; the 2x2 only needs low/medium/high.
- Do not pitch leadership on code quality, elegance, or "best practices" - pitch interest payments and unblocked roadmap.
- Do not schedule low-CoD/high-leverage items standalone; fold them into adjacent feature work or they will lose every prioritization fight.
- Do not let the parking lot skip its quarterly review; items older than 6 months get dropped or re-scored, not carried silently.
- Do not put an XL item on the quarter plan un-decomposed.

## Quality bar

- Every inventory item traces to one of the three signal sources; guesses are labeled.
- Every item has both axis scores, an index, and a quadrant - no unscored pet projects smuggled into "do now".
- The funded list fits the team's realistic capacity after the debt-tax percentage is applied.
- The proposal contains zero architecture jargon a non-engineer executive would need defined.
- Owners are spread beyond the two most senior engineers.
