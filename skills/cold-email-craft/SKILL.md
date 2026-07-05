---
name: Cold Email Craft
description: Write short, personalized B2B cold emails and follow-up copy - under 90 words, one ask, personalization in the first line - with good/bad contrast pairs to calibrate. Use when someone asks "write a cold email", "why is nobody replying to my outreach", "draft a follow-up sequence", "make this email shorter", or is starting founder-led outbound. Do NOT use for sending infrastructure, domains, SPF/DKIM/DMARC, warmup, or spam placement - use cold-email-deliverability instead; for multi-channel cadence architecture use outreach-sequence-designer; for building and verifying the list use prospect-list-builder.
---

# Cold Email Craft

A cold email earns a reply by respecting the reader's time and making the next step trivial. The costly mistake this skill prevents is the 200-word "quick question" pitch that reads as mail-merge, burns the prospect, and teaches the founder that outbound doesn't work. Short beats clever; this skill owns the words. If the words are good but nothing lands in the inbox, the problem is infrastructure - route to cold-email-deliverability.

## Operating procedure

### Step 1: Gather inputs

1. Who is being emailed: role, company profile, and one concrete reason this person, now (a launch, a hire, a post, a funding event). If no reason exists, the list is not ready - route to prospect-list-builder.
2. The offer and the one result it produces, with a number from a comparable company. Default: if no customer result exists yet, use the founder's own before/after and label it as such.
3. The lowest-friction ask the sender can honor ("15-min call", "2-min Loom", "reply with yes/no").
4. Sequence length and channel mix already planned, if any (architecture belongs to outreach-sequence-designer; copy for each step belongs here).

### Step 2: Write the first line - personalization first

The first line is about them, specific and true: a launch, a hire, a post, a talk. Never "I hope this finds you well," and never open with the sender's name or company. Test: could this line be pasted into an email to anyone else? If yes, it is not personalization. A merge tag that says {{first_name}} only is not personalization either. This line is 80% of the reply-rate difference; spend the research minutes here.

### Step 3: Build the body in four moves, under 90 words total

1. Opener - the specific, true observation about them (Step 2).
2. Relevance - one sentence connecting their situation to what you do.
3. Proof - one concrete result, with a number, from a comparable company.
4. Ask - a single low-friction CTA ("Worth a 15-min call next week?" or "Want me to send a 2-min Loom?").

Hard limits: under 90 words, 1-3 sentences per move, one idea, one ask - no menus of options. Mobile-first: it must read in one thumb-scroll.

### Step 4: Write the subject line

3-5 words, specific, lowercase is fine, no clickbait, no brackets-gimmicks. It should read like an internal colleague's subject: "flaky tests at acme", "your q3 reporting post". If the subject over-promises what the body delivers, the reply never comes even when the open does.

### Step 5: Strip and check

- Cut jargon, buzzwords, "synergy", "solution", "reach out", "touch base".
- Cut every sentence about the sender's company history.
- No links and no images in the first touch beyond at most one (also a placement issue - see cold-email-deliverability).
- Read it aloud; anything that sounds like a campaign rather than a person gets rewritten.

### Step 6: Draft the follow-up sequence

3-4 follow-ups, spaced 3-5 days apart, each adding a new angle or proof point - never "just bumping this to the top of your inbox."

- Follow-up 1 (day 3-4): a second proof point or a relevant resource, half the length of the original.
- Follow-up 2 (day 7-9): a different angle on the pain (cost of inaction, a peer's move).
- Follow-up 3 (day 12-15): the short breakup - one or two lines, permission to say no: "If this isn't a priority, a quick 'no' saves us both the follow-ups." Making no easy builds the relationship for later, and breakup emails routinely pull the highest reply rate in the sequence.

## Worked pair

Bad (114 words, no personalization, three asks):

```
Subject: Revolutionize Your Data Infrastructure!

Hi {{first_name}},

I hope this email finds you well! My name is Jordan and I'm the founder of
DataFlowPro, a cutting-edge platform that leverages AI to streamline data
pipelines for modern enterprises. We've built a revolutionary solution that
helps companies like yours unlock synergies across their data stack.

I'd love to schedule a 30-minute demo, or I can send over our whitepaper,
or feel free to check out our website. We also offer a free consultation!

Looking forward to hearing from you soon.
```

Good (61 words, personalized, one ask):

```
Subject: your pipeline post

Hi Maya - your post on the 3am pipeline pages at Northbeam hit home.

We built Dataflow after living the same on-call rotation. Loop, a team
about your size, cut pipeline incidents 70% in their first month.

Worth a 15-min call next week to see if it maps to your setup? Happy
to send a 2-min Loom instead.
```

The difference to copy: the first line proves a human did research, the proof has a number and a comparable company, and there is exactly one primary ask with a lower-friction fallback.

## Deliverable

Produce the first-touch email (subject + body, under 90 words, word count shown) plus a 3-4 step follow-up sequence with day spacing and the new angle of each step labeled - ready to paste into the sending tool.

## Do NOT

- Do not exceed 90 words in the first touch; length is the most reliable predictor of deletion.
- Do not open with the sender's name, company, or "I hope this finds you well" - the first line must be about the reader.
- Do not stack asks; a menu of options converts worse than any single option.
- Do not send "just bumping this" follow-ups; each touch must earn its place with new value.
- Do not fake personalization with merge tags alone; detected automation converts to spam complaints, which burns the domain (see cold-email-deliverability).
- Do not use clickbait subjects; an opened email that disappoints trains the prospect to ignore the sequence.

## Quality bar

- First touch is under 90 words and reads in one thumb-scroll.
- The first line could not be sent to anyone else.
- Exactly one CTA, answerable in under a minute.
- Proof contains a number and a comparable company, or is honestly labeled founder-result.
- Every follow-up adds a new angle; the sequence ends with an easy-no breakup.

## Escalation

Route domain setup, warmup, volume caps, and spam diagnosis to cold-email-deliverability; multi-channel cadence design to outreach-sequence-designer; list building and verification to prospect-list-builder; and post-reply sales conversation prep to discovery-call-prep.
