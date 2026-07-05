---
name: rfp-response-writer
description: Answers RFPs and security questionnaires with discipline - a go/no-go score before any writing (win probability times deal size against effort, under 30 percent win chance means decline), a compliance matrix mapping every requirement to a response, answer-library reuse, and win themes threaded through every section. Use when a user says "we got an RFP, help us respond", "should we even bid on this RFP", "build a compliance matrix for this RFP", "answer this security questionnaire", or "our RFP responses take weeks and we keep losing". Do NOT use for proactive proposals sent without a formal solicitation - use sales-proposal-writer instead.
---

# RFP Response Writer

RFP responses are lost twice: first by bidding on RFPs that were wired for another vendor, and second by answering requirements the evaluators cannot find. The costly mistake this skill prevents is spending two weeks of team effort on a bid with a sub-30-percent win chance - the single largest waste in most proposal operations. Scoring go/no-go first, mapping every requirement in a compliance matrix, and reusing an answer library turns RFP response from an all-hands fire drill into a repeatable process.

## Operating procedure

Steps run in strict order. Go/no-go comes before any writing because the most valuable output of this skill is often a fast, well-reasoned decline. The compliance matrix comes before drafting because evaluators score against the requirement list, not against prose quality - an eloquent response that misses requirement 4.2.c scores zero on 4.2.c.

### Step 1: Score go/no-go

Collect the six inputs below, compute expected value against effort, and issue a verdict before anyone drafts a sentence.

- **Win probability.** Score honestly from evidence: existing relationship with the buyer (+), you helped shape the requirements (+, strong), an incumbent is rebidding (−, strong), the requirements read like a competitor's datasheet (−, near-fatal), you learned of the RFP only at public release with no prior contact (−). If two or more strong negatives apply, cap the estimate at 20 percent.
- **Deal size** - total contract value, not year one.
- **Effort cost** - loaded hours to respond times loaded hourly rate. A mid-size RFP commonly burns 40-120 team hours; a large government bid several times that.
- **Strategic value** - reference-ability, market entry, competitive block. Real but secondary; it tempers a marginal score, it does not rescue a bad one.
- **Deadline feasibility** - can a compliant response ship without wrecking other commitments.
- **Mandatory disqualifiers** - certifications, insurance, geography. Missing any mandatory requirement is an automatic no-bid regardless of everything else.

**Decision rule:** decline when win probability is under 30 percent - below that line, expected value rarely clears effort cost and the team's hours win more elsewhere. Between 30 and 50 percent, bid only if expected value (win probability × deal size) exceeds 10× the effort cost or the strategic value is named and endorsed by leadership. Above 50 percent, bid. Record the verdict and reasoning in one paragraph; a declined RFP gets a courteous decline note to the issuer, which preserves the relationship for the next one. Label every estimated probability as an estimate.

### Step 2: Build the compliance matrix

Extract **every** requirement - numbered requirements, "shall" statements, submission-format rules, page limits, font sizes, deadline and delivery method - into a matrix. Format rules are requirements: responses get disqualified unopened for a wrong file format or a blown page limit.

```
| Req # | Requirement (verbatim) | Comply? (Full/Partial/No) | Response location | Owner | Status |
```

Rules: quote requirements verbatim, never paraphrased, because evaluators check the exact words. Every row gets an owner and a response location (section and page). "Partial" compliance is stated honestly with the gap and mitigation - evaluators punish discovered overstatement far harder than disclosed gaps. The matrix ships inside the response itself when permitted; evaluators reward vendors who do the mapping work for them.

### Step 3: Pull from the answer library, then write the gaps

Before drafting anything new, pull matching answers from the answer library - the versioned repository of previously approved responses (company overview, security architecture, SOC 2 posture, support SLAs, implementation methodology, data handling). Then:

1. Mark each pulled answer **reuse-as-is**, **tailor**, or **stale** (older than 12 months or touching anything that changed - flag stale answers for owner re-verification before reuse, because a stale security answer is a misrepresentation risk).
2. Tailor every reused answer's first and last sentence to this buyer - evaluators smell pure boilerplate and read it as low effort.
3. Draft net-new answers only for true gaps, and file each new approved answer back into the library so the next RFP is cheaper. A mature library answers 60-80 percent of a typical questionnaire; security questionnaires reach higher because the questions standardize.

### Step 4: Thread the win themes

Define 2 or 3 win themes - the reasons this buyer should pick you over the field, stated as buyer outcomes, not features ("cuts claim-processing time in half," not "modern architecture"). Then thread them: every major section opens or closes with a sentence tying its content to a theme, in the buyer's vocabulary from the RFP itself. A response where the themes appear only in the executive summary has not threaded them; an evaluator reading any single section cold should be able to name at least one theme.

### Step 5: Assemble, verify, submit

Assemble in the RFP's mandated order. Run the quality bar. Verify the submission mechanics twice - portal login, file format, naming convention, deadline timezone - because mechanical disqualification is the most preventable loss in the entire process. Submit at least 24 hours before the deadline; portals fail at 4:55pm on due dates.

## Inputs to collect

- The complete RFP document, all attachments and amendments (required).
- Deal size and contract term (required).
- Relationship history with the buyer - who, when, did you shape the requirements (required for scoring; if unknown, score conservatively and label it).
- Answer library access, or the last two responses to mine one from (default: start a library from this response).
- Team hours available and loaded rate (default guess: 80 hours at $150/hour - label as guess).
- Named competitors likely bidding, if known.

## Concrete thresholds

- Win probability under 30 percent → decline. 30-50 percent → bid only if expected value > 10× effort cost or leadership endorses named strategic value. Over 50 percent → bid.
- Two or more strong negative signals → cap win probability at 20 percent.
- Any missed mandatory requirement → automatic no-bid.
- Answer library reuse target: 60-80 percent of questionnaire items; every reused answer's first and last sentence tailored.
- Answers older than 12 months → re-verify before reuse.
- Submit ≥24 hours before deadline.

## Worked example - go/no-go verdict

```
RFP: Regional health system, claims-analytics platform. TCV $840,000 / 3 yr.
Signals: no prior relationship (−), requirements name a competitor's module
verbatim (− strong), learned at public release (− strong), we hold the
required HITRUST cert (+).
Win probability: two strong negatives → capped at 20%. Estimate: 15% (estimate).
Effort: ~90 hours × $150 = $13,500.
Expected value: 0.15 × $840,000 = $126,000 - but probability is under the
30% line.
VERDICT: DECLINE. Send the decline note, request a debrief call, ask to be
included pre-RFP next cycle. Log in pipeline for relationship-building.
```

## Template - compliance matrix row + section skeleton

```
COMPLIANCE MATRIX - [FILL: RFP name] - [FILL: due date + timezone]
| Req # | Requirement (verbatim) | Comply? | Location | Owner | Status |
| [FILL] | "[FILL: exact RFP text]" | Full/Partial/No | §[FILL], p.[FILL] | [FILL] | draft/review/final |

SECTION SKELETON
  Win theme tie-in (1 sentence, buyer vocabulary): [FILL]
  Direct answer to the requirement (first sentence answers it plainly): [FILL]
  Evidence: [FILL: metric, named customer outcome, or certification]
  Library source: [FILL: answer ID + reuse-as-is / tailored / net-new]
```

## Deliverable

Either a one-paragraph documented no-bid decision with a decline note, or a submission-ready response package: the completed compliance matrix with every requirement owned and located, the assembled response with 2-3 win themes threaded through every major section, the reuse ledger showing which answers came from the library, and the updated answer library.

## Do NOT

- **Do NOT start writing before the go/no-go verdict.** Sunk drafting effort is the main reason teams bid RFPs they should decline.
- **Do NOT paraphrase requirements in the matrix.** Evaluators match exact language; a paraphrase that drops one clause becomes a silent non-compliance.
- **Do NOT overstate compliance.** A discovered "Full" that was really "Partial" can disqualify the whole response and poison the next bid; a disclosed gap with mitigation often scores acceptably.
- **DO NOT paste library answers untouched.** Untailored boilerplate signals the buyer wasn't worth an hour of effort - evaluators score accordingly.
- **Do NOT treat format rules as suggestions.** Page limits, fonts, file naming, and delivery method are pass/fail gates enforced before anyone reads a word.
- **Do NOT ignore amendments and Q&A addenda.** They frequently change requirements late; the matrix must be rechecked after every one.

## Quality bar

- Go/no-go verdict recorded with reasoning before drafting began.
- Every RFP requirement - including format and submission rules - appears as a matrix row with owner, location, and honest compliance status.
- Every major section names a win theme in the buyer's vocabulary; an evaluator reading one section cold can identify a theme.
- No reused answer is older than 12 months unverified; first and last sentences of reused answers are tailored.
- Submission mechanics verified twice; submitted ≥24 hours early.
- New answers filed back into the library.

## Escalation and neighbors

Contract terms, liability caps, and indemnity language in the RFP go to counsel - this skill drafts responses, not legal positions. Security-questionnaire answers touching certifications or audit posture need sign-off from the security owner; for assembling the underlying audit evidence, route to soc2-evidence-helper. For proactive proposals where no formal RFP exists - you are creating the buying vision rather than answering a solicitation - use sales-proposal-writer; the two motions differ exactly there: proposals persuade an open question, RFP responses score against a fixed rubric.
