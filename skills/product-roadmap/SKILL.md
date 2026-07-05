---
name: Product Roadmap
description: Turns product strategy into a themed Now/Next/Later roadmap with explicit trade-offs, confidence levels, and no committed dates beyond one quarter. Use when someone asks "build our product roadmap", "turn these priorities into a roadmap", "execs want committed ship dates for next year", or needs to communicate direction without over-promising. Do NOT use for company-wide annual operating plans — use annual-plan instead; for defining the goals a roadmap serves, use okr-builder; for scheduling work inside a sprint, use sprint-planning.
---

# Product Roadmap

A roadmap is a communication tool, not a promise to ship a feature list on fixed dates. The costly failure it prevents is double-sided: a Gantt chart of dated features sets the team up to publicly "miss the roadmap" within two months, while a roadmap with no trade-offs is a wishlist that aligns nobody. This skill builds one that aligns the organization around outcomes and survives contact with reality.

## Operating procedure

### Step 1: Gather inputs and anchor to strategy

Collect before building; where an input is missing, that is upstream work, not a blank to improvise:

- Vision — where the product is going in 1–3 years.
- Goals — the 2–4 outcomes for this period (growth, retention, expansion). More than 4 goals means no goals; send goal-setting itself to okr-builder.
- Target users and problems — who is served and what pain is being solved.
- The candidate list: everything currently being considered, with rough effort and any evidence of value.

Every roadmap item must trace back to a goal. An item that traces to no goal gets questioned, and either a goal is missing or the item is.

### Step 2: Organize by themes, not features

Group work into themes — problem areas and outcomes, such as "Reduce onboarding friction", never "Build feature X". Themes survive when specific solutions change, communicate intent without over-promising implementation, and leave teams free to find the best solution inside the theme. If a theme name contains a solution noun (a screen, an integration, a widget), rewrite it as the problem it solves.

### Step 3: Bucket into Now / Next / Later — and hold the date line

Use horizon buckets instead of dates:

- Now (this quarter): committed, scoped, in motion. This is the only bucket that may carry dates, and only within the quarter.
- Next (next quarter): high-confidence direction, not fully scoped. No dates.
- Later (beyond): directional bets that will change. No dates, ever.

The discipline: no committed dates beyond one quarter. Confidence decreases with distance, and precision implies a promise. When an exec asks for a date on a Later item, the correct answer is the horizon plus its confidence level — a date given under pressure today becomes a public miss next quarter, and each miss devalues every future roadmap. Label every item's confidence explicitly: High / Medium / Low. A useful sanity check: Now items should be High confidence, Next mostly Medium, Later mostly Low — a Later item marked High confidence usually means it belongs in Next.

### Step 4: Make trade-offs visible

For each major theme, record:

- Why this, why now — the rationale tied to a goal.
- What we're NOT doing — the explicit trade-off. Saying no is the roadmap's real value; without a not-doing line, the roadmap is a wishlist.
- Key assumptions and risks — what new information would change the plan.

### Step 5: Prioritize with a consistent framework

Score candidates with one framework — RICE (Reach, Impact, Confidence, Effort), value vs. effort, or weighted goal alignment — so prioritization is defensible rather than loudest-voice-wins. Re-score when new information arrives; a framework applied once and frozen is theater.

### Step 6: Communicate and maintain

- Tailor the view: execs get themes and outcomes; engineering gets Now/Next detail.
- Revisit monthly. A roadmap is a living document, not a contract; a stale roadmap loses all credibility at once, not gradually.
- Pair with metrics: report progress against each theme's success measure, not feature ship counts.

## Roadmap row template

```
Theme: [FILL: outcome-oriented name — a problem, not a feature]
Goal it serves: [FILL: strategic goal]
Horizon: Now | Next | Later
Confidence: High | Medium | Low
Why now: [FILL: rationale]
Not doing: [FILL: the explicit trade-off]
Success measure: [FILL: how we'll know it worked]
```

## Worked contrast: one entry, bad and good

Bad:

```
Theme: Build Slack integration
Horizon: Q3, ships Sept 15
Why now: Sales asked for it
```

Why it fails: the theme is a solution, not a problem; it carries a committed date two quarters out; the rationale traces to a request, not a goal; there is no confidence, no trade-off, and no success measure. In two months this row is either a public miss or an anchor the team ships badly to honor.

Good:

```
Theme: Meet users inside their existing workflow
Goal it serves: Activation — lift week-1 active rate
Horizon: Next
Confidence: Medium
Why now: 40% of churned trials cite "another tool to check"; workflow embedding
  is the highest-scored RICE candidate against the activation goal
Not doing: native mobile app this period — same goal, 3x the effort, lower reach
Success measure: week-1 active rate for integrated users vs. baseline
```

Why it works: problem-framed theme (Slack may still be the answer, but so might email or Teams), honest horizon with matching confidence, goal-traced rationale with evidence, a named sacrifice, and a measurable outcome.

## Deliverable

Produce a complete roadmap: the strategy anchor (vision, 2–4 goals, target users/problems), themed entries in Now/Next/Later buckets each using the row template with confidence, not-doing, and success measure filled, the prioritization scores behind the ordering, and the two communication views (exec theme view, engineering Now/Next view) with a monthly revisit date set.

## Do NOT

- Do not build a Gantt chart of features with fixed dates — it manufactures public misses and trains the org to distrust the roadmap.
- Do not commit dates beyond one quarter — precision at distance is a promise the data cannot back.
- Do not list solutions instead of problems — solution-named themes box the team into the first idea anyone had.
- Do not omit the "not doing" line — without visible trade-offs the roadmap is a wishlist and every stakeholder assumes their item is implied.
- Do not let prioritization be loudest-voice-wins — score with one framework and show the scores.
- Do not let it go stale — an unrevised roadmap is worse than none, because people plan against it.

## Quality bar

- Every item traces to one of 2–4 named goals; untraceable items removed or a missing goal surfaced.
- Every theme is problem/outcome-framed — zero solution nouns in theme names.
- Dates appear only inside the current quarter; Next and Later carry horizons and confidence only.
- Confidence labels follow the gradient (Now high, Later low) or the mismatch is explained.
- Every theme has a not-doing trade-off and a success measure.
- A monthly review is scheduled and the framework scores are attached, not implied.
