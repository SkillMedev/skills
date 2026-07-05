---
name: PII Scrubber
description: Detects and redacts personally identifiable information from text, logs, datasets, and LLM prompts using layered pattern, checksum, and NER detection, then picks the right redaction mode for each downstream use. Use when someone asks "scrub PII from these logs", "redact personal data before sending it to the LLM", "is this dataset safe to share", or is wiring up logging, analytics, or third-party exports that might carry user data. Do NOT use for leaked API keys, tokens, or credentials — use secrets-hygiene instead.
---

# PII Scrubber

Personal data that reaches logs, analytics, prompts, or exports is a liability that outlives the feature that leaked it — retention systems copy it, backups preserve it, and deletion requests cannot find it. The outcome of this skill is a scrubbing setup that catches PII at the boundary and redacts it in the least destructive mode that still protects the person. The costly mistake it prevents is redacting so naively that either PII slips through in free text or the data becomes useless for the job it was collected for.

## Operating procedure

Detection precedes mode selection because the right redaction depends on what the downstream consumer needs from each field — decide per field, not per dataset.

### Step 1: Gather inputs

- The data source: logs, database export, chat transcripts, prompt payloads, analytics events.
- Downstream use: debugging, analytics joins, support lookups, model training, third-party sharing. This decides the redaction mode.
- Whether records must remain joinable after scrubbing (tokenize) or only comparable for equality (hash) or neither (mask or remove).
- Jurisdictions and regimes in play (GDPR, CCPA, HIPAA). Default to the strictest plausible; label the assumption a guess.
- Locales in the data — phone, postal, and ID formats vary by country.

### Step 2: Inventory the categories to detect

Names, emails, phone numbers, postal addresses, government IDs (SSN, passport), dates of birth, credit card and bank account numbers, IP addresses, MAC addresses, geolocation, biometric references, medical record numbers, vehicle plates, usernames, device IDs, URLs with embedded tokens, and free-text quasi-identifiers.

### Step 3: Detect in layers

1. Deterministic patterns for structured PII (regex plus checksums).
2. Validate wherever possible — Luhn for cards, format checks for SSN and IBAN — to cut false positives.
3. Named-entity recognition for names, locations, and organizations in free text.
4. Context rules: a 9-digit number near the word "SSN" is higher confidence than one standing alone.

```text
email:  [^\s@]+@[^\s@]+\.[^\s@]+
card:   \b(?:\d[ -]?){13,16}\b   then Luhn-validate
ipv4:   \b\d{1,3}(\.\d{1,3}){3}\b
```

### Step 4: Choose the redaction mode per field

- Mask: j***@example.com, ****-****-****-1234 (keep last 4 for support workflows).
- Tokenize: replace with a stable pseudonym so records still join (USER_a1b2). Make tokenization deterministic with a secret salt so the same value maps consistently — and rotate that salt carefully, because rotation breaks joins.
- Hash: one-way, for analytics that only need equality.
- Remove: drop the field entirely when nothing downstream needs it.

Default to the most aggressive mode that still serves the downstream need. If nobody can say why a field is needed, remove it.

### Step 5: Apply at the boundary

Scrub before logging, before sending to third parties, before LLM prompts — never after the data is already at rest in the destination. Maintain an allowlist of fields safe to emit rather than a blocklist of bad ones: blocklists fail open when a new field appears; allowlists fail closed.

### Step 6: Verify with sampling

Pull a random sample of at least 100 scrubbed records (or 1% of the dataset, whichever is larger) and review for false negatives — typos, unusual formats, and free-text leaks that patterns miss. Combine NER with patterns and repeat sampling after any format change in the source.

## Worked artifact: log line, bad vs good

Bad — raw PII straight into the log store:

```
INFO payment failed for jane.doe@example.com card 4111111111111111 from 203.0.113.42
```

Good — tokenized user, masked card, generalized IP, still debuggable and joinable:

```
INFO payment failed user=USER_a1b2 card=****1111 ip=203.0.113.0/24 error=insufficient_funds
```

The good line supports the two real jobs — joining this user's events (stable token) and support lookup (last 4) — while carrying no recoverable PII. Never log the raw value alongside the redacted one; that pattern silently defeats the entire pipeline.

## Compliance notes

- GDPR/CCPA: support deletion and access requests — deterministic tokenization is what lets you find all of a person's records to honor them.
- Minimize: do not collect or retain PII that is not needed for a stated purpose.
- Audit: log that redaction ran and what rules fired — never what it redacted.

## Edge cases

- False negatives in free text (typos, unusual formats): patterns alone are insufficient; layer NER and review samples.
- Quasi-identifiers: combinations like zip + date of birth + gender can re-identify individuals; for shared datasets, generalize until each combination matches at least a handful of records (k-anonymity, commonly k of 5 or more).
- Internationalization: use locale-aware validators for phone numbers and national IDs; a US-only SSN regex misses every other country's equivalents.

## Deliverable

Produce a scrubbing specification containing: the field inventory with a PII category per field, the detection layer and validation applied to each, the chosen redaction mode per field with the downstream justification, the boundary points where scrubbing executes, and the sampling verification plan with its false-negative findings.

## Do NOT

- Do not scrub after the data lands in the destination — backups and replicas have already copied it.
- Do not run a blocklist of "bad fields"; new fields ship unreviewed and leak. Allowlist what may be emitted.
- Do not log the raw value next to the redacted one, even at DEBUG level.
- Do not hash when records need to join later and tokenization was the requirement — and do not tokenize without a secret salt, or the pseudonyms are reversible by anyone who can guess inputs.
- Do not treat structured-field scrubbing as coverage for free-text fields; free text is where the misses live.
- Do not rotate the tokenization salt casually — it silently breaks every downstream join.

## Quality bar

- Every field in the output is on the allowlist with a named downstream reason, or absent.
- Structured PII is validated (Luhn, format checks), not just pattern-matched.
- Free text passed through NER plus context rules, and a sample of at least 100 records was human-reviewed for false negatives.
- The mode per field is the most aggressive that still serves the stated need.
- The audit trail records that redaction ran without containing redacted values.

## Escalation

This skill is not legal advice; for regime-specific obligations (GDPR lawful basis, HIPAA de-identification standards, CCPA service-provider terms) involve privacy counsel or a compliance officer. Credentials and API keys found while scrubbing route to secrets-hygiene immediately. If scrubbed datasets feed model training or evaluation, pair with llm-evaluation for the downstream quality checks.
