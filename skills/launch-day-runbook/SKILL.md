---
name: launch-day-runbook
description: Use when you are running an actual launch day in real time across channels. Triggers on "launch day runbook", "we launch tomorrow", "Product Hunt launch day plan", "hour by hour launch schedule", "who does what on launch day", "PH / Hacker News / Twitter / email sequencing", "launch day checklist", "our launch is going sideways", "contingency if we get rate-limited / Show HN flops / site goes down". Turns the plan into a minute-by-minute war room. Do NOT use when you are still picking the launch date, beats, or channel mix — use [[launch-plan-sequencer]] for the calendar first; this is the execution companion that runs the day it lands on.
---

# Launch Day Runbook

A launch is won or lost in the first six hours, and almost every failure is
operational, not strategic: the email went out before the Product Hunt page was
live, nobody was awake when the ranking slipped, the founder spent the morning
rewriting copy instead of replying to commenters. This skill is the war-room
runbook — an hour-by-hour script with named owners, a monitoring loop, and
pre-decided contingencies — so launch day is execution, not improvisation.

This is the *execution* companion. The strategy is already set upstream:
[[launch-plan-sequencer]] owns the date and beat calendar, [[positioning-statement]]
and [[messaging-hierarchy]] own the words, [[plg-motion-designer]] owns what
happens after signup, and [[sales-enablement-kit]] arms the team for the inbound
this creates. Do not re-litigate any of those today. Today you run the play.

## When to use this skill

Reach for it once the date is locked and you are within 48 hours of launch, and
keep it open *during* the day itself. If you are still choosing the date,
ordering the beats, or deciding which channels to fire, stop and use
[[launch-plan-sequencer]] first — a runbook for a plan that does not exist yet is
theater.

## The ordered workflow

Run these in order. Steps 1–3 happen the day before; 4–8 are the live day.

1. **Freeze scope the day before.** No new copy, no new features, no new
   channels after T-minus-24h. Lock the asset list: PH gallery + first comment,
   the [[messaging-hierarchy]] one-liner, the launch tweet/thread, the LinkedIn
   post, the email, the changelog entry, the landing page. Ship the
   [[landing-page-copy]] and pricing the night before, not at 6am.
2. **Assign owners to every channel and every contingency.** Each line in the
   runbook has exactly one name. The four roles that must be filled: *Captain*
   (calls audibles, owns the schedule), *Comments* (replies on PH/HN/social
   within minutes), *Amplify* (mobilizes the warm list and reshares), *Ops*
   (watches the site, signup flow, and metrics). One person can wear two hats;
   no line can be ownerless.
3. **Pre-stage and pre-write everything.** Draft the first PH comment, the HN
   text post, the thank-you replies, and the "we're #1" / "we slipped, push
   now" rally messages *before* the day. Schedule nothing that depends on the PH
   page being live (the email, the broad social) until a human confirms the page
   renders for a logged-out visitor.
4. **Launch the anchor first, in sequence.** The canonical order: **PH goes live
   at 12:01am PT → founder first comment within 2 min → notify the warm inner
   circle (not a blast) → confirm page is publicly visible → THEN fire the email
   and broad social a few hours later when there's ranking momentum to point at.**
   Hacker News (Show HN) is a *separate, hands-off* bet — submit it as its own
   plain post, never cross-post "please upvote" (that gets you flagged); let it
   live or die on the title and first comment.
5. **Run the amplification waves, not one blast.** Sequence outreach so momentum
   compounds instead of spiking and dying: inner circle → team & advisors →
   broad email → broad social → communities where you're a genuine member. Each
   wave links to the same anchor and references real traction ("#3 and climbing")
   rather than a generic "we launched."
6. **Work the comments like it's the product.** Reply to every PH/HN/social
   comment fast and human — the founder, not a brand voice. Comment velocity and
   maker engagement drive PH ranking more than raw vote count. Pull objection
   answers straight from [[sales-enablement-kit]]; never argue on HN, answer the
   technical question and move on.
7. **Monitor on a fixed cadence, not vibes.** Every 30 minutes the Captain reads
   the dashboard aloud (see the artifact below): rank, vote/comment velocity,
   signups, site health, top objection. Decisions are made off numbers, not the
   group chat mood.
8. **Close the day deliberately.** A final-hours push to the warm list, a
   thank-you comment, then *stop* — do not flog a dead post past midnight.
   Capture the numbers and the top three objections for tomorrow's follow-up and
   for [[plg-motion-designer]] to act on the signups you just earned.

## Contingency table (decide these before, not during)

| If this happens | The owner | does this, immediately |
|---|---|---|
| Rank slips below #5 by mid-morning | Captain + Amplify | fire the next amplification wave early; DM 10 warm supporters directly, no mass beg |
| Site/signup is down or slow | Ops | flip to status page + waitlist capture; Captain posts an honest "scaling, hang tight" comment — never go silent |
| Show HN gets flagged or buried | Captain | leave it; do NOT resubmit or ask for votes (both make it worse). Redirect energy to PH/social |
| A hostile/skeptical top comment | Comments | answer once, factually, with the [[messaging-hierarchy]] proof point; do not get dragged into a thread war |
| Email didn't send / broke | Ops | hold all downstream social that references it; resend to a test segment before re-blasting |
| You hit #1 early | Amplify | bank it — screenshot, post the social proof, ride it into the broad waves |

## Quality bar

A runbook is ready to run only if all of these are true:

- Every channel and every contingency row has exactly one named owner — zero
  ownerless lines.
- The sequence has an explicit anchor-first order with a human gate: the email
  and broad social literally cannot fire until someone confirms the PH page
  renders logged-out.
- All copy and assets are frozen and pre-written at T-24h; nothing creative is
  authored on launch morning.
- There is a fixed monitoring cadence (e.g. every 30 min) with a named metric
  set, not "keep an eye on it."
- Hacker News is treated as a hands-off, no-vote-begging bet, separate from the
  orchestrated PH push.
- The day has a defined *end* and a handoff: numbers captured, top objections
  logged, signups routed to [[plg-motion-designer]].

## Do NOT

- **Do NOT** blast the email and broad social before the PH page is confirmed
  live and publicly visible — the #1 launch-day own-goal.
- **Do NOT** ask for upvotes anywhere, ever (PH and HN both penalize it). Ask
  people to "check it out," never to vote.
- **Do NOT** cross-post the same "we launched 🚀" blast identically to every
  channel — sequence waves that reference live traction instead.
- **Do NOT** write or rewrite positioning, pricing, or landing copy on launch
  morning. That work belongs to [[positioning-statement]] and
  [[landing-page-copy]], finished the night before.
- **Do NOT** run the day on vibes from the group chat — run it on the 30-minute
  dashboard read.
- **Do NOT** let it bleed past a defined end. A tired team flogging a dead post
  at 1am converts no one and wrecks tomorrow.

## Runnable artifact — launch-day timeline generator

Feed it your launch date (PT) and it prints the hour-by-hour runbook with owners
and the sequenced waves. Self-contained, standard library only.

```python
#!/usr/bin/env python3
"""launch_runbook.py — generate an hour-by-hour launch-day runbook.

Usage: python launch_runbook.py 2026-07-15
"""
import sys
from datetime import datetime, timedelta

# (offset_minutes_from_PH_go_live, owner, action). PH goes live 12:01am PT.
TIMELINE = [
    (0,    "Captain",  "Product Hunt post goes LIVE (12:01am PT). Confirm it renders logged-out."),
    (2,    "Captain",  "Founder posts the first comment (pre-written: story + ask to 'check it out')."),
    (10,   "Amplify",  "Notify INNER CIRCLE only (DMs) — no mass blast yet."),
    (30,   "Comments", "Reply to every early comment, human voice, < 5 min each."),
    (180,  "Captain",  "Confirm ranking momentum, THEN green-light email + broad social."),
    (185,  "Ops",      "Fire the launch EMAIL to the broad list (links to PH anchor)."),
    (200,  "Amplify",  "Wave 2: team + advisors reshare the launch thread."),
    (240,  "Captain",  "Submit Show HN as its OWN post. Hands off after — no vote-begging."),
    (300,  "Amplify",  "Wave 3: broad social (Twitter/X thread, LinkedIn) referencing live rank."),
    (420,  "Amplify",  "Wave 4: communities where you're a real member."),
    (720,  "Captain",  "Midday checkpoint: read dashboard, decide if an early wave is needed."),
    (1140, "Amplify",  "Final-hours push to warm list ('last few hours to back us')."),
    (1320, "Captain",  "Thank-you comment. STOP. Capture numbers + top 3 objections. Hand to plg-motion-designer."),
]

MONITOR_EVERY_MIN = 30
METRICS = ["rank", "vote velocity", "comment velocity", "signups", "site health", "top objection"]

def main():
    if len(sys.argv) != 2:
        sys.exit("usage: python launch_runbook.py YYYY-MM-DD")
    day = datetime.strptime(sys.argv[1], "%Y-%m-%d")
    go_live = day.replace(hour=0, minute=1)  # 12:01am PT

    print(f"LAUNCH DAY RUNBOOK — {day:%A %b %d, %Y} (all times PT)\n")
    print("SEQUENCE (anchor-first; email/social gated on confirmed PH page):")
    for off, owner, action in TIMELINE:
        t = go_live + timedelta(minutes=off)
        print(f"  {t:%I:%M%p}  [{owner:<8}] {action}")

    print(f"\nMONITORING LOOP — Captain reads aloud every {MONITOR_EVERY_MIN} min:")
    print("  " + "  |  ".join(METRICS))
    print("\nReminder: never ask for upvotes. Ask people to 'check it out'.")

if __name__ == "__main__":
    main()
```

Worked example — `python launch_runbook.py 2026-07-15`:

```
LAUNCH DAY RUNBOOK — Wednesday Jul 15, 2026 (all times PT)

SEQUENCE (anchor-first; email/social gated on confirmed PH page):
  12:01AM  [Captain ] Product Hunt post goes LIVE (12:01am PT). Confirm it renders logged-out.
  12:03AM  [Captain ] Founder posts the first comment (pre-written: story + ask to 'check it out').
  12:11AM  [Amplify ] Notify INNER CIRCLE only (DMs) — no mass blast yet.
  12:31AM  [Comments] Reply to every early comment, human voice, < 5 min each.
  03:01AM  [Captain ] Confirm ranking momentum, THEN green-light email + broad social.
  03:06AM  [Ops     ] Fire the launch EMAIL to the broad list (links to PH anchor).
  03:21AM  [Amplify ] Wave 2: team + advisors reshare the launch thread.
  04:01AM  [Captain ] Submit Show HN as its OWN post. Hands off after — no vote-begging.
  05:01AM  [Amplify ] Wave 3: broad social (Twitter/X thread, LinkedIn) referencing live rank.
  07:01AM  [Amplify ] Wave 4: communities where you're a real member.
  12:01PM  [Captain ] Midday checkpoint: read dashboard, decide if an early wave is needed.
  07:01PM  [Amplify ] Final-hours push to warm list ('last few hours to back us').
  10:01PM  [Captain ] Thank-you comment. STOP. Capture numbers + top 3 objections. Hand to plg-motion-designer.

MONITORING LOOP — Captain reads aloud every 30 min:
  rank  |  vote velocity  |  comment velocity  |  signups  |  site health  |  top objection

Reminder: never ask for upvotes. Ask people to 'check it out'.
```

Tune the offsets to your audience's timezone and the channels you actually run,
but keep the invariants: anchor first, human-gated email, waves not blasts, a
fixed monitoring loop, and a hard stop with a handoff.

## Deliverable

A filled-in launch-day runbook: the sequenced hour-by-hour timeline with one
named owner per line, the four-role assignment (Captain / Comments / Amplify /
Ops), the pre-written asset list frozen at T-24h, the contingency table with
owners, the 30-minute monitoring cadence and metric set, and the end-of-day
handoff (numbers + top objections routed to [[plg-motion-designer]] and
[[sales-enablement-kit]]).
