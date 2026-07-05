---
name: Push Notification Copy
description: Writes push and in-app notification copy that survives lock-screen truncation, ties every send to a user-specific trigger, deep-links to the exact destination, and respects frequency caps. Use when someone asks "write a push notification for this event", "why is our push opt-out rate rising", "draft re-engagement push copy", or is planning lifecycle, transactional, or re-engagement notifications. Do NOT use for implementing the delivery pipeline, tokens, or notification infrastructure — use push-notification-wirer instead; for deciding which lifecycle stage should send push at all, start with lifecycle-journey-map.
---

# Push Notification Copy

Push notifications live one tap away from being blocked permanently. Every send either builds or spends trust, the bar for sending is higher than email, and the margin for error is narrower — one misleading or mistimed push can cost the channel for that user forever. This skill prevents the two fatal patterns: copy that truncates into nonsense on the lock screen, and sends that exist because a calendar said so rather than because something happened.

## Inputs to collect

1. **The triggering event** — what just happened to this specific user (price drop, milestone, message received, task due). If the requester cannot name a user-specific trigger, stop: the send should not exist. Scheduled blasts untied to user events are spam regardless of how well-written they are.
2. **The deep-link destination** — the exact screen the tap opens. A push that dumps the user on the home screen breaks the promise the copy made.
3. **The variable data available** — names, amounts, deadlines, counts. Specifics are what make the body line earn its place.
4. **The user's recent notification history** — what they received in the last 24 hours and 7 days, to check against caps.
5. **Platform(s)** — iOS, Android, browser — for truncation behavior.

## Operating procedure

1. **Verify the trigger.** Confirm the send is the direct result of a user-specific event. No event, no send.
2. **Write the body first, then distill the title from it.** Writing the title last ensures it is the essence of the message, not a generic label. The body adds the detail the title omits — the specific amount, the person's name, the deadline — never a rephrase of the title. Two lines saying the same thing read as padding.
3. **Fit the limits.** Title: target **~40 characters** so it survives every surface; **60 characters** is the hard ceiling before most mobile lock screens truncate. Body: target **~120 characters**; assume anything beyond may be cut on smaller screens or watch faces. The title must stand alone and communicate the value without the body.
4. **Attach the deep link.** Every push deep-links to the exact content it references. Specify the destination alongside the copy so whoever wires it (push-notification-wirer) cannot guess.
5. **Check frequency caps.** Never more than **one push per day per user** — a ceiling, not a target; most users should receive fewer. Caps belong in the infrastructure layer so individual campaigns cannot exceed them by accident; if the system lacks caps, flag that before shipping copy into it.
6. **Set the permission-request timing (for new flows).** Request push permission immediately after the user experiences the product's core value, never on first launch. A user who just completed their first meaningful action has a reason to say yes; a user staring at an empty onboarding screen does not. Correct timing roughly doubles opt-in rates.
7. **Define the measurement pair.** Every campaign tracks opt-out rate alongside click rate. High clicks with rising opt-outs means the copy is misleading or the frequency is too high — treat it as a failing campaign even if CTR looks healthy.

## Good/bad pairs

**Re-engagement (task app):**
- Bad: `Title: We miss you! 😢` / `Body: Come back to TaskFlow and see what's new in the app today!` — no trigger, no specific, no reason to tap; deep-links nowhere useful.
- Good: `Title: 3 tasks due tomorrow` / `Body: "Ship v2 landing page" and 2 others are due Friday. Review your list.` — event-driven, title stands alone at 20 chars, body adds the named specific, deep-links to the task list.

**Transactional (marketplace):**
- Bad: `Title: You have a new notification` / `Body: Someone did something on your listing. Open the app to find out more!` — the title is a label, the body withholds the one fact the user wants.
- Good: `Title: Your camera lens just sold — $340` / `Body: Maria bought your Canon 50mm. Print the shipping label by Thursday.` — value in the title (38 chars), name + deadline in the body, deep-links to the order.

**Price alert (travel):**
- Bad: `Title: Great deals on flights this week!` / `Body: Prices are dropping on routes you might like. Don't miss out!` — a scheduled blast wearing a trigger costume.
- Good: `Title: SFO→Tokyo dropped to $612` / `Body: Down $184 from your saved search. Fares at this level usually last 1–2 days.` — the user's own saved search fired it; both lines carry numbers.

## Deliverable

Produce, for each notification: the title (≤60 chars, ~40 target), the body (~120 chars), the triggering event, the deep-link destination, the frequency-cap check, and the two metrics to watch (click rate and opt-out rate).

## Do NOT

- **Do NOT send on a schedule.** Every push is the direct result of something that happened to that user; blasts are spam however polished.
- **Do NOT write the title first** — it becomes a label instead of the distilled message.
- **Do NOT repeat the title in the body** — the second line earns its place with new detail or not at all.
- **Do NOT exceed one push per day per user**, and do not treat one per day as a goal.
- **Do NOT ship a push without a deep link** to the exact referenced content.
- **Do NOT judge a campaign on click rate alone** — a high-CTR, rising-opt-out campaign is burning the channel.
- **Do NOT ask for push permission on first launch** — wait until after the first value moment.

## Quality bar

Copy ships only if: the title stands alone under 60 characters and communicates value without the body; the body adds a specific (name, number, or deadline) the title omits; a named user event triggered it; the deep link lands on the referenced content; the send passes the one-per-day cap; and opt-out tracking is in place beside click tracking.
