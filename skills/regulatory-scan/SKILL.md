---
name: Regulatory Scanner
description: Maps which regulations apply to a product or business across every operating jurisdiction, translates them into concrete obligations, and produces a prioritized compliance-gap table. Use when someone asks "what regulations apply to my product", "are we GDPR or CCPA exposed", "run a compliance gap analysis", or is entering a new market or handling a new data type. Do NOT use for drafting the legal documents themselves - use terms-of-service instead. Do NOT use for assembling SOC 2 audit evidence - use soc2-evidence-helper instead. Do NOT use for patent landscape questions - use patent-prior-art instead.
---

# Regulatory Scanner

Produce a clear map of which regulations apply to a product and where the gaps are - research and triage, not legal advice. The costly failure this prevents is discovering an obligation from the enforcement letter: teams routinely learn that a checkout flow triggered a state privacy law, or a health-adjacent feature crossed into HIPAA territory, only after the exposure exists.

## Operating procedure

Jurisdiction drives everything, so scoping comes first; a scan that assumes US-only while the product sells into the EU is worse than no scan, because it creates false confidence.

### Step 1: Gather inputs

Collect from the user:

- What the product does, in plain terms, and the industry it serves.
- What data it collects, stores, or processes - especially personal data, health data, payment data, children's data, biometric data.
- Who the customers are (consumers vs. businesses; whether any are minors).
- Every jurisdiction where it operates, sells, or has users - not just where it is incorporated. Serving EU residents triggers GDPR regardless of company location.
- Company scale (revenue, user counts) - many regimes have applicability thresholds.

Label anything assumed as a guess and confirm before Step 2, because a wrong jurisdiction list invalidates the whole map.

### Step 2: Identify applicable regulatory domains

Work through the domains systematically and mark each apply / not apply / unclear, with a one-line reason:

- **Data & privacy**: GDPR (EU residents; fines up to 4% of global annual turnover or €20M, whichever is higher), CCPA/CPRA (California; applicability thresholds include ~$25M+ annual revenue or data on 100,000+ consumers/households), other US state privacy laws, sector privacy rules.
- **Sector-specific**: HIPAA (health data held by covered entities and business associates), GLBA and PCI-DSS (finance/payments), FDA (medical devices, food, software-as-medical-device), FCC (telecom), FTC Act §5 (unfair or deceptive practices - the catch-all that reaches almost every consumer product), COPPA (users under 13).
- **Product safety & labeling**; **accessibility** (ADA, WCAG, EN 301 549); **employment & labor**; **environmental**; **export controls** (EAR/ITAR, sanctions).
- **Emerging**: AI regulation (EU AI Act risk tiers), platform and content rules (DSA).

"Unclear" is a legitimate status - it becomes a question for counsel, which is more useful than a guess.

### Step 3: Translate each regulation into requirements

For each applicable regulation, list the concrete obligations it imposes - consent mechanisms, breach notification timelines, record-keeping, audits, disclosures, data-subject rights. Cite the specific article or section so every requirement is traceable and checkable. Precision matters because obligations carry real deadlines: GDPR breach notification to the supervisory authority is 72 hours from awareness (Art. 33); GDPR data-subject access requests must be answered within one month (Art. 12).

### Step 4: Assess current state and score gaps

For each requirement, mark status: **Met / Partial / Not met / Unknown**. "Unknown" is a gap, not a pass. Prioritize gaps by three factors:

1. **Penalty exposure** - fines, injunctions, criminal liability, private rights of action.
2. **Likelihood of enforcement** - active regulator, audit triggers, complaint-driven regimes.
3. **Effort to remediate** - quick disclosures before multi-quarter engineering work.

High exposure + high likelihood + low effort is the top of the list.

### Step 5: Output the compliance map

Fill one row per requirement:

```
Regulation | Jurisdiction | Requirement (with citation) | Status | Gap | Priority | Owner

Example row:
GDPR       | EU           | Notify supervisory authority of a personal-data breach
                            within 72h of awareness (Art. 33)  | Not met | No incident-response
                            playbook with notification step    | HIGH    | [FILL]
[FILL]     | [FILL]       | [FILL]                             | [FILL]  | [FILL] | [FILL] | [FILL]
```

Add a narrative summary of the top 3 risks in plain language an executive can act on, and note the date the scan was performed - regulations change, and an undated scan misleads.

### Step 6: Recommend next steps

For each high-priority gap, suggest a concrete remediation action and an owner. Explicitly flag every item that needs qualified legal counsel before action - binding interpretation, filings, regulator contact, anything ambiguous in Step 2.

## Deliverable

Produce: the jurisdiction and data inventory, the domain applicability checklist with reasons, the compliance map table (Regulation | Jurisdiction | Requirement + citation | Status | Gap | Priority | Owner), a top-3 risk narrative, a remediation list, and an explicit "requires counsel" list.

## Do NOT

- Do NOT assume US-only or headquarters-only exposure; check every jurisdiction where users actually are.
- Do NOT mark a requirement "Met" on the team's say-so without an artifact (policy, log, control) behind it - mark it Partial or Unknown.
- Do NOT blend hard law (statutes, regulations) with soft guidance (frameworks, best practices) in the same rows without labeling which is which; only one carries penalties.
- Do NOT interpret ambiguous statutory language definitively - state the plausible readings and route to counsel.
- Do NOT let the scan go stale; stamp the date and recommend re-scanning on market entry, new data types, or major regulatory news.

## Quality bar

- Every requirement carries a specific article/section citation.
- Every status is one of Met / Partial / Not met / Unknown - no blanks, and Unknowns are counted as gaps.
- Priorities reflect all three factors (exposure, enforcement likelihood, effort), not just fine size.
- The "requires counsel" list is present and non-empty for any non-trivial product.

## Escalation

This is a research scan, NOT legal advice - no output from it should be treated as a binding interpretation, and nothing here creates an attorney-client relationship. Bring in qualified counsel for: any binding interpretation, all filings and regulator communications, anything marked "unclear" in Step 2, and any regime with criminal liability or where enforcement is active in the user's sector. For drafting policies and terms, route to terms-of-service; for audit-evidence assembly, route to soc2-evidence-helper.
