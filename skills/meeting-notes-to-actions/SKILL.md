---
name: Meeting Notes to Actions
description: Converts raw meeting notes or transcripts into a four-part record - a 3-5 sentence summary, the decisions actually made, an action table with owner, verb-phrased action, due date, and status, and the open questions - without inventing owners, dates, or tasks. Use when someone pastes notes and asks "pull the action items out of this", "summarize this meeting", "who owns what from this transcript", or "what did we actually decide". Do NOT use for preparing the agenda before a meeting happens - use meeting-agenda instead - or for turning extracted actions into engineering tickets - use jira-ticket-writer instead.
---

# Meeting Notes to Actions

The expensive failure with meeting notes is not messiness - it is the commitment that silently evaporates because nobody wrote down who owns it. This skill extracts, rather than reformats: decisions and commitments come out of the noise, gaps in ownership are made loudly visible instead of papered over, and nothing is invented that was not in the room.

## Operating procedure

### Step 1: gather inputs

- The raw material: notes, transcript, chat log, or bullet dump. Accept it as-is.
- The meeting date if stated or supplied - relative dates like "Friday" can only be resolved to a calendar date when the meeting date is known; otherwise keep the relative form exactly as written.
- Attendee names if available, used only to disambiguate ("Alex" when two Alexes attended); never to guess owners.

If the notes are too thin to extract from (no verbs, no names), say so and return what is extractable rather than padding.

### Step 2: extract in three passes

Pass the material three times, one lens per pass - a single pass reliably misses commitments buried inside discussion:

1. Decisions: a choice between options that was settled. "We're going with quarterly billing" is a decision. Something debated but left open is not - it goes to Open Questions.
2. Commitments: any first-person or assigned promise - "I'll handle X", "Priya will send Y", "we should do Z by Friday" - becomes an action item.
3. Unresolved threads: questions raised and dropped, disagreements without a settlement, "let's discuss offline."

Collapse duplicate mentions of the same action into one row, keeping the most specific phrasing and the latest stated date.

### Step 3: apply the action-item rules

- Owner: exactly one name per action, preserved exactly as written in the notes. If no owner was named, write UNASSIGNED - visibly, in caps - so the gap gets fixed in review rather than discovered at the deadline. Never assign the note-taker, the meeting organizer, or the most plausible person by inference.
- Action: phrase as a verb-first instruction ("Send the pricing draft to sales"), never a topic ("Pricing draft"). A topic cannot be done; a verb can.
- Due: only when explicitly stated. Resolve relative dates ("by Friday") to calendar dates only if the meeting date is known. Otherwise write TBD. Do not infer urgency into a date.
- Status: default "Not started" unless the notes say the work already began.

### Step 4: assemble the four sections, in order

1. Summary - 3 to 5 sentences: what was discussed and why it mattered. No filler, no play-by-play.
2. Decisions - one line each: the decision plus the rationale if one was stated.
3. Action items - the table: Owner · Action · Due · Status.
4. Open questions / follow-ups - each with who should resolve it, if that was stated.

### Step 5: verify against the source

Before returning, check every table row traces to a specific line in the notes. If a row cannot be pointed back to the source, delete it - an invented action is worse than a missed one, because it spends someone's time on work nobody asked for.

## Worked extraction example

Raw notes:

```
- pricing discussion went long. sarah thinks we should test $49, marc pushing $59
- decided: go with $49 for the beta cohort, revisit after 100 signups
- marc: "I'll get the updated pricing page copy to design by Thursday"
- need someone to set up the signup dashboard before launch - nobody volunteered
- also sarah mentioned the pricing page again later, marc said "yeah yeah Thursday"
- legal review of new ToS?? tabled
```

Extracted:

Decisions:
- Beta cohort priced at $49; revisit after 100 signups. (Rationale: test lower price point first.)

| Owner | Action | Due | Status |
|-------|--------|-----|--------|
| Marc | Send updated pricing page copy to design | Thursday | Not started |
| UNASSIGNED | Set up the signup dashboard before launch | TBD | Not started |

Open questions:
- Legal review of the new ToS - tabled; no owner named.

Note what the rules did: the two mentions of Marc's pricing-page task collapsed into one row; the dashboard task kept its commitment but exposed the ownership gap as UNASSIGNED rather than guessing Sarah; "Thursday" stayed relative because no meeting date was given; the $49-vs-$59 debate produced one decision line, not two actions; and the ToS item landed in open questions because "tabled" is not a decision.

## Deliverable

Produce the four-section record - summary, decisions, action table (Owner · Action · Due · Status), open questions - where every row is traceable to the source notes, every ownerless action reads UNASSIGNED, and every unstated date reads TBD.

## Do NOT

- Do not invent owners, dates, or tasks. The output's value is that it can be trusted; one fabricated row poisons the whole table.
- Do not quietly drop ownerless actions or assign them to a plausible person - UNASSIGNED in caps is the feature, not a formatting failure.
- Do not phrase actions as topics; "Q3 offsite" is not an action, "Book venue for Q3 offsite" is.
- Do not promote debated-but-unsettled items to Decisions; they belong in Open Questions.
- Do not editorialize or add advice. The role is recorder, not participant - neutral and factual, unless the user explicitly asks for recommendations.
- Do not paraphrase names or normalize spellings; preserve them exactly as written.

## Quality bar

- All four sections present, in order; the summary is 3-5 sentences.
- Every action row has all four columns filled (with UNASSIGNED / TBD where applicable) and starts with a verb.
- No duplicate rows for the same commitment.
- Every decision and action traces to a specific line in the source.
- Zero content exists in the output that did not exist, at least implicitly, in the notes.
