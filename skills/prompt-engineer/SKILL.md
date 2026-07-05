---
name: Prompt Engineer
description: Turns a vague request into a structured, reliable prompt — role, context, task, format, failure handling, and few-shot examples — that produces consistent output across real inputs. Use when someone asks "why does my prompt give inconsistent results", "write a prompt for this task", "the model keeps breaking my JSON", "how do I stop prompt injection from user input", or is building any LLM feature whose prompt was written ad hoc. Do NOT use for converting a working prompt into a reusable agent skill — use prompt-to-skill instead; do NOT use for measuring whether a prompt change improved quality — use llm-evaluation instead.
---

# Prompt Engineer

Turn a vague request into a prompt that produces consistent, high-quality output on inputs you have not seen yet. The costly mistake this prevents is the adjective prompt: "be detailed and professional" reads like an instruction but constrains nothing, so output quality varies run to run and the author iterates blind. Structure and examples constrain; adjectives decorate.

## Operating procedure

Ordering inside the prompt is load-bearing, not stylistic: instructions come before context so the model reads the data already knowing what to do with it, and the most important constraint is restated last because recency makes it stick.

### Step 1: Gather inputs

1. The single objective, stated in one sentence. If it takes two sentences, it is two prompts — split into a chain.
2. 3-5 real example inputs, including one ugly one. Iterate on real inputs, not imagined ones; label synthetic examples as synthetic.
3. Who or what consumes the output: a downstream parser wants JSON with a schema; a human wants markdown.
4. The failure policy: what the model should do when input is missing, ambiguous, or out of scope (default: return "UNKNOWN" rather than guess).

### Step 2: Build the four blocks, in this order

```text
ROLE: You are a senior <domain> expert.
TASK: <imperative, one goal — placed before the context it operates on>
FORMAT: Respond as <literal JSON schema / markdown table / bullets>.
RULES:
- If <field> is missing, return "UNKNOWN" rather than guessing.
- Keep reasoning internal; output only the final result.
CONTEXT:
<facts, constraints, audience — and any user-supplied content, delimited>
<restate the single most important constraint here, last>
```

Instructions before context: a model that reads the task first processes the context purposefully; a model that reads three pages of context first has already formed its own idea of the job. Pin the output format with a literal schema or example, never a description of one.

### Step 3: Replace adjectives with examples

Examples beat adjectives every time. Add 1-3 few-shot examples whenever the output shape is non-obvious, choosing examples that mark the decision boundaries — the near-misses, not the easy cases.

Bad — adjectives, no shape, no failure policy:

```text
Analyze this customer review and tell me the sentiment. Be accurate,
thorough, and professional. Don't get it wrong.

Review: "Shipping was fast but the box was crushed."
```

Why it fails: "accurate, thorough, professional" constrains nothing measurable; the output shape is unpinned so one run returns a paragraph and the next a single word; no labels are enumerated, so the model may invent "MOSTLY NEGATIVE"; mixed-signal reviews — the actual hard case — get whichever reading the sampling favors that run.

Good — enumerated labels, boundary-marking examples, pinned shape:

```text
Classify sentiment as POSITIVE, NEGATIVE, or NEUTRAL. Use only these
three labels. A review mixing praise and a product/delivery defect is
NEGATIVE.

Input: "Shipping was fast but the box was crushed."
Output: NEGATIVE

Input: "Exactly what I expected."
Output: NEUTRAL

Input: <review>${userText}</review>
Output:
```

The examples do the work the adjectives could not: the first one settles the mixed-signal boundary by demonstration.

### Step 4: Harden the boundaries

- Delimit user-supplied content with XML-style tags (`<review>...</review>`) so injected instructions inside the data cannot blur into your instructions.
- For classification, enumerate the exact allowed labels and forbid new ones.
- Prefer positive instructions ("respond in JSON") over negative ones ("don't use prose") — models follow targets better than prohibitions.
- Specify the failure mode explicitly; an unspecified failure mode means the model improvises one per run.

### Step 5: Iterate against real inputs and known edge cases

Run the prompt on the collected real inputs, then apply the standard fixes:

- Long context: place the question both before and after the documents.
- Inconsistent JSON: lower temperature, validate against a schema, retry once on parse failure.
- Hallucinated facts: instruct "only use the provided context; cite the sentence."
- Refusals on benign tasks: add a one-line justification of legitimate intent.
- Format drift over long conversations: restate the format in the latest turn; do not rely on the model remembering it.

When a change "seems better," measure it — hand the comparison to llm-evaluation rather than eyeballing three outputs.

## Deliverable

Produce a prompt file containing: the one-sentence objective, the four blocks in the Step 2 order, 1-3 boundary-marking few-shot examples, delimited slots for user content, an explicit failure-mode rule, and a note listing the real inputs it was iterated against.

## Do NOT

- Do not use quality adjectives ("detailed", "creative", "professional") as instructions — they are unfalsifiable and change nothing; encode the desired property as a rule or an example.
- Do not stuff multiple tasks into one prompt — each added task degrades all of them; split into a chain.
- Do not describe the output format in prose when you can pin it with a literal schema or example.
- Do not leave user-supplied content undelimited — that is the injection surface.
- Do not iterate on imagined inputs; the prompt will overfit to inputs no one sends.
- Do not bury the critical constraint in the middle — put it last, where recency bias makes it stick.

## Quality bar

- The objective is one sentence and the prompt serves only it.
- Output shape is pinned by a literal schema or example; a parser or reader gets the same shape every run.
- Every allowed label or enum value is enumerated; new ones are forbidden.
- User content sits inside delimiters; the failure mode is specified.
- Few-shot examples mark decision boundaries, not easy wins.
- Any claimed improvement is backed by a run on real inputs, not a single good sample.
