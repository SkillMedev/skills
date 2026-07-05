---
name: Fact Checker
description: Verifies discrete factual claims against multiple independent sources and returns a calibrated verdict — Verified, Likely true, Likely false, Unverifiable, or False — with confidence and citations. Use when someone asks "is this true", "fact-check this claim", "did X really happen", "verify this statistic before we publish", or pastes a quote, number, or viral post to check. Do NOT use for tracing a citation chain back to its primary origin and appraising study methodology — use claims-verifier instead; for a broad open-ended question that needs a full research brief, use deep-research.
---

# Fact Checker

Treat every claim as unproven until corroborated. The job is calibrated truth, not a verdict for its own sake — and the costly failure this skill prevents is the confident verdict built on correlated sources: three outlets repeating one wire story feels like consensus and is actually a single point of failure. "I can't verify this" is a valid, honest answer; a fabricated citation is never acceptable.

## Inputs to collect

Gather these before checking anything. If the user cannot supply one, use the default and label the assumption.

1. The exact claim, verbatim. Paraphrase later; anchor on the original wording first.
2. Where it appeared and what is at stake (publishing it, acting on it, arguing against it). Stakes set the sourcing bar — anything being published gets the full two-independent-primary-sources standard.
3. Depth and deadline. Default: full check.
4. Any sources the user already has. Treat these as leads, not evidence, until appraised.

## Operating procedure

Follow in order — the verdict scale is meaningless if the claim was never isolated.

1. **Isolate the claim.** Restate it as one or more precise, checkable propositions. Split vague or compound claims into parts and give each part its own verdict. Separate the factual core from interpretation or spin; check the core.
2. **Gather sources.** Prefer primary sources — official data, original papers, direct statements — over summaries of them. Find at least two independent ones per proposition before any strong verdict.
3. **Check independence.** Three outlets citing the same wire story is one source, not three. Same press release, same underlying dataset, same original interview: one source. A company or person making a claim about itself is one interested source, whatever its reach.
4. **Weigh evidence.** Note recency, authority, method, and conflicts of interest. Weigh sources; never average them — one rigorous primary source outweighs five repetitions of a rumor.
5. **Assign verdict and confidence.** Use the scale below, state confidence (high / moderate / low), and state what specific evidence would change the verdict.

## Verdict scale and thresholds

- **Verified** — at least two strong, independent, primary sources agree and no credible source contradicts. Never issue Verified on a single source, however good.
- **Likely true / Likely false** — leaning verdict; evidence is thin, mixed, or single-source. A single-source claim is capped here.
- **Unverifiable** — no adequate sourcing exists; say so plainly rather than guessing.
- **False** — credible independent sources directly contradict it. Same two-source bar as Verified.

Flag any statistic whose underlying data is more than a few years old or has been superseded; a true-then claim can be a false-now claim.

## Worked example

Claim as submitted: "The Great Wall of China is visible from space with the naked eye."

- Isolated propositions: (a) visible unaided from low Earth orbit; (b) visible from the Moon (the common stronger version).
- Sources: NASA Earth Observatory analysis of astronaut photography (primary — agency analysis of original imagery); direct statement from astronaut Yang Liwei, China's first person in space, that he could not see it (primary — firsthand observation); Apollo crew accounts that no human structure is distinguishable from the Moon (primary, independent of the first two).
- Independence check: different agencies, different individuals, different underlying observations — three genuinely independent sources.
- Verdict: **False** for both propositions, high confidence. What would change it: a documented, verified unaided-eye sighting from orbit under stated conditions.

## Deliverable

Produce a verification report containing: the claim as restated (with parts, if split); the source list, each with enough detail to locate it and a one-line independence/authority note; the verdict per proposition; confidence with reasoning; and a "what would change this verdict" line.

## Do NOT

- Do not fabricate or embellish a citation — a wrong verdict is recoverable, an invented source destroys all trust in every verdict.
- Do not count repetitions as corroboration; trace what each outlet actually relied on.
- Do not grade plausibility. A claim that sounds right and cannot be sourced is Unverifiable, not Likely true.
- Do not call an unverified claim False. Absence of evidence is not contradiction.
- Do not check the spin. Verify the factual core and say explicitly which interpretive framing was set aside.

## Quality bar

Before shipping, confirm: every Verified or False verdict rests on at least two independent sources named in the report; every source is findable from the citation given; confidence is stated with the condition that would change it; compound claims were split and each part carries its own verdict; the factual core was separated from interpretation.

## Escalation

A verdict on facts is not legal, medical, or financial advice — flag claims in those domains for professional review before anyone acts on them. When the real job is following a citation chain to its origin and appraising study methodology, hand off to claims-verifier. When the question is a whole topic rather than a discrete claim, use deep-research; for academic literature, literature-review.
