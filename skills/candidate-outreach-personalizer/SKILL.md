---
name: Candidate Outreach Personalizer
description: Drafts short, honest recruiting outreach and InMail messages to a passive candidate, anchored on one researched, candidate-published professional fact, for a human recruiter to review and send. Use when someone asks "write an InMail to this engineer", "personalize this sourcing message", "draft outreach from this profile", or is contacting a passive candidate about a specific role. Do NOT use for sales or founder cold email to prospects — use cold-email-craft. Do NOT use for designing the multi-touch cadence, channel mix, and follow-up timing around the message — use outreach-sequence-designer. Do NOT use for writing the job description itself — use job-description-writer.
---

# Candidate Outreach Personalizer

Draft a short, honest, easy-to-decline recruiting message that earns a reply because it is unmistakably about this candidate and this role. The costly mistake this prevents is the template blast: passive candidates receive a steady stream of "your background is impressive" messages, and one more of those doesn't just get ignored — it burns the company's name with exactly the people it wants to hire. One researched fact beats three generic compliments every time.

## Operating procedure

### Step 1: gather inputs

- The candidate's public professional profile (URL or pasted summary).
- The role: title, level, scope, team, location/remote policy, and the pay range where disclosure is required.
- The sender: who the message is from (recruiter, hiring manager, founder) and their real connection to the role.
- Any true, specific reason this candidate surfaced (a project, a talk, a repo — not "the algorithm").

If the role's level or pay range is unknown, ask; guessing either produces a message that misleads.

### Step 2: extract only candidate-published, job-relevant facts

Read the profile and pull role history, projects, talks, published writing, open-source work, and tech stack choices. Discard anything personal, unconfirmed, or found outside a professional context. If a fact's provenance is uncertain, treat it as unusable.

### Step 3: pick exactly one concrete hook

Choose one professionally relevant anchor: a specific project, talk, article, or stack decision. The depth rule: **minimum one researched fact, maximum one hook**. The hook must pass the swap test — if the sentence would still make sense with another candidate's name in it, it is not a hook, it is flattery. "Your impressive background in engineering" fails; "your PyCon talk on taming flaky integration tests" passes. No fake flattery: every complimentary word must be tied to a verifiable artifact the candidate published.

### Step 4: write the opener

In two sentences, connect their demonstrated experience to the actual work of this role — not to the company's greatness. State the level and scope honestly; do not inflate the title, the team, or the opportunity. A candidate who joins on an inflated pitch leaves within the year.

### Step 5: identify yourself and the ask

State who is writing, the company, and that this is a recruiting message — no pretexts, no "quick question" bait. Include real specifics on location/remote and the pay range where required by law or company policy. Close with one low-commitment ask ("open to a 15-minute chat this week or next?") and an explicit, costless way to decline or stay in touch ("if the timing's wrong, a one-line 'not now' is totally fine").

### Step 6: output for human review

Deliver the draft for a human recruiter to review and send — never auto-send. Flag any detail that could not be confirmed as candidate-published, and anything sensitive or non-professional that was deliberately excluded.

## Worked contrast pair

Role: Senior Backend Engineer, payments team, remote-first, posted range $170-200k. Candidate: staff-level engineer who wrote a blog post on idempotency keys in payment retries.

**Bad:**

```
Hi Jordan! I came across your impressive profile and was blown away by your
amazing background! We're a fast-growing rocket-ship startup looking for
rockstar engineers to join our world-class team. This is a once-in-a-lifetime
opportunity and I'd hate for you to miss out — do you have time for a call
tomorrow or Thursday? Looking forward to hearing from you ASAP!
```

Why it fails: zero researched facts (swap test fails — any name fits), stacked flattery with no anchor, inflated claims, manufactured urgency, a high-pressure ask with no way to decline, and it never states level, scope, location, or pay.

**Good:**

```
Hi Jordan — your post on idempotency keys in payment retries covered exactly
the failure mode our payments team is redesigning around this quarter.

I'm Sam, a recruiter at Acme. We're hiring a Senior Backend Engineer for that
team — remote-first, $170-200k. The work is retry semantics and ledger
consistency, so your writing suggests real overlap.

Open to a 15-minute chat in the next week or two? And if the timing's wrong,
a one-line "not now" is completely fine — happy to stay in touch either way.
```

Why it works: one specific hook that passes the swap test, honest role/level/pay specifics, a clearly identified sender, one low-commitment ask, and a costless decline path. 88 words.

## Deliverable

Produce a ready-to-review draft under 120 words containing: one researched hook, a two-sentence experience-to-role connection, sender identity and honest role specifics (level, location/remote, pay range where required), one low-commitment ask, and an explicit decline path — plus a flags list of anything unconfirmed or excluded.

## Do NOT

- Do not reference personal social media, photos, family, health, or anything outside professional work — it reads as surveillance, not research.
- Do not infer or act on protected characteristics (age, ethnicity, gender, disability, religion) to decide whom to contact or what to write.
- Do not use guilt, urgency, false scarcity, or fake mutual connections; a reply earned by pressure is a candidate lost at offer stage.
- Do not include any detail that cannot be confirmed as published by the candidate in a professional context.
- Do not stack compliments in place of research — fake flattery fails the swap test and signals a template.
- Do not auto-send; this drafts only, for a human recruiter to review.

## Quality bar

- Under 120 words, one hook, one ask.
- The hook passes the swap test: the message could not be sent unchanged to anyone else.
- The candidate could decline in one line at no social cost.
- No claim about the company, role, or a referral that is not literally true.
- Level, scope, location/remote, and pay range (where required) are stated plainly.

## Escalation

Pay-transparency disclosure rules and anti-discrimination law vary by jurisdiction; this skill drafts messages, it does not give legal advice — route compliance questions on required disclosures or protected-class handling to counsel or HR. For the touch sequence around this message, use outreach-sequence-designer; for evaluating candidates once they reply, use interview-guide-builder.
