---
name: Claims Verifier
description: Decomposes a claim into checkable propositions, traces each through the citation chain to its primary source, appraises the evidence for method and independence, and issues a verdict from Supported to Contradicted with confidence. Use when someone asks "is this claim actually backed by the study it cites", "verify the claims in this whitepaper", "trace this statistic to its source", or needs an evidentiary audit of research, marketing, or policy claims. Do NOT use for a quick true/false corroboration of a discrete factual statement - use fact-checker instead; for broad open-ended investigation of a topic, use deep-research instead.
---

# Claims Verifier

The job is to determine whether a claim is supported by evidence - not whether it sounds plausible. The costly failure this skill prevents is laundered authority: a weak source cited by a stronger one until the claim looks settled, when the entire edifice rests on one press release. Extraordinary claims require extraordinary evidence.

## Inputs to collect

1. **The claim text**, verbatim, with its original context (where it appeared, who made it).
2. **The stakes**: what decision the verdict will inform. High-stakes claims get deeper chain-tracing.
3. **Any citations already attached** to the claim.
4. **Access constraints**: paywalled sources the user can or cannot retrieve. If a link in the chain is inaccessible, say so rather than guessing its contents.

## Operating procedure

### Step 1: Decompose the claim

Restate the claim as one or more single, falsifiable propositions. Apply the decomposition rules:

- One proposition per checkable fact. "The drug is safe and effective" is two claims with different evidence bases - split it.
- Strip rhetoric and quantify vagueness: "many studies" becomes "at least N studies exist showing X"; "linked to" becomes either "causes" or "is correlated with" - force the claimant's strongest defensible reading, and check that.
- Label un-falsifiable propositions ("best-in-class", predictions with no deadline) as unverifiable and set them aside - do not let them free-ride on verified neighbors.

### Step 2: Trace each proposition to its primary source

Follow the citation chain to origin:

- News article → cites a study → find the actual study, not the press release.
- "Studies show" → which studies? Locate them by author, year, venue.
- A statistic → find the dataset and the methodology that produced it.

Stop only at original data, a primary document, or a dead end. Record every hop. Note where the chain breaks ("source cites a source that no longer exists") - a broken chain is a finding, not a failure.

### Step 3: Test source independence

Multiple sources only count as corroboration if they are independent. Apply the independence rules:

- Trace each source's own citations: three outlets repeating one wire story or one press release are **one** source.
- Shared funding, shared authors, or shared institutional interest collapse independence - count them as one voice and note the interest.
- Genuine independence means separately gathered data or separately conducted analysis. Two sources are independent only when neither could have copied the other and they do not share a common upstream origin.

### Step 4: Appraise each primary source

- **Authority**: who produced it, with what expertise and what incentives.
- **Method**: how the evidence was gathered; sample size and representativeness; is it reproducible.
- **Recency**: current, or superseded by later work.
- **Status**: peer-reviewed vs preprint vs self-published; check for retractions and corrections before relying on any paper.

### Step 5: Check for missing evidence

Flag the standard evidentiary gaps: correlation presented as causation; sample too small or unrepresentative; cherry-picked timeframe or subgroup; missing control or baseline; anecdote generalized to a population; a confident conclusion resting on a preprint or a retracted paper.

### Step 6: Assign a verdict with confidence

- **Supported** - multiple independent primary sources agree.
- **Partially supported** - the core is true but overstated or context-dependent; state exactly which part survives.
- **Unsupported** - no credible primary source found.
- **Contradicted** - primary evidence refutes it.
- **Unverifiable** - not falsifiable, or sources inaccessible.

Attach confidence (high / medium / low) driven by the count of independent primary sources and the strength of their methods - not by how often the claim is repeated.

## Worked artifact: filled verification

**Claim as published**: "Remote workers are 47% more productive, according to a Stanford study."

- **Decomposition**: (a) a Stanford-affiliated study exists measuring remote-work productivity; (b) it found a 47% productivity gain; (c) the finding generalizes to remote workers broadly.
- **Chain trace**: blog post → HR-vendor infographic → press coverage of a Stanford GSB experiment at a Chinese travel agency (call-center workers randomized to work from home). Primary source located: the experiment reports a ~13% performance increase; a "47%" figure appears only in a separate vendor survey of self-reported productivity, unaffiliated with Stanford.
- **Independence check**: all secondary sources trace to the same two origins - one experiment, one self-report survey. Effective independent sources: 2, measuring different things.
- **Gaps**: single firm, single role (call-center), volunteers who opted in - generalization to knowledge workers unsupported; self-reported productivity is not measured productivity.
- **Verdict**: (a) Supported; (b) **Contradicted** - the study found ~13%, not 47%, and 47% comes from a different, weaker source; (c) Unsupported. Confidence: high, primary sources retrieved and quoted.

## Deliverable

Produce a verification report containing, per proposition: the falsifiable restatement, the full source chain with each hop, the independence count, the gaps found, the verdict, and the confidence - with a link or full citation to every primary source so the reader can check the work.

## Do NOT

- Do not treat repetition as corroboration - popularity is not evidence, and citation loops manufacture false consensus.
- Do not say "false" when the honest verdict is "unverified" - absence of evidence is not evidence of absence.
- Do not paraphrase a primary source in a way that strengthens the claim; quote verbatim.
- Do not invent or approximate a citation - a fabricated source is worse than no verdict.
- Do not let prior beliefs set the verdict; the chain and the appraisal do.

## Quality bar

- Every proposition has a chain traced to a primary source or an explicitly noted dead end.
- The independent-source count is stated, after collapsing shared origins.
- Each verdict names the specific evidence that drove it, and the reader could re-verify in under ten minutes from the citations given.
- Overstated claims get "partially supported" with the surviving core spelled out - not a lazy binary.

## Escalation

For claims with legal exposure (defamation, securities, regulated health claims), the report informs but does not replace counsel review. Route quick single-fact checks to fact-checker and topic-wide investigations to deep-research.
