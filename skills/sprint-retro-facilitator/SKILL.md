---
name: Sprint Retro Facilitator
description: Structures and facilitates sprint retrospectives with a timeboxed agenda, format selection, anti-blame ground rules, and owned action items that actually close. Use when someone asks "help me run our retro", "our retros produce the same actions every sprint", "what format should this retrospective use", or "how do I get the team to say what they're really thinking". Do NOT use for incident postmortems with a timeline and root-cause analysis — use postmortem-writer instead. For diagnosing chronic team dysfunction beyond one sprint, use team-health-check; for planning the next sprint's scope, use sprint-planning.
---

# Sprint Retro Facilitator

Most retros produce the same three actions every sprint and close none of them, which teaches the team that speaking up changes nothing — the most expensive lesson a team can learn. This skill forces specificity, surfaces the issues people are actually thinking, and ends with someone's name on every action.

## Inputs to collect

Before the session, gather:

1. Previous retro's action items and their real status (done / in progress / dropped). Non-negotiable — see Step 1.
2. Sprint facts: what shipped, what slipped, any incidents. Facts anchor discussion away from vibes.
3. Team mood signal, if you have one (a one-question pulse, or your own read). If the sprint was rough or trust is low, this changes the format choice below. Label your read as a guess if it is one.
4. Session length. Default 60 minutes maximum; 45 is better. Below 30, cut the discussion topics to one, not the timeboxes.

## Choose the format

Rotate formats every 3–4 sprints — any format run ten sprints straight produces autopilot answers. Pick by situation:

- **Start / Stop / Continue** — the default. Fast, action-oriented, works when the sprint was unremarkable.
- **4Ls (Liked / Learned / Lacked / Longed for)** — after sprints with heavy learning or new territory; surfaces knowledge gaps, not just process complaints.
- **Sailboat (wind / anchors / rocks / island)** — when the team is stuck in short-term firefighting and needs to reconnect actions to a goal; the "rocks" column surfaces risks nobody has said aloud.
- **Mad / Sad / Glad** — after a rough or emotionally loaded sprint (a failed launch, a departure). Gives feelings a legitimate column so they do not leak sideways into every topic.

Whatever the format, open by stating the anti-blame ground rule in your own words: everyone did the best job they could given what they knew, their skills, and the situation at the time. The retro examines the system that produced the outcome, not the people. Say it every time, not just when things went badly — the sprint it goes unsaid is the sprint someone gets blamed.

## Timeboxed agenda (60 minutes)

Run the phases in order and hold the timeboxes; a timer visible to everyone works better than the facilitator interrupting.

### 1. Review previous actions (10 min)

Open with the status of last retro's action items: done, in progress, or dropped. If more than half were dropped, say so plainly — that is itself a retro topic, and it outranks whatever else is on the board. Appoint a note-taker who is not the facilitator.

### 2. Gather data — silent brainstorm (15 min)

Individuals write items on sticky notes or a shared board without seeing each other's input first. This prevents the most vocal person from anchoring the group; it is the single highest-leverage facilitation move in the session. Prompt with three columns: "What slowed us down?", "What went well that we should protect?", and "What are we not saying out loud?" The third column is the most valuable and the most often skipped — keep it even when using a named format above.

### 3. Dot-vote and cluster (5 min)

Give each person exactly 3 votes. Cluster near-duplicate items before voting closes. The top 2–3 clusters by votes become the discussion topics — the group's actual priorities, not the facilitator's.

### 4. Discussion (20 min)

For each topic, in order:

1. State the observation as a system problem, not a person problem. "Reviews sat for two days" — not "Sam is slow at reviews."
2. Ask "what made this likely to happen?" before "what should we do?" Solutioning first locks in the shallowest diagnosis.
3. Root-cause at least one level deeper than the surface complaint. If the answer is "we need better communication," that is not a root cause — push for the structural or process condition that produced the failure ("nobody is on point for reviews after 3pm" is actionable; "communicate better" is not).

If discussion turns to a named individual, redirect to the condition that let the failure happen — this is the anti-blame rule doing its job.

### 5. Action items (10 min)

Every action needs three fields, no exceptions: **what** exactly will change, **who** owns it (one name, never "the team"), and **when** it will be done or reviewed. Cap actions at 3 per retro — more than 3 almost never close. If there are more candidates, vote for the top 3 and park the rest in a visible backlog. An action a single person can complete within one sprint beats a grand initiative every time.

## Worked contrast

**Bad action:** "Improve our deployment process." (No owner, no date, no defined change — it will appear again next retro, verbatim.)

**Good action:** "Priya adds a staging smoke-test step to the deploy pipeline by Thursday the 12th; we review whether deploy rollbacks dropped at the next retro."

## Deliverable

Produce a retro packet: the chosen format with a one-line rationale, the timeboxed agenda with clock times filled in, the three brainstorm prompts, the anti-blame opener script, and — after the session — the action list with what/who/when per item plus the status ledger of the previous retro's actions.

## Do NOT

- Do not skip reviewing previous actions — it is the only mechanism that makes retros compound instead of repeat.
- Do not open discussion before the silent brainstorm — the first speaker anchors everyone else's list.
- Do not accept "the team" as an owner — diffuse ownership is no ownership.
- Do not let a root-cause stop at "better communication" or "be more careful" — those are restatements of the failure, not causes.
- Do not exceed 3 actions to seem thorough — a closed action beats three open ones.
- Do not run a retro on a SEV or outage — that is a postmortem with a timeline and contributing factors; use postmortem-writer.

## Quality bar

- Every action item has one named owner and a date; zero actions assigned to "the team."
- At least one discussion topic came from votes, not from the facilitator or the manager.
- Each discussed topic reached a structural cause at least one level below the surface complaint.
- The anti-blame ground rule was stated aloud at the open.
- Previous retro's actions were reviewed first, with a plain-spoken dropped count.

## Escalation

If the same item appears in three consecutive retros with no progress, escalate it outside the retro — it is either blocked by something beyond the team's control or nobody actually owns it. Name both possibilities explicitly to the team and take it to whoever can unblock it. If retro after retro surfaces the same interpersonal friction, that is a team-health problem, not a process problem — move to team-health-check or conflict-resolution rather than another format rotation.
