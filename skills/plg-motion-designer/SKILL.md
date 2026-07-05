---
name: PLG Motion Designer
description: Use when designing a product-led, self-serve activation motion for signups. Triggers on "design our PLG motion", "self-serve onboarding", "what is our aha moment", "define activation", "activation rate", "time-to-value", "onboarding funnel", "in-product nudges", "set activation milestones", "signup to value", "free-to-paid", "PQL". Defines the aha moment, the activation milestones to it, and the in-product nudges and metric gates between each step. Do NOT use for outbound/sales-led launch sequencing — use [[launch-plan-sequencer]] instead; for arming a human sales team with collateral, use [[sales-enablement-kit]] instead; for the page that captures the signup, use [[landing-page-copy]]; for pricing tiers and the paywall, use [[saas-pricing]] and [[pricing-strategy]].
---

# PLG Motion Designer

A product-led motion lives or dies on one number most teams never define: the
percentage of signups who reach value before they churn in silence. You design
the path from `signup → aha moment → activated` and instrument the gates between
each step, so the product sells itself instead of waiting on a human. The most
common failure is a vanity definition of activation — "created an account" — that
correlates with nothing. This skill fixes the definition first, then builds the
motion around it.

## When to use this skill

Reach for it once positioning and the offer are set ([[positioning-statement]],
[[messaging-hierarchy]]) and the self-serve product can be signed up for without
a sales call. It owns the *in-product* journey; the acquisition page is
[[landing-page-copy]], the paywall economics are [[saas-pricing]] /
[[pricing-strategy]], and the broad launch plan is [[go-to-market-planner]] and
[[launch-plan-sequencer]]. Run it before [[launch-day-runbook]] so the motion is
live and measured when traffic hits.

## Step 1 — Define the aha moment off behavior, not opinion

The aha moment is the *first* in-product action after which a user's odds of
retaining jump sharply. Find it, do not guess it:

1. List the 8-12 candidate first-week actions a user can take.
2. For each, compare week-4 retention of users who did it vs. didn't. The aha
   action is the one with the widest retention gap that a *new* user can plausibly
   reach in the first session.
3. Pin it to a number, not a vibe: not "sends a message" but "sends a message to
   a second person." Slack's was ~2,000 messages sent by a team; the shape
   (frequency x breadth x time) matters more than the figure.

A crisp aha definition reads: **"<count> of <core action> across <breadth> within
<time window>."** If you cannot fill that template, you do not yet know your aha
moment — and every downstream nudge is guesswork.

## Step 2 — Reverse-engineer activation milestones to it

Activation is reaching the aha moment. Work backward and name the 3-5 *ordered*
milestones a user must clear to get there. Each milestone is a discrete, logged
event — the rungs of the ladder:

```
  M0  Signup            account created
  M1  Setup             the one config without which value is impossible
  M2  First action      core action done once (the "empty state" defeated)
  M3  Aha moment        the Step-1 definition is met  ← ACTIVATED
  M4  Habit / PQL       repeated value → ready to convert (hand to saas-pricing)
```

Rules that keep the ladder honest:
- Strip every step that is not on the critical path to value. Each extra step
  leaks users.
- Order them; a milestone a user reaches *out of order* is mislabeled.
- M2 is the empty-state killer — the single hardest drop, where a blank product
  meets a new user. Design it deliberately (templates, sample data, a guided
  first action).

## Step 3 — Gate each step with a metric

Every transition between milestones gets two numbers, and you do not move on
until they are instrumented:

- **Step conversion rate** — % who advance Mₙ → Mₙ₊₁. This localizes the leak.
- **Time-to-value (TTV)** — median time from signup to M3 (aha). The headline
  speed metric; long TTV silently kills self-serve.
- **Activation rate** — % of signups who reach M3 within the TTV window. The one
  number the whole motion optimizes.

The biggest single drop in step conversion is your bottleneck — fix it before
touching anything else. Optimizing a 90% step while a 30% step bleeds upstream is
the classic waste.

## Step 4 — Place in-product nudges only on the gaps

Nudges are interventions to lift a specific step's conversion — never decoration.
For each *leaking* step, pick the lightest nudge that moves it:

| Leak | Nudge | Note |
|---|---|---|
| Stalls at M1 setup | Inline checklist / progress bar | Show the path; reduce the unknown |
| Empty state at M2 | Templates, sample data, guided first action | Defeat the blank screen |
| Drops before aha | Contextual tooltip at the moment of need | In-product beats email here |
| Goes dark after signup | Lifecycle email tied to the *missed* milestone | Behavior-triggered, not drip |
| Hits aha, no repeat | Habit loop: trigger → action → reward | Build toward M4 / PQL |

Discipline: one nudge per leak, instrument its effect, keep it only if step
conversion rises. Over-nudging (badges, popups, tours everywhere) trains users to
dismiss everything and buries the one that matters.

## Runnable artifact — activation funnel diagnostic

Drop your per-milestone counts in; it returns step conversion, where the funnel
leaks worst, TTV, and the overall activation rate. Self-contained Python.

```python
# activation_funnel.py  —  python3 activation_funnel.py
from statistics import median

# Ordered milestones: signup is M0, aha is the activation gate.
MILESTONES = ["M0 signup", "M1 setup", "M2 first action", "M3 aha (ACTIVATED)"]
COUNTS     = [10000,        6200,        3900,             2600]   # users reaching each
AHA_INDEX  = 3                                                     # index of the aha milestone
TTV_HOURS  = [0.5, 6.0, 22.0, 34.0, 48.0, 70.0]                   # signup→aha per activated user

def report():
    print(f"{'transition':<34}{'reached':>9}{'step conv':>11}")
    worst = ("", 1.0)
    for i in range(1, len(MILESTONES)):
        conv = COUNTS[i] / COUNTS[i - 1]
        arrow = f"{MILESTONES[i-1].split()[0]} -> {MILESTONES[i].split()[0]}"
        print(f"{arrow:<34}{COUNTS[i]:>9}{conv:>10.0%}")
        if conv < worst[1]:
            worst = (arrow, conv)
    activation = COUNTS[AHA_INDEX] / COUNTS[0]
    print("-" * 54)
    print(f"activation rate (signup -> aha){'':<3}{activation:>20.1%}")
    print(f"median time-to-value (hours){'':<6}{median(TTV_HOURS):>20.1f}")
    print(f"biggest leak{'':<22}{worst[0]:>16}  ({worst[1]:.0%})")

report()
```

Worked output for the sample numbers above:

```
transition                          reached  step conv
M0 -> M1                               6200        62%
M1 -> M2                               3900        63%
M2 -> M3                               2600        67%
------------------------------------------------------
activation rate (signup -> aha)                  26.0%
median time-to-value (hours)                      28.0
biggest leak                          M0 -> M1  (62%)
```

Read: 26% activation with the worst leak at the very first setup step (M0→M1) and
a 28-hour TTV. The move is a Step-4 nudge on setup (inline checklist) and a hard
look at why first value takes a day — not a new feature, not more signups.

## Fill-in template — the activation spec

```
Aha moment:    <count> of <core action> across <breadth> within <time window>
Activation =   reaching the aha moment within <TTV window>

Milestones (ordered, each a logged event):
  M0 Signup        → event: ______
  M1 Setup         → event: ______   gate: step conv ____%  nudge: ______
  M2 First action  → event: ______   gate: step conv ____%  nudge: ______
  M3 Aha/ACTIVATED → event: ______   gate: step conv ____%
  M4 Habit / PQL   → event: ______   (hand to saas-pricing for conversion)

North-star gate:   activation rate = ____%   (target: ____%)
Speed gate:        median TTV = ____         (target: ____)
Current bottleneck: step ______ at ____%  →  intervention: ______
```

## Quality bar

- The aha moment fits the `<count> of <action> across <breadth> within <window>`
  template and is backed by a retention gap, not an opinion.
- Every milestone is a discrete logged event, ordered, on the critical path to
  value — no decorative steps.
- Each transition has a step-conversion number and the whole motion has an
  activation rate and a median TTV. If a step is uninstrumented, the motion is
  not done.
- Exactly one nudge sits on each *leaking* step, and each nudge has a measured
  before/after on that step's conversion.
- The single biggest leak is named, and the recommended action targets it — not
  a healthier step.
- M4/PQL hands cleanly to [[saas-pricing]] / [[pricing-strategy]] for conversion;
  this skill stops at activation.

## Do NOT

- Do NOT define activation as "signed up" or "completed onboarding tour." Those
  correlate with nothing; activation is reaching *value*.
- Do NOT optimize a healthy step while an upstream step bleeds. Fix the biggest
  leak first.
- Do NOT carpet the product in tours, badges, and popups. One nudge per leak,
  kept only if it moves the number.
- Do NOT use this for sales-led or outbound launch sequencing — that is
  [[launch-plan-sequencer]] and [[launch-day-runbook]].
- Do NOT use this to arm a human sales team with decks, one-pagers, or objection
  handling — a PLG motion replaces the rep; for the sales-led path use
  [[sales-enablement-kit]] instead.
- Do NOT design the signup/acquisition page or the paywall here — that is
  [[landing-page-copy]] and [[saas-pricing]] / [[pricing-strategy]].
- Do NOT invent the aha-moment number to look good in a deck. An unbacked
  activation target sends the whole motion chasing the wrong behavior.
- Do NOT confuse a long TTV with a feature gap; it is usually a leaking step or a
  missing nudge, found in Step 3.

## Deliverable

A one-page activation spec: the aha-moment definition with its retention backing,
the ordered milestone ladder, the step-conversion / TTV / activation-rate gates,
one nudge per leaking step with its measured lift, the named current bottleneck
and the intervention for it, and a clean handoff at M4/PQL to
[[saas-pricing]] for conversion. Fits inside the GTM launch built by
[[go-to-market-planner]] and [[launch-plan-sequencer]].
