---
name: press-release-writer
description: Writes announcement press releases in proper wire format - inverted pyramid structure, a headline under 100 characters carrying the news verb, dateline conventions, quote construction rules (executive quote states vision, customer quote states outcome), and a boilerplate block. Use when a user says "write a press release for our launch", "we're announcing a funding round, draft the release", "turn this announcement into a press release", or "is this press release newsworthy enough". Do NOT use for orchestrating a Product Hunt launch - use product-hunt-launch instead - for running the launch day itself - use launch-day-runbook instead - or for tracking coverage after the release goes out - use media-monitor instead.
---

# Press Release Writer

A press release has two readers - a journalist deciding in ten seconds whether there is a story, and a customer or investor who finds it via search - and both are served by the same discipline: the news first, in plain declarative sentences, with zero marketing adjectives. The costly mistake this skill prevents is burying the news under company throat-clearing ("XYZ Corp, the leading provider of innovative solutions, today announced...") - journalists delete those unread, and the announcement earns no coverage despite real news underneath.

## Operating procedure

Steps run in order; the news kernel comes first because every other element - headline, lede, quotes - is derived from it, and writing prose before isolating the kernel is how the news gets buried.

### Step 1: Isolate the news kernel

Ask: what changed, and why would someone outside the company care? Reduce the announcement to one sentence containing a concrete change - launched, raised, acquired, partnered, hired, published, reached. If the sentence contains no verb of change ("XYZ continues its commitment to..."), there is no news and no release; tell the user and stop. Newsworthiness signals, strongest first: money (funding, revenue milestone), firsts (first to do X in market Y), scale (a number big for the category), names (known customer, partner, or hire), conflict or contrast (displacing an incumbent way of doing things).

### Step 2: Write the headline - under 100 characters, news verb included

The headline is the news kernel compressed: **Company + news verb + the concrete thing + the number or differentiator if it fits**. Under 100 characters (media databases and email subject lines truncate longer). Active voice, present tense per wire convention ("Raises," not "Has Raised"). No exclamation marks, no ALL CAPS words, no marketing adjectives ("revolutionary," "cutting-edge" - journalists read these as inverse quality signals). An optional subheadline (one line, under 120 characters) carries the second-most-important fact.

### Step 3: Structure by inverted pyramid

Order paragraphs by decreasing newsworthiness so the release survives being cut from the bottom - which is exactly what editors do:

1. **Lede (paragraph 1):** who, what, when, why-it-matters in 25-40 words. A journalist reading only this paragraph must be able to write a one-line story.
2. **Paragraph 2:** the most important supporting facts - the numbers, the names, the mechanics of what changed.
3. **Paragraph 3:** executive quote (see Step 5).
4. **Paragraph 4:** context - market problem, what this replaces, how it works at one level deeper.
5. **Paragraph 5:** customer or partner quote (see Step 5).
6. **Paragraph 6:** availability, pricing, where to learn more.
7. **Boilerplate + media contact** (see Step 6).

Total length 300-500 words. Over 600 words means the pyramid is broken - something below belongs above, or belongs nowhere.

### Step 4: Apply dateline and wire conventions

- Dateline opens the lede: `CITY, State/Country - Month Day, Year - ` with the city in caps (e.g., `AUSTIN, Texas - March 4, 2026 - `). The city is where the company is headquartered or where the news happened.
- "FOR IMMEDIATE RELEASE" at top left (or the embargo line: "EMBARGOED UNTIL [date, time, timezone]").
- Third person throughout the body; no "we" or "our" outside quotes.
- End marker `###` centered after the boilerplate.
- Company name in full on first mention, consistent short form after.

### Step 5: Construct the quotes - two, each with one job

Quotes are the only place opinion is allowed, so they must not waste it restating facts the body already carries.

- **Executive quote = vision.** Why this matters, where it leads, the belief behind it. Test: if the quote would still be true with the product name swapped out, rewrite it. One or two sentences; readable aloud without embarrassment.
- **Customer/partner quote = outcome.** What concretely changed for them, with a number wherever one exists ("cut our onboarding time from two weeks to two days"). A customer quote about feelings ("we're excited to partner") is a wasted slot - push for the outcome or cut the quote.

Never fabricate a quote and never publish one without the named person's explicit approval of the exact wording.

### Step 6: Write the boilerplate and contact block

The boilerplate is the reusable "About [Company]" paragraph: 40-80 words, factual, one sentence on what the company does and for whom, one on scale or traction (funding, customers, footprint - only real numbers), one with the website. Written once, reused verbatim on every release until the facts change. Below it: media contact name, title, email - a real monitored inbox, because journalists on deadline move on if the first email bounces.

### Step 7: Verify and package

Run the quality bar, confirm every fact and number against a source, confirm quote approvals, and deliver the release plus a suggested distribution note (embargo pitch list vs. wire vs. owned channels - full distribution strategy is out of scope; see neighbors).

## Inputs to collect

- The announcement facts: what, when, the numbers, the names (required).
- Company HQ city and existing boilerplate if one exists (default: draft a new boilerplate and label it for approval).
- Quote sources: which executive, which customer - and whether quotes exist or must be drafted for approval (default: draft candidate quotes clearly marked DRAFT - NEEDS APPROVAL).
- Target publish date and any embargo.
- Media contact person and email.

Label every drafted quote and every inferred fact as needing verification.

## Concrete thresholds

- Headline under 100 characters, containing a news verb of change.
- Lede 25-40 words; answers who/what/when/why-it-matters standalone.
- Total length 300-500 words; hard ceiling 600.
- Exactly 2 quotes: one executive (vision), one customer/partner (outcome); customer quote carries a number where one exists.
- Boilerplate 40-80 words.
- Zero marketing adjectives in headline and lede - the test: would a journalist keep the word in their own story?

## Worked contrast - headline

**Bad (147 chars, no news verb, adjective soup):**

```
XYZ Corp, the Leading Provider of Innovative AI-Powered Solutions,
Announces the Revolutionary Next Generation of Its Groundbreaking Platform!
```

Why it fails - "announces" carries no news (announces *what*?), every adjective is a claim the journalist must delete, it exceeds 100 characters, and the actual change is nowhere in it.

**Good (78 chars, verb + number + differentiator):**

```
Datachord Raises $18M Series A to Automate Insurance Claims Review
```

Why it works - the news verb ("Raises") and the number are in the first five words, the category is named, and a journalist could run it nearly verbatim.

## Template - full release skeleton

```
FOR IMMEDIATE RELEASE

[FILL: Headline - <100 chars, news verb, no adjectives]
[FILL: Optional subheadline - <120 chars, second-most-important fact]

[FILL: CITY], [FILL: State] - [FILL: Month Day, Year] - [FILL: Lede - who,
what, when, why it matters, 25-40 words.]

[FILL: Paragraph 2 - key supporting facts: numbers, names, mechanics.]

"[FILL: Executive quote - vision: why this matters and where it leads]," said
[FILL: Name], [FILL: Title] of [FILL: Company].

[FILL: Paragraph 4 - context: the problem, what this replaces, how it works.]

"[FILL: Customer quote - concrete outcome with a number]," said [FILL: Name],
[FILL: Title] at [FILL: Customer company].

[FILL: Availability, pricing, link.]

About [FILL: Company]
[FILL: Boilerplate - 40-80 words: what it does and for whom, one real
traction number, website.]

Media Contact
[FILL: Name], [FILL: Title]
[FILL: monitored email]

###
```

## Deliverable

A wire-format press release ready for distribution: headline and subheadline, datelined inverted-pyramid body of 300-500 words, two purpose-built quotes flagged for approval where drafted, boilerplate, contact block, and end marker - plus a list of every fact and quote still awaiting verification or sign-off.

## Do NOT

- **Do NOT open with company self-description.** The news leads; the company description is paragraph seven (the boilerplate). "Leading provider of..." in a lede is the fastest route to the journalist's trash.
- **Do NOT use marketing adjectives.** "Revolutionary," "innovative," "cutting-edge," and "excited" transfer zero information and signal a release written for the CEO, not the reader.
- **Do NOT write interchangeable quotes.** If the executive quote survives a product-name swap, it says nothing; if the customer quote has no outcome, it earns no ink.
- **Do NOT bury the number.** Funding amount, customer count, performance figure - whatever the strongest number is, it appears in the headline or lede, not paragraph four.
- **Do NOT publish unapproved quotes or unverified facts.** A corrected release is a credibility wound with every journalist who ran the original.
- **Do NOT exceed 600 words.** Length signals the writer could not find the news either.

## Quality bar

- Headline: <100 chars, news verb, zero adjectives, survives the "would a journalist keep this word" test.
- Cut-from-the-bottom test passes: deleting everything after paragraph 2 still leaves a complete, accurate story.
- Both quotes do their assigned jobs (vision / outcome) and carry named, approval-flagged attribution.
- Dateline, third-person voice, boilerplate length, and `###` marker all conform.
- Every number traced to a source; every [FILL] resolved or explicitly flagged.

## Escalation and neighbors

Material announcements for companies with public-market or regulatory exposure (funding specifics, financial results, M&A) need legal review before distribution - this skill drafts, counsel clears. For the Product Hunt motion around a launch, route to product-hunt-launch; for running the launch day across channels, launch-day-runbook; for tracking who covered the release and how, media-monitor. If the underlying announcement is a crisis response rather than good news, use crisis-comms-external instead - the rules invert.
