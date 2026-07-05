---
name: Launch Plan Sequencer
description: Use when planning a product launch end-to-end and you need the full dated timeline. Triggers on "plan my launch", "launch plan", "how do I sequence a launch", "pre-launch checklist", "what happens before launch day", "launch timeline", "GTM launch plan", "coordinate a launch across channels", "who owns what for launch". Builds the pre-launch → launch-day → post-launch arc with a channel & asset checklist, owners, and dates. Do NOT use when you need the minute-by-minute run-of-show for the day itself — use launch-day-runbook instead. Do NOT use to write the core message — use positioning-statement and messaging-hierarchy. Do NOT use to design the self-serve adoption motion — use plg-motion-designer. Do NOT use to build the sales deck, battlecard, or demo script — use sales-enablement-kit.
---

# Launch Plan Sequencer

A launch is not a day; it is an arc. The teams that botch launches treat "launch"
as a single deadline, pile every task onto it, and discover on the morning of that
the demo video is unrendered, sales has never seen the pitch, and nobody owns the
Hacker News thread. Your job is the opposite: stretch the launch into three phases
— **pre-launch**, **launch day**, **post-launch** — and assign every asset and
channel an owner and a date *before* the clock starts.

This skill owns the **sequence and the schedule**. It does not own the strategy
(what to launch, to whom, why now — that is a precondition the team brings in),
it does not own the message itself (that is [[positioning-statement]] and
[[messaging-hierarchy]]), and it does not own the minute-by-minute choreography
of the day itself (that is [[launch-day-runbook]]). You produce the map; those
skills fill in the territory.

## When to use this skill

Reach for it the moment someone says "we're launching" and there is no dated plan.
Run it once the strategy is settled and the message spine exists
([[positioning-statement]], [[messaging-hierarchy]]), and before
[[launch-day-runbook]] sequences the day. Re-run it whenever the launch date
moves — a slipped date silently invalidates every dependency behind it.

## The launch arc

Work backward from the launch date (call it **L**). Everything is relative to L.

### Phase 1 — Pre-launch (L−21d → L−1d): build the ammunition

Nothing ships on launch day that was not finished days before. Pre-launch is where
assets get made, owners get assigned, and the channels get armed.

1. **Lock the launch thesis.** One sentence: who it's for, what changes for them,
   why now. If you cannot write it, you are not ready to launch — settle the
   strategy first. Everything downstream inherits this sentence.
2. **Build the message spine.** The positioning statement and the laddered
   messaging that every asset reuses come from [[positioning-statement]] and
   [[messaging-hierarchy]]. Do this *first* so the landing page, emails, and sales
   deck all say the same thing in the same words.
3. **Commission the assets** (each with an owner and a due date — see the
   checklist below): landing page copy ([[landing-page-copy]]), competitive
   framing, pricing/packaging, the demo. Set every asset due at **L−3d**, not
   L−0 — you need a buffer day for the thing that breaks.
4. **Arm the channels.** For each channel you'll use (see the channel matrix),
   name the owner, draft the post/email, and schedule it. Self-serve / product-led
   distribution mechanics are [[plg-motion-designer]]; the sales team's deck,
   battlecard, and demo script are [[sales-enablement-kit]].
5. **Warm the list.** Tease to your existing audience (email, in-app, social) so
   launch day has an audience, not an empty room. A launch nobody is waiting for
   is a press release into the void.
6. **Dry-run at L−2d.** Click every link, send every email to yourself, load the
   landing page on mobile, confirm the demo renders. The dry run is where you find
   the broken thing while you still have a day to fix it.

### Phase 2 — Launch day (L): execute, don't create

Launch day is pure execution of pre-built ammunition. **If you are writing copy on
launch day, you have already failed.** The hour-by-hour orchestration — publish
order, who hits which channel when, the war-room, the incident path — is owned by
[[launch-day-runbook]]. Your job here is only to confirm the day has: a single
owner (the "launch captain"), a go/no-go check at L−30min, and the assets staged
and scheduled. Then hand off to [[launch-day-runbook]].

### Phase 3 — Post-launch (L+1d → L+30d): convert the spike into a flywheel

The launch spike decays in days. Post-launch turns attention into pipeline and
learnings.

1. **Follow-up sequence (L+1d → L+7d):** thank-you / nurture emails to everyone
   who engaged, a "in case you missed it" resend, and a sales follow-up on every
   warm lead. Cadence and copy reuse [[messaging-hierarchy]].
2. **Amplify the proof (L+2d → L+14d):** customer reactions, press pickups, usage
   milestones — reshare the social proof the launch generated.
3. **Retro (by L+7d):** what worked, what slipped, what each channel actually
   drove. Record the numbers honestly; this is the input to your *next* launch
   plan.
4. **Sustain (L+7d → L+30d):** fold the new message into the always-on motion —
   ongoing content, paid, lifecycle. Hand the durable distribution back to
   [[plg-motion-designer]] and the ongoing sales motion to
   [[sales-enablement-kit]].

## The channel & asset matrix

Every launch plan must answer, for each row: **owner, due date, status.** Cut rows
that don't fit the audience — a developer tool does not need a TikTok — but never
leave a row ownerless.

| Asset / channel | Phase | Owner | Due | Source skill |
|---|---|---|---|---|
| Launch thesis (1 sentence) | Pre | PMM | L−21d | strategy (precondition) |
| Positioning statement | Pre | PMM | L−18d | [[positioning-statement]] |
| Messaging hierarchy | Pre | PMM | L−16d | [[messaging-hierarchy]] |
| Landing / launch page | Pre | Web | L−3d | [[landing-page-copy]] |
| Pricing & packaging | Pre | PMM | L−10d | pricing & packaging |
| Competitive battlecard | Pre | PMM | L−5d | competitive framing |
| Demo / product video | Pre | Product | L−3d | (asset) |
| Sales deck + script | Pre | Sales | L−5d | [[sales-enablement-kit]] |
| In-product launch surface | Pre | Eng | L−1d | [[plg-motion-designer]] |
| Email — warm-up tease | Pre | Lifecycle | L−7d | [[messaging-hierarchy]] |
| Email — launch announce | Day | Lifecycle | L | [[messaging-hierarchy]] |
| Social posts (X/LinkedIn) | Day | Social | L | [[messaging-hierarchy]] |
| Community / forum post | Day | Community | L | — |
| Press / outreach | Day | PMM | L | — |
| Hour-by-hour run-of-show | Day | Launch captain | L | [[launch-day-runbook]] |
| Follow-up nurture sequence | Post | Lifecycle | L+1d | [[messaging-hierarchy]] |
| Launch retro | Post | PMM | L+7d | (this skill) |

## Quality bar

A launch plan is done when:

- **Every row has an owner and a date.** No "TBD", no "the team". One name per
  row, accountable.
- **Nothing is due on L.** Every asset lands at L−3d or earlier; launch day only
  *executes*. The one exception is the live posts/emails that are scheduled but
  fire on the day.
- **Dependencies point the right way.** The message spine
  ([[positioning-statement]], [[messaging-hierarchy]]) is due before any asset
  that reuses it. If the landing page is due before the messaging, the plan is
  wrong.
- **There is a buffer day** between "assets done" (L−3d) and launch (L), and a
  dry-run at L−2d.
- **Post-launch is planned, not improvised.** The follow-up sequence and retro
  have dates *before* launch day, or they will never happen.
- **The day itself is handed off** to [[launch-day-runbook]], not improvised here.

## Do NOT

- **Do NOT pile tasks onto launch day.** If the plan has assets "due" on L, it is
  a deadline, not a sequence. Pull them back to L−3d.
- **Do NOT write the minute-by-minute day-of schedule here.** That is
  [[launch-day-runbook]]'s job; duplicating it splits the source of truth and the
  two will drift.
- **Do NOT re-derive the message or the strategy.** The one-line claim is
  [[positioning-statement]] and the laddered copy is [[messaging-hierarchy]];
  strategy and pricing are settled upstream. Cross-link; do not rewrite.
- **Do NOT launch to an empty room.** No warm-up = no audience. The tease in
  pre-launch is not optional.
- **Do NOT skip the dry run.** The cost of finding a broken link at L−2d is a
  five-minute fix; finding it at L is a public failure.
- **Do NOT forget post-launch.** The spike decays in days. A plan that ends at L
  leaves most of the value on the table.

## Runnable artifact — the sequencer

Give the script your launch date and it prints the dated, phased plan with owners.
Drop it in `launch_sequencer.py` and run `python launch_sequencer.py 2026-09-15`.
It resolves every L−offset to a real calendar date so the plan is a schedule, not
a wish list.

```python
#!/usr/bin/env python3
"""Launch Plan Sequencer — turn a launch date into a dated, owned, phased plan.
Usage: python launch_sequencer.py YYYY-MM-DD
"""
import sys
from datetime import date, timedelta

# (label, phase, owner, offset_days_from_L, source_skill)
PLAN = [
    ("Launch thesis (1 sentence)",   "pre",  "PMM",            -21, "strategy (precondition)"),
    ("Positioning statement",        "pre",  "PMM",            -18, "positioning-statement"),
    ("Messaging hierarchy",          "pre",  "PMM",            -16, "messaging-hierarchy"),
    ("Pricing & packaging",          "pre",  "PMM",            -10, "—"),
    ("Warm-up tease email",          "pre",  "Lifecycle",       -7, "messaging-hierarchy"),
    ("Sales deck + script",          "pre",  "Sales",           -5, "sales-enablement-kit"),
    ("Competitive battlecard",       "pre",  "PMM",             -5, "—"),
    ("Landing / launch page",        "pre",  "Web",             -3, "landing-page-copy"),
    ("Demo / product video",         "pre",  "Product",         -3, "—"),
    ("Dry run (click every link)",   "pre",  "Launch captain",  -2, "—"),
    ("In-product launch surface",    "pre",  "Eng",             -1, "plg-motion-designer"),
    ("Go / no-go check",             "day",  "Launch captain",   0, "launch-day-runbook"),
    ("Run-of-show (hour by hour)",   "day",  "Launch captain",   0, "launch-day-runbook"),
    ("Launch announce email",        "day",  "Lifecycle",        0, "messaging-hierarchy"),
    ("Social + community + press",   "day",  "Social/PMM",       0, "—"),
    ("Follow-up nurture sequence",   "post", "Lifecycle",        1, "messaging-hierarchy"),
    ("Amplify social proof",         "post", "Social",           2, "—"),
    ("Launch retro",                 "post", "PMM",              7, "this skill"),
    ("Fold into always-on motion",   "post", "Growth",          14, "plg-motion-designer"),
]

PHASE_LABEL = {"pre": "PRE-LAUNCH", "day": "LAUNCH DAY", "post": "POST-LAUNCH"}


def main() -> None:
    if len(sys.argv) != 2:
        sys.exit("usage: python launch_sequencer.py YYYY-MM-DD")
    L = date.fromisoformat(sys.argv[1])
    print(f"Launch date L = {L:%a %Y-%m-%d}\n")
    current = None
    for label, phase, owner, off, src in PLAN:
        if phase != current:
            current = phase
            print(f"== {PHASE_LABEL[phase]} ==")
        when = L + timedelta(days=off)
        rel = "L" if off == 0 else (f"L{off}" if off < 0 else f"L+{off}")
        src = "" if src == "—" else f"   <- {src}"
        print(f"  {when:%a %Y-%m-%d}  [{rel:>4}]  {owner:<14} {label}{src}")


if __name__ == "__main__":
    main()
```

Worked example — `python launch_sequencer.py 2026-09-15`:

```
Launch date L = Tue 2026-09-15

== PRE-LAUNCH ==
  Tue 2026-08-25  [ L-21]  PMM            Launch thesis (1 sentence)   <- strategy (precondition)
  Fri 2026-08-28  [ L-18]  PMM            Positioning statement   <- positioning-statement
  Sun 2026-08-30  [ L-16]  PMM            Messaging hierarchy   <- messaging-hierarchy
  Sat 2026-09-05  [ L-10]  PMM            Pricing & packaging
  Tue 2026-09-08  [  L-7]  Lifecycle      Warm-up tease email   <- messaging-hierarchy
  Thu 2026-09-10  [  L-5]  Sales          Sales deck + script   <- sales-enablement-kit
  Thu 2026-09-10  [  L-5]  PMM            Competitive battlecard
  Sat 2026-09-12  [  L-3]  Web            Landing / launch page   <- landing-page-copy
  Sat 2026-09-12  [  L-3]  Product        Demo / product video
  Sun 2026-09-13  [  L-2]  Launch captain Dry run (click every link)
  Mon 2026-09-14  [  L-1]  Eng            In-product launch surface   <- plg-motion-designer

== LAUNCH DAY ==
  Tue 2026-09-15  [   L]  Launch captain Go / no-go check   <- launch-day-runbook
  Tue 2026-09-15  [   L]  Launch captain Run-of-show (hour by hour)   <- launch-day-runbook
  Tue 2026-09-15  [   L]  Lifecycle      Launch announce email   <- messaging-hierarchy
  Tue 2026-09-15  [   L]  Social/PMM     Social + community + press

== POST-LAUNCH ==
  Wed 2026-09-16  [  L+1]  Lifecycle      Follow-up nurture sequence   <- messaging-hierarchy
  Thu 2026-09-17  [  L+2]  Social         Amplify social proof
  Tue 2026-09-22  [  L+7]  PMM            Launch retro   <- this skill
  Tue 2026-09-29  [ L+14]  Growth         Fold into always-on motion   <- plg-motion-designer
```

Notice what the schedule enforces: the message spine lands first, every asset is
done by L−3d, there is a dry run at L−2d, launch day only executes, and post-launch
is already on the calendar. Edit the `PLAN` list to add or cut rows for your
audience — the dates and phases recompute themselves.

## Deliverable

A dated, phased launch plan: the pre-launch → launch-day → post-launch arc with
every channel and asset assigned an owner and a due date relative to L, the
message spine sequenced before its dependents, a dry-run and buffer day before
launch, and a post-launch follow-up + retro already scheduled. Hand the day-of
run-of-show to [[launch-day-runbook]] and the durable distribution to
[[plg-motion-designer]] and [[sales-enablement-kit]].
