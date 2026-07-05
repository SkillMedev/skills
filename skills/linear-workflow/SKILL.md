---
name: Linear Workflow
description: Configures a Linear team end to end — team structure, minimal workflow states, cycles, a small label taxonomy, a daily triage rotation, and priority SLAs with breach views. Use when someone asks "set up Linear for my team", "how should we structure teams and labels in Linear", "our Linear backlog is a mess", or "how do we handle inbound bugs in Linear". Do NOT use for writing the tickets themselves — acceptance criteria, story points, Definition of Ready — use jira-ticket-writer instead (its ticket anatomy applies to Linear issues too). For deciding sprint scope and capacity, use sprint-planning.
---

# Linear Workflow

Linear rewards lightweight, opinionated process; teams that port over a heavyweight Jira scheme get the worst of both worlds — ceremony without clarity. This skill sets up a team so issues flow predictably from idea to done, and inbound noise never silts up the backlog.

## Inputs to collect

1. Delivery units: which groups actually ship together? (Squads, not departments.)
2. Team size and count — decides whether sub-teams are justified yet.
3. Inbound volume: roughly how many bugs/requests arrive per week, and from where.
4. Current cadence, if any (1- or 2-week sprints, or none). If unknown, default to 2-week cycles and label it a default.
5. The 3–5 product areas people already use in conversation ("auth", "billing") — these become area labels.

## Operating procedure

Do the steps in order: states before cycles (cycles report on states), labels before triage (triage applies labels), triage before SLAs (SLAs are measured from triage decisions).

### Step 1: Team structure

- Create one **team per delivery unit** — a squad that ships together — not per function. A "Frontend team" and "Backend team" split guarantees every feature spans two boards.
- Keep team identifiers short (`ENG`, `WEB`) — they prefix every issue ID.
- Avoid sub-teams early; split only when a single team's backlog is genuinely unmanageable, which rarely happens under ~10 people.

### Step 2: Workflow states

Keep states minimal and meaningful. Five working states plus one terminal branch:

```
Backlog -> Todo -> In Progress -> In Review -> Done
                                        |
                                    Canceled
```

- **Backlog**: real but not committed.
- **Todo**: committed to the current cycle.
- **In Progress**: actively worked. Limit WIP to one or two per person — a third concurrent issue is a queue wearing a disguise.
- **In Review**: PR open, awaiting review.
- **Done / Canceled**: terminal. Canceled is a decision, not a failure; use it freely.

Every state beyond these needs a written answer to "what decision happens here?" — a state without a decision is a place issues go to die.

### Step 3: Cycles

- Enable cycles with a fixed cadence — 1 or 2 weeks. Pick one and keep it; switching cadence resets every velocity trend.
- Let unfinished issues **auto-roll** to the next cycle so nothing is silently lost.
- Use cycle velocity (completed issues/points) to forecast, never to punish. The moment velocity becomes a target, it gets gamed and stops meaning anything.
- Review the cycle graph at each cycle's end for two smells: scope creep (line rising mid-cycle) and chronic carryover (same issues rolling 3+ cycles — cancel or split them).

### Step 4: Labels

Small, structured taxonomy in label groups:

- **Type**: `bug`, `feature`, `chore`, `tech-debt` — exactly one per issue.
- **Area**: `area/auth`, `area/billing`, … — the 3–5 surfaces from your inputs.
- **Priority is a built-in field** (Urgent/High/Medium/Low) — use it; never duplicate it as labels.

Rule of thumb: if a label is not used in at least one filter or saved view, delete it. Ten labels that drive views beat forty that decorate.

### Step 5: Triage

Turn on **Triage** for all inbound (bugs, requests, integrations):

1. New issues land in Triage, unassigned.
2. A rotating triage owner (rotate weekly with on-call if you have one) reviews daily. For each issue: validate it is real and non-duplicate, apply Type + Area labels, set priority, then assign / move to Backlog or Todo / close as won't-fix.
3. Nothing sits in Triage longer than one business day. Triage is a decision point, not a holding pen.

### Step 6: SLAs

Encode response targets by priority:

- **Urgent**: acknowledged < 1 hour, In Progress the same day.
- **High**: triaged < 1 business day, scheduled into a cycle within the week.
- **Medium/Low**: triaged < 3 business days, scheduled when capacity allows.

Build saved views that surface breaches so nobody has to remember to check — e.g. "Urgent + not started".

### Step 7: Saved views

Create at minimum:

- **My active issues** — assignee = me, state In Progress / In Review.
- **Needs triage** — state = Triage (the triage owner's homepage).
- **Cycle at risk** — current cycle + priority High/Urgent + not Done.
- **SLA breach** — Urgent older than 1h unacknowledged, High untriaged past 1 day.

## Worked artifact: team setup spec

Copy, fill, and execute in Linear settings top to bottom:

```
LINEAR TEAM SETUP — [FILL: team name] ([FILL: 3-letter identifier])

States: Backlog / Todo / In Progress / In Review / Done / Canceled
WIP limit (social contract): [FILL: 1 or 2] per person in In Progress

Cycles: [FILL: 1 or 2] weeks, auto-roll unfinished ON, start day [FILL]

Label groups:
  type/     bug, feature, chore, tech-debt
  area/     [FILL: 3-5 areas, e.g. auth, billing, api]

Triage: ON. Owner rotation: [FILL: names, weekly]. Max dwell: 1 business day.

SLAs: Urgent ack <1h, in progress same day
      High triaged <1d, in cycle within week
      Med/Low triaged <3d
Views: My active issues / Needs triage / Cycle at risk / SLA breach
```

## Deliverable

Produce a filled team setup spec (above) plus the list of saved views with their exact filters — everything an admin needs to configure the team in one sitting, and the two social contracts (WIP limit, triage dwell time) written where the team will see them.

## Do NOT

- Do not add workflow states to model every nuance — each extra state is a place issues stall, because no one owns moving things out of it.
- Do not skip triage because volume is low today — the backlog fills with unvalidated noise precisely because no one notices it happening.
- Do not treat velocity as a target — it becomes gamed and meaningless within two cycles.
- Do not duplicate priority as labels — two sources of truth for urgency guarantees they disagree.
- Do not create teams per function — cross-team dependencies on every feature is the predictable result.

## Quality bar

- Five working states, and every additional state has a written decision it represents.
- Every label appears in at least one filter or saved view.
- Triage has a named rotation and nothing older than one business day sitting in it.
- The four core saved views exist and open to non-empty, correct results.
- SLA breaches are visible in a view, not dependent on memory.

## Related skills

The issues flowing through this workflow still need to be well written — user story, testable acceptance criteria, sane sizing. That anatomy lives in jira-ticket-writer and applies verbatim to Linear issues. Sprint scoping and capacity questions go to sprint-planning; if inbound is dominated by incidents rather than requests, pair triage with sev-triage.
