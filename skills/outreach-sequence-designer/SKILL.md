---
name: outreach-sequence-designer
description: Use when you need to design the cadence/sequence structure that moves a cold prospect to a booked meeting across multiple channels — the orchestration of touches, not the copy of any one message. Trigger phrases "design an outreach sequence", "build a cold cadence", "how many touches should my sequence have", "multi-channel sequence", "what's a good follow-up cadence", "set up day-by-day steps", "how long should my sequence be", "add LinkedIn and calls to my email sequence", "design a breakup email sequence", "what's the spacing between touches". Workflow chooses channels and mix, sets length and step spacing, defines bump/follow-up logic, matches personalization tier to account tier, wires the "why now" signal hook, varies the message angle across steps, and sets exit/branch criteria. Do NOT use for writing the words of a single email — use [[cold-email-craft]]. Do NOT use for who to put in the sequence — use [[prospect-list-builder]]. Do NOT use for building the sequence inside a tool — use [[apollo-prospecting]].
---

# Design a Multi-Channel Outreach Sequence

A sequence is a system, not a stack of emails. The unit of design is the *cadence*: which channels fire, in what order, how far apart, with what angle, and when a prospect exits. Most reps obsess over the words of touch #1 and ship a 3-email "sequence" that is really one email sent three times. That loses on both axes — single-channel, and no angle variation — so it gets ignored after the first send.

The core insight: replies are a function of *coverage over time*, not cleverness in a single message. The majority of positive replies land after the first touch, and a meaningful share come from channels other than email. Your job here is to design the orchestration so that a prospect who ignores your Day 1 email still gets a relevant, differently-angled reason to respond on Day 4 and Day 9 — without you babysitting it. The words live in [[cold-email-craft]]; the *structure* lives here.

## When to use this skill

- You are standing up a new outbound motion and need a defensible default cadence.
- Your current sequence is email-only and reply rates have flatlined.
- You are getting opens but no replies, and suspect the follow-up logic (not the copy) is the problem.
- You need to differentiate effort by account tier without building three separate machines.
- You have a fresh batch of [[buying-signal-tracker]] signals and need a "why now" cadence to act on them.

Use [[cold-email-craft]] for the actual message wording, [[apollo-prospecting]] for building this inside a tool, and [[prospecting-metrics]] for reading whether it works.

## The workflow

1. **Set the goal and the exit.** Define the single conversion event (usually "meeting booked" or "positive reply"). Then define every way a prospect *leaves* the sequence: replied, meeting booked, opted out, hard bounce → pause immediately; soft bounce → retry once then pause. Branches first — they protect your domain and your reputation.

2. **Pick the channel mix.** Email-only is a one-legged stool. Default mix: email (the workhorse), LinkedIn (connect → view → message → optional voice note), phone + voicemail (highest intent, lowest scale), and optional video (Tier 1 only). Multi-channel beats email-only because each channel reaches a different attention surface and signals legitimacy when they reinforce each other.

3. **Choose length and spacing.** Default to ~7-9 touches over ~14-18 business days. Tighter early (Day 1, 2, 4 — strike while the signal is warm), wider later (Day 9, 14, 18 — persistent but not annoying). Never two touches the same day on the same channel. Spacing is a design variable, not an afterthought.

4. **Match personalization tier to account tier.** Pull tiers from [[prospect-list-builder]]. Tier 1 (named/strategic): fully manual, research-led, custom first line + custom "why now" + video — low volume, high touch. Tier 2 (ICP-fit): templated with 2-3 researched variables (signal, role, peer logo). Tier 3 (volume): scaled, signal-token + role-token, no manual research. One sequence skeleton, three personalization depths.

5. **Wire the "why now" hook.** The opener's job is relevance, not introduction. Drive it from a [[buying-signal-tracker]] signal — new hire in the buying role, funding, job posting, tech-stack change, competitor mention. No signal? The cadence still runs, but the hook degrades to a thesis/pain hook, and you should expect lower reply rates.

6. **Vary the angle across steps.** Each touch is a *different reason to care*, never "just bumping this." Default angle arc: (1) why-now/pain, (2) social proof/peer logo, (3) different stakeholder value or a resource, (4) a sharp one-line question, (5) the breakup ("assuming this isn't a priority — should I close the loop?"). The breakup is consistently one of the highest-reply touches; never skip it.

7. **Respect deliverability and volume.** Honor warmup and per-inbox send caps from [[cold-email-deliverability]] — new domains/inboxes ramp slowly; spread volume across inboxes; keep daily sends under the cap. Spammy cadence kills the domain that the whole motion depends on.

8. **A/B test one variable at a time.** Change the subject line, OR the Day 1 angle, OR the send time — never two at once, or you can't attribute the lift. Run to a meaningful sample, read it with [[prospecting-metrics]] (reply rate and positive-reply rate, not opens), keep the winner, test the next variable.

## Sequence template

```
GOAL: meeting booked   EXIT: reply | booked | opt-out | hard bounce → pause
TIER: [1 manual | 2 templated | 3 scaled]   SIGNAL: [why-now hook source]

Day 1   Email     Angle: why-now / pain      Personalize: {{signal}} opener
Day 2   LinkedIn  Connect request (no pitch) + profile view
Day 4   Email     Angle: social proof        Reply on Day-1 thread (bump)
Day 6   Call      + voicemail if no answer   Reference the email, not the pitch
Day 9   LinkedIn  Message (or voice note T1) Angle: different stakeholder value
Day 11  Email     Angle: resource / insight  New thread, new subject
Day 14  Call      + voicemail
Day 18  Email     Angle: breakup             "Closing the loop — bad timing?"

BRANCHES: reply→stop+route to rep | booked→stop | opt-out→suppress |
          hard bounce→pause+clean | soft bounce→retry once→pause
```

## Worked example

**Persona:** VP Engineering at a 200-500 person Series C SaaS company (from [[icp-persona-builder]]).
**Signal:** Company posted 4 backend roles in 30 days and just announced a Series C — scaling pain is live (from [[buying-signal-tracker]]). **Tier:** 2 (strong ICP fit, templated-with-variables).

- **Day 1 — Email (why-now):** "Saw you're hiring 4 backend engineers post-raise — usually means on-call is getting painful faster than headcount can fix it." One-line thesis + soft ask. Copy crafted via [[cold-email-craft]].
- **Day 2 — LinkedIn:** Connect request, no pitch. View profile. Pure familiarity.
- **Day 4 — Email (social proof):** Reply on the Day-1 thread: "Two other Series C eng teams ([peer logo], [peer logo]) cut on-call pages 40% with us during the same scaling crunch."
- **Day 6 — Call + voicemail:** "Left you a note about the on-call load post-raise — calling to see if it's worth 15 minutes."
- **Day 9 — LinkedIn message:** Different stakeholder value angle — "Your eng leads usually feel this before the VP does; happy to share what we saw at [peer]."
- **Day 11 — Email (resource):** New thread/subject. Share a one-pager or teardown, no ask beyond "useful?"
- **Day 14 — Call + voicemail.**
- **Day 18 — Email (breakup):** "Assuming scaling on-call isn't a Q-this priority — want me to close the loop, or circle back next quarter?"

A reply at any step stops the sequence and routes to the rep. A hard bounce on Day 1 pauses everything and flags the contact for list cleaning before the domain takes damage. Read the run in [[prospecting-metrics]]: if Day 18 breakup is your top-replying step, your earlier angles are too weak — fix the front of the cadence, don't add more steps to the back.

## Deliverable

Produce a sequence spec ready to build in a tool ([[apollo-prospecting]]): the filled template above — goal, exit/branch rules, tier, signal hook, and the day-by-day step table with channel, angle, and personalization depth per touch — plus a one-line copy brief per step for [[cold-email-craft]] (the angle and the ask, not the words), and the single A/B variable the first run will test with its pre-committed sample size.

## Quality bar

- At least two channels beyond email appear in the cadence, or the omission is a deliberate, stated choice.
- Every touch has a distinct angle — no step whose brief reduces to "bump."
- Exit and branch rules cover reply, booked, opt-out, hard bounce, and soft bounce explicitly.
- The cadence's total daily volume fits inside the [[cold-email-deliverability]] caps for the sending infrastructure it will run on.
- The sequence ends on a breakup touch.

## Common failure modes

- **Email-only "sequences."** Three emails is not multi-channel. Without LinkedIn and a call you forfeit the prospects who never open email.
- **Same message three times.** "Just bumping this" is not an angle. Every touch must give a *new* reason to care, or it trains the prospect to ignore you.
- **No breakup.** You quit one touch before the highest-reply step. Always end on a clean breakup.
- **Skipping the exit logic.** Continuing to email a hard bounce or someone who replied is how you torch deliverability and look like a bot. Branches are not optional.
- **Cadence too aggressive for the domain.** A clever sequence on an unwarmed inbox lands in spam. Volume and warmup constraints from [[cold-email-deliverability]] gate everything.
- **Testing five things at once.** If you change subject, angle, and timing together, a lift tells you nothing. One variable, read with [[prospecting-metrics]].
- **Same effort for every account.** Manually researching a Tier 3 volume account wastes your best hours; sending a Tier 1 strategic account a token-merge email wastes the account. Match the tier.
- **Front-loading all touches in week one.** Cramming 7 touches into 5 days reads as desperate and burns the contact. Spacing is part of the design.
