---
name: Email Drip Builder
description: Designs automated onboarding and activation drip sequences with behavior-gated sends, exit triggers on conversion, and per-email jobs and timing. Use when someone asks "build an onboarding email sequence", "design a drip campaign for new signups", "why isn't our onboarding flow activating users", or is extending an existing drip series. Do NOT use for recurring content newsletters - use email-newsletter-pro; for recovering abandoned checkouts use abandoned-cart-sequence; for re-engaging dormant users use win-back-campaign; for cold outreach to prospects use outreach-sequence-designer or cold-email-craft.
---

# Email Drip Builder

A drip sequence is a contract with the user: they gave their attention, and the sequence returns it with value at the right moment. Sequences that read like mail merges get unsubscribes; sequences timed to behavior build habits. The costly mistake this skill prevents is the fixed-timer drip that keeps selling activation to users who already activated - the fastest way to teach a new user that these emails can be ignored.

## Inputs to collect

1. **The activation milestone** - the one action that means the user is activated (first invoice sent, first project shared). This is the exit trigger for the entire sequence. If undefined, define it first - a drip without an exit condition is a leak, not a funnel. Pull it from lifecycle-journey-map if that map exists.
2. **The intermediate milestones** - 2-4 steps between signup and activation, each trackable as an event.
3. **Current activation rate and where users stall** - determines which email gets the most work.
4. **Independently useful assets** - tips, benchmarks, shortcuts, case studies each email can deliver.
5. **Sending constraints** - ESP capabilities for event-based triggers vs. timers; if only timers exist, flag the sequence as degraded and say which rules cannot be enforced.

## Operating procedure

1. **Define the exit triggers before writing any email.** Primary: the user completes the activation milestone → leave the sequence immediately and move to the engagement track. Secondary: conversion to paid, unsubscribe, or hard bounce → leave immediately. Sending onboarding to activated or converted users erodes trust and skews every metric downstream.
2. **Anchor Email 1 on the first value moment.** It sends immediately on signup and has one job: the fastest path to core value. No feature tour, no company history. One action, one link. Subject states the outcome, not the product name.
3. **Gate Emails 2+ on behavior gaps, not timers.** Each email fires only if the user has *not* done the target action; if they did it, skip forward to the next milestone's email. Fixed-timer sequences waste sends on the already-activated.
4. **Give every email an independent payload.** Each must deliver something useful on its own - a tip, a benchmark, a shortcut, a case study - so a user who unsubscribes after Email 3 still got value. This lowers unsubscribe pressure and builds sender trust.
5. **Write subjects as promises, not headlines.** The body must pay off the subject exactly. Bait-and-switch subjects boost opens and tank clicks. The best subjects are specific and slightly incomplete - the body finishes the thought.
6. **Cap the sequence at 8 emails.** If a user has not activated after eight, a ninth will not fix it. Route them to a dormant segment and hand them to win-back-campaign. Endless drips create unsubscribe pressure across the whole list.
7. **Instrument per-email metrics** - open, click, and the target action's completion - so the stalling step is visible, and the email covering it gets rewritten first.

## Worked artifact - 5-email activation drip (project-management SaaS; activation = first project shared with a teammate)

| # | Timing | Skip-if condition | Job | Subject (promise) | CTA |
|---|---|---|---|---|---|
| 1 | Immediately on signup | never skipped | Fastest path to first value: create a project in 3 minutes | "Your first project, live in 3 minutes" | Create your project |
| 2 | +2 days, only if no project created | project created | Remove the setup objection with a template shortcut | "Skip setup - start from this template" | Use the template |
| 3 | +2 days after email 2, only if project exists but nothing shared | project shared | Push to the activation milestone: invite a teammate | "Projects shared with a teammate finish 2x faster" | Invite one person |
| 4 | +3 days, only if still not shared | project shared | Independent value: a benchmark/case study that models the behavior | "How a 6-person team cut status meetings to zero" | See how they set it up |
| 5 | +4 days, only if still not activated | activation | Last direct ask + honest fork: help or pause | "Stuck? Here's the 10-minute fix" | Book a 10-min setup call |

Exit rules for this sequence: share event fires at any point → user leaves immediately for the engagement track. Upgrade to paid → leave immediately. Still inactive after Email 5 (extendable to 8 max) → dormant segment, hand to win-back-campaign. Total span ~11 days; every gap is a behavior check, not a fixed calendar.

## Deliverable

Produce a sequence spec containing: the activation milestone and all exit triggers, the email table (number, timing rule, skip-if condition, single job, promise-subject, one CTA), the independent payload per email, the 8-email cap with the dormant handoff, and the per-email metrics to instrument.

## Do NOT

- **Do NOT run on fixed timers when events are available** - timers send activation pitches to activated users.
- **Do NOT keep sending after conversion or activation** - leaving the sequence on conversion is the single most important rule; violating it burns the trust the sequence built.
- **Do NOT give an email two jobs or two CTAs** - one action, one link.
- **Do NOT open Email 1 with a feature tour or company history** - it has one job: first value, fastest path.
- **Do NOT write bait subjects** - opens without clicks poison the sequence's metrics and the sender's reputation.
- **Do NOT extend past 8 emails** - route non-activators to win-back-campaign instead.
- **Do NOT use this for cart recovery or dormant reactivation** - those have their own timing physics (abandoned-cart-sequence, win-back-campaign).

## Quality bar

The sequence ships only if: every email after the first has an explicit skip-if condition; the activation milestone and conversion both trigger immediate exit; every subject is a promise the body pays off; every email carries an independently useful payload; no email has more than one CTA; the sequence is 8 emails or fewer with a named dormant handoff; and per-email completion of the target action is instrumented so the weakest step is findable.
