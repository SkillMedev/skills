---
name: Notion Database Designer
description: Designs Notion databases that scale — one entity per database, deliberate property types, two-way relations with rollups, audience-specific views, and row templates — with explicit decision rules for relation vs rollup vs formula. Use when someone asks "design a Notion database for my projects", "should this be a relation or a rollup", "my Notion workspace is a junk drawer", or "set up a tasks and projects system in Notion". Do NOT use for building a full personal knowledge-management method with capture and review habits — use second-brain instead. For SQL or application database schemas, use database-schema.
---

# Notion Database Designer

A well-modeled Notion database scales; a sloppy one becomes a junk drawer where the same project name is typed forty different ways and no filter works. The expensive mistake is deferred: free-text fields and duplicated names feel fine for a month, then the workspace crosses a few hundred rows and every view, count, and cleanup becomes manual archaeology.

## Inputs to collect

1. What a single row represents, in one sentence (a task, a contact, a project). If a row could mean two different things, that is two databases — settle this first.
2. The questions people will ask of the data ("what's overdue?", "how loaded is each project?") — each question becomes a view or a rollup.
3. Who uses it and how often — each distinct audience gets its own view.
4. What already exists — related databases to link to rather than duplicate.

## Operating procedure

Order matters: entity before properties (properties describe the entity), relations before rollups (rollups aggregate across relations), structure before views (views are lenses on structure).

### Step 1: Define the entity

State in one sentence what a single row represents. If a row could mean two different things — a task sometimes, a meeting note other times — split into two databases now; migrating rows apart later is the most painful operation in Notion.

### Step 2: Choose property types deliberately

Map each attribute to the right type. The governing rule: **if you will ever filter, sort, or group by it, do NOT make it plain text.**

- **Select / Multi-select** — fixed small sets (priority, tags). Prefer over free text so views and filters work.
- **Status** — workflow stages (To do / In progress / Done) with grouping built in; use it instead of a Select for anything that moves through states.
- **Relation** — links to rows in another database (Task → Project). The backbone of a real system; make it two-way so both sides see the link.
- **Rollup** — aggregates across a relation (count of tasks, sum of hours, latest date).
- **Date** — with reminders for deadlines.
- **Person** — owners and assignees; this is what powers "my tasks" views.
- **Formula** — computed fields (days until due, is-overdue flag).
- **Text / Number / URL / Files** — only when no structured type fits.

### Step 3: Model relations, not duplication

Never type "Project Apollo" into every task row. Create a Projects database and relate tasks to it: rename the project once and it updates everywhere, rollups give project-level counts for free, and navigation works in both directions.

Decision rules for the three lookalikes:

- **Relation vs. copied text**: if two databases mention the same real-world thing, relate them. The test — would renaming it in one place need to propagate? Then it must be a relation.
- **Rollup vs. formula**: a rollup answers a question about *related rows* ("how many open tasks does this project have?"); a formula answers a question about *this row's own properties* ("is this task overdue?"). A formula cannot reach across a relation without a rollup feeding it, so if the input lives in another database, rollup first, formula on top if math is needed.
- **Relation vs. select**: if the options carry their own data (a client has an email, a tier, notes), it is an entity — use a relation to a database. If options are bare labels that will never hold data (priority: High/Med/Low), a select is correct and lighter. Promoting a select to a database later means re-linking every row by hand, so decide by asking whether the option will ever need properties of its own.

Common relation patterns: Tasks → Projects → Goals (cascading), Notes → People / Companies (CRM-style), Tasks ↔ Sprints.

### Step 4: Build views for each audience

One database, many views — one view per question someone actually asks:

- **Board** grouped by Status — daily work.
- **Calendar** by due date — deadlines.
- **Table** filtered to "Assigned to me" + "Not done" — the personal queue.
- **Gallery** — when visuals matter (assets, contacts).

Each view = filter + sort + group + visible properties tuned to exactly one job. A view answering two questions answers neither well.

### Step 5: Templates and defaults

Add database templates so new rows start pre-filled (standard checklist, default properties), and set default values on Status and Select properties. Every field an author must fill manually is a field that will eventually be left blank.

## Worked artifact: a three-database schema

Client work tracker — Clients, Projects, Tasks:

```
CLIENTS
  Name (title) | Contact email (email) | Tier (select: A/B/C)
  Projects (relation → PROJECTS, two-way)
  Active projects (rollup: Projects → Status, count per group)

PROJECTS
  Name (title) | Status (status: Planned/Active/Done)
  Client (relation → CLIENTS, two-way)
  Tasks (relation → TASKS, two-way)
  Open tasks (rollup: Tasks → Status, count where not Done)
  Latest due date (rollup: Tasks → Due, latest)
  Due (date) | Owner (person)

TASKS
  Name (title) | Status (status) | Due (date) | Assignee (person)
  Project (relation → PROJECTS, two-way)
  Overdue? (formula: Due < now() and Status != Done)

Views: TASKS board by Status ("Daily work") · TASKS table Assignee=me +
Status!=Done ("My queue") · PROJECTS table sorted by Latest due date
("Project health") · TASKS calendar by Due ("Deadlines")
```

Why it is shaped this way: Tier is a select because A/B/C are bare labels; Client is a relation because clients carry email and tier; "Open tasks" is a rollup because it counts rows in another database; "Overdue?" is a formula because it reads only the task's own Due and Status.

## Deliverable

Produce a schema sheet: each database with its one-sentence entity definition, every property with its type and a one-line reason, the relation map with rollups annotated, the view list (name, filter, sort, group, audience), and the row template contents for the common case.

## Quality bar

- [ ] Every workflow field is a Status or Select, never text.
- [ ] Cross-database links use two-way Relations, never copied names.
- [ ] Rollups surface the aggregates someone actually reviews — no vanity counts.
- [ ] At least one view per distinct audience or question.
- [ ] A row template exists for the common case, with defaults set.
- [ ] Each database's entity fits in one sentence with no "or" in it.

## Do NOT

- Do not build one giant database for everything — a row that can mean two things breaks every filter; split by entity type.
- Do not allow free-text statuses — "done", "Done ", and "DONE" are three values to a filter, and the views silently rot.
- Do not chain deep formulas nobody understands — keep formulas simple and add a description; an opaque formula becomes untouchable and then wrong.
- Do not add a rollup for every possible aggregate — each one costs load time and attention; roll up only what someone reviews.
- Do not model bare labels as databases — a Priority database with three childless rows is ceremony, not structure.

## Related skills

For a full personal knowledge-management practice — capture habits, PARA-style organization, review cadence — use second-brain; this skill designs the databases such a system might sit on. Application and SQL schema design belongs to database-schema. If the database backs a team documentation library, pair with document-template-system for the row templates' content.
