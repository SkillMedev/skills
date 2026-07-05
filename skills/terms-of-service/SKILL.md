---
name: Terms of Service
description: Drafts plain-English Terms of Service and Privacy Policies for SaaS products using a clause-by-clause checklist, flagging every business decision and lawyer-review item explicitly. Use when someone asks "write terms of service for my app", "I need a privacy policy", "draft my ToS", "what sections does a SaaS ToS need", or is preparing legal pages before launch. Do NOT use for employee offer letters, NDAs, or employment agreements - use employment-contract instead. Do NOT use for assessing which regulations apply to a product or market - use regulatory-scan instead.
---

# Terms of Service

Draft Terms of Service and Privacy Policies for SaaS products in plain English. Readable beats clever: users and regulators both reward clarity, and courts have voided clauses buried in impenetrable prose. The two costly failures this skill prevents are shipping a ToS with a missing load-bearing section (no liability cap, no termination clause) and publishing a privacy policy that describes practices the product does not actually have - the second is worse, because a false privacy promise is an FTC deception claim waiting to happen.

The output is a strong first draft, not legal advice. Every draft must state, prominently, that a qualified attorney must review before publishing - this is a hard rule, not a courtesy line.

## Operating procedure

### Step 1: Gather inputs

Collect before drafting. Where the user does not know, propose a default and label it [DECISION NEEDED].

1. Product name and one-sentence description of what it does.
2. Who can use it: minimum age (default 13 in the US due to COPPA; 16 in parts of the EU), geographies served.
3. Data collected: directly, automatically (analytics, logs), and from third parties.
4. Payment model: free, subscription, usage-based; refund stance.
5. Third-party processors: payments, analytics, hosting, email - the real list, by name.
6. User-generated content: does the product host any, and who owns it.
7. Governing-law jurisdiction (default: where the company is incorporated) and dispute preference (courts vs. arbitration - [DECISION NEEDED], never silently chosen).

### Step 2: Draft the ToS against the clause checklist

Work through the checklist below in order; every SaaS ToS must cover at minimum the license grant, acceptable use, termination, liability limits, and dispute resolution. A missing clause is a gap the company discovers in its worst week.

### Step 3: Draft the Privacy Policy as a separate document

Never merge the two; they have different audiences and different legal weight.

### Step 4: Flag, then package

Mark every item needing counsel as [LAWYER REVIEW] and every open business choice as [DECISION NEEDED]. Deliver both documents with an effective-date placeholder and the consolidated flag list on top.

## ToS clause-by-clause checklist

```
[ ] 1.  Acceptance of terms - how assent happens (clickwrap beats browsewrap;
        courts routinely refuse to enforce terms nobody clicked)
[ ] 2.  Description of the service - plain English, one paragraph
[ ] 3.  Eligibility - minimum age, account requirements
[ ] 4.  User accounts & responsibilities - credential security, one person per seat
[ ] 5.  License grant - limited, non-exclusive, non-transferable, revocable right
        to use the service; company retains all IP  [LOAD-BEARING]
[ ] 6.  Acceptable use / prohibited conduct - abuse, scraping, resale, illegal
        content, security probing  [LOAD-BEARING]
[ ] 7.  Subscription, billing & refunds - renewal mechanics, price-change notice,
        refund stance  [DECISION NEEDED: refund policy]
[ ] 8.  User content & IP - user keeps ownership; company gets the license it
        needs to operate (host, display, back up) and no more
[ ] 9.  Termination & suspension - who can end it, for what, with what notice;
        what happens to user data after (export window, deletion timeline)
        [LOAD-BEARING]
[ ] 10. Disclaimers & limitation of liability - "as is" disclaimer; cap (commonly
        fees paid in the prior 12 months); exclusion of consequential damages
        [LOAD-BEARING] [LAWYER REVIEW: caps are unenforceable in some
        jurisdictions and against some claim types]
[ ] 11. Indemnification - user indemnifies for their content/misuse
[ ] 12. Governing law & dispute resolution - jurisdiction; courts vs. binding
        arbitration; class-action waiver  [LOAD-BEARING] [DECISION NEEDED +
        LAWYER REVIEW: arbitration and class waivers are business decisions with
        enforceability limits]
[ ] 13. Changes to terms - how users are notified, and that material changes
        need affirmative notice, not a silent edit
[ ] 14. Contact information
```

## Privacy Policy section checklist

1. What data is collected, and how (directly, automatically, from third parties)
2. Why - with legal basis under GDPR where it applies
3. How it is used
4. Who it is shared with - name the actual processors (analytics, payments, hosting)
5. Cookies and tracking
6. Data retention periods (real ones, not "as long as necessary" alone)
7. User rights - access, deletion, portability, opt-out - and how to exercise them
8. Security measures (described honestly, not aspirationally)
9. International data transfers [LAWYER REVIEW if EU users]
10. Children's data (COPPA if under-13 users are possible)
11. Contact / DPO
12. Effective date and change notification

## Worked contrast: the liability clause

Bad: "In no event shall the Company, its affiliates, or licensors be liable for any indirect, incidental, special, exemplary, consequential or punitive damages howsoever arising under any theory of liability whether in contract, tort or otherwise..."

Good: "We built [FILL: Product] carefully, but we provide it 'as is.' To the extent the law allows: we are not liable for indirect damages (like lost profits or lost data), and our total liability to you is capped at what you paid us in the 12 months before the claim. [LAWYER REVIEW: cap enforceability varies by jurisdiction.] Some places don't allow these limits, so parts of this section may not apply to you."

Both attempt the same protection. Only one will a user read, and only one flags its own enforceability risk.

## Plain-English rules

- Short sentences. Define a term once, then use it consistently.
- Use "you" and "we." Never "the Party of the first part."
- Headers and numbered clauses so users (and support teams) can cite sections.
- Where law requires specific language (GDPR rights, auto-renewal disclosures), keep it precise - do not oversimplify away legal meaning.

## Deliverable

Produce two documents - ToS and Privacy Policy - each with numbered sections, an effective-date placeholder, and a consolidated list at the top of every [LAWYER REVIEW] and [DECISION NEEDED] item, plus a closing restatement that this is a draft requiring attorney review before publication.

## Do NOT

- Do NOT publish-ready-ify the draft by removing the flags; the flags are the product's honesty layer.
- Do NOT claim "we never sell your data" or any practice the product has not verified - false privacy statements create regulatory liability that no ToS can cap.
- Do NOT silently pick aggressive terms (arbitration, class waivers, broad IP grabs on user content); mark them [DECISION NEEDED] and explain the trade-off in one sentence.
- Do NOT copy a competitor's ToS; it encodes their processors, their jurisdiction, and their risk decisions.
- Do NOT merge ToS and Privacy Policy into one document.
- Do NOT let the processor list in the privacy policy drift from the real subprocessor list.

## Quality bar

The draft ships only when: all five load-bearing clauses (license grant, acceptable use, termination, liability limits, dispute resolution) are present; every checklist item is either drafted or explicitly marked out-of-scope with a reason; the flag list is consolidated on top; the processor list matches what the user actually named; and the attorney-review requirement appears at both the top and bottom of the deliverable.

## Escalation

This is not legal advice, and no draft leaves without saying so. Attorney review is required before publishing in all cases, and is urgent - not optional - when any of these apply: GDPR or CCPA exposure, HIPAA or health data, payment handling beyond a standard processor, users under 13, or regulated industries. For figuring out which regulations apply in the first place, route to regulatory-scan; for employment paperwork, route to employment-contract.
