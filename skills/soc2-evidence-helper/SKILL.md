---
name: SOC 2 Evidence Helper
description: Maps engineering controls to SOC 2 Trust Service Criteria, builds a continuous evidence-collection plan with cadences per control family, and produces the control-to-evidence table auditors work from. Use when someone asks "what evidence do we need for SOC 2", "map our controls to the Trust Service Criteria", "how do we prepare for Type II fieldwork", "the auditor asked for access reviews", or is closing findings or standing up a compliance program. Do NOT use for building an incident postmortem — use postmortem-writer instead; do NOT use for finding and prioritizing actual vulnerabilities — use vulnerability-triage instead; this skill organizes proof that controls operate, it does not implement the controls.
---
# SOC 2 Evidence Helper

SOC 2 audits test whether security controls exist (Type I, point in time) and operate consistently over a review period (Type II, typically 3–12 months). Engineering teams lose the most time gathering evidence reactively in the month before fieldwork — and for Type II that scramble cannot work, because auditors sample from the whole period and a gap in month two is already a finding. Build collection into normal operations instead.

## Operating procedure

### Step 1: Gather inputs

- Type I or Type II, and the review period dates. Default assumption: first audit is Type I, followed by a 6-month Type II.
- Which Trust Service Criteria are in scope (Security is mandatory; the rest are contract-driven). If unknown, check customer contracts and security questionnaires — scope only what customers demand.
- The systems of record: IdP (Okta, Entra), source control, CI, cloud provider, ticketing, HR system, vulnerability scanner.
- Any prior findings or bridge letters. Label unknowns as unknowns.

### Step 2: Scope the criteria before collecting anything

Auditors only test criteria in scope:

- Security (CC — required): logical access, change management, risk assessment, incident response, monitoring.
- Availability (A): uptime commitments, capacity planning, backup and recovery.
- Processing Integrity (PI): complete and accurate processing, error handling.
- Confidentiality (C): data classification, encryption in transit and at rest, NDA controls.
- Privacy (P): personal data lifecycle — notice, consent, retention, deletion.

### Step 3: Build the control-to-evidence map

For each in-scope criterion, name the control, the evidence artifact, the system of record, the owner, and the cadence. This table is the audit's backbone — create it in a shared doc before fieldwork. Never create artifacts that do not reflect how the system actually operates: auditors test consistency, and fabricated evidence is itself a finding.

Practitioner cadences by control family:

| Control family | Cadence | Red line |
|---|---|---|
| Access reviews (CC6.1) | Quarterly export + sign-off | Offboarding deprovisioned within 24 hours |
| Change management (CC8.1) | Continuous (every merged PR) | 100% of production changes via reviewed PR + passing CI |
| Monitoring/alerting (CC7.2) | Continuous config + monthly alert-triage sample | Every sampled alert shows an investigation trail |
| Vulnerability management (CC7.1) | Scans at least monthly; pen test annually | Criticals remediated within 15 days, highs within 30 |
| Backup/recovery (A1.2) | Backups continuous; restore test quarterly at minimum, annually as the floor | A restore actually performed, not just configured |
| Risk assessment (CC3) | Annual formal assessment | Documented with owners and treatment decisions |
| Incident response (CC7.3–7.5) | Plan reviewed annually; at least one tabletop per year | Post-incident reviews exist for real incidents |
| Policy attestations | Annual, plus onboarding | Timestamped acknowledgment per employee |
| Security awareness training | Annual, tracked per employee | Completion records with dates |

### Step 4: Automate collection into a compliance folder

- Access reviews: scheduled IdP export to a designated, access-controlled evidence folder — not ad hoc screenshots.
- Vulnerability scans: schedule the scanner and archive full reports automatically; never run scans only when the auditor asks.
- Pen test: retain the full report including findings and retest results, not just the executive summary.
- Policy attestations: a GRC tool or a timestamped form; manual sign-off with no timestamp is not auditable.
- Encryption and infrastructure config: export IaC or config screenshots as evidence — "enabled" without proof of correct configuration is a common finding.

### Step 5: Run the auditor interaction deliberately

- Provide the minimum necessary evidence — do not over-share system diagrams or design docs the control does not require.
- When a control has a gap (it was not operating for part of the period), disclose proactively with a documented remediation date. Auditors respond better to transparency than to discovering gaps in sampling.
- For Type II, maintain artifacts continuously across the whole period; auditors sample from all of it.

## Worked artifact: evidence-collection table

```
CONTROL-TO-EVIDENCE MAP — [FILL: company] — Period: [FILL: start] to [FILL: end]

| TSC ref | Control statement                          | Evidence artifact                     | System of record | Cadence     | Owner  | Status |
|---------|--------------------------------------------|---------------------------------------|------------------|-------------|--------|--------|
| CC6.1   | Quarterly user access reviews              | IdP export + reviewer sign-off        | [FILL: Okta]     | Quarterly   | [FILL] | [FILL] |
| CC6.2   | Deprovisioning within 24h of offboarding   | Offboarding tickets w/ timestamps     | [FILL: HRIS+IdP] | Per event   | [FILL] | [FILL] |
| CC8.1   | All prod changes via reviewed PR + CI      | Branch protection config + PR sample  | [FILL: GitHub]   | Continuous  | [FILL] | [FILL] |
| CC7.1   | Monthly vuln scans, criticals fixed <15d   | Scan reports + remediation tickets    | [FILL: scanner]  | Monthly     | [FILL] | [FILL] |
| CC7.2   | Alerting on anomalous activity             | SIEM/CloudTrail config + alert sample | [FILL]           | Monthly     | [FILL] | [FILL] |
| A1.2    | Backups with tested restores               | Backup config + restore-test record   | [FILL]           | Quarterly   | [FILL] | [FILL] |
| CC7.3   | Incident response plan tested via tabletop | Plan doc + tabletop notes             | [FILL]           | Annual      | [FILL] | [FILL] |
```

## Deliverable

Produce a control-to-evidence map covering every in-scope criterion, with artifact, system of record, owner, cadence, and status per row, plus a gap list with remediation dates for any control not yet operating.

## Do NOT

- Do not collect evidence reactively before fieldwork — Type II samples the entire period.
- Do not fabricate or backdate artifacts; auditors test consistency and fabrication is itself a finding.
- Do not leave any control row ownerless — unowned controls are the ones that lapse mid-period.
- Do not hand auditors more than the control requires.
- Do not claim encryption or backups without configuration or restore-test proof.
- Do not skip the annual tabletop — an untested incident response plan is a standard finding.

## Quality bar

- Every in-scope criterion has at least one mapped control with artifact, system, owner, and cadence.
- Every cadence meets or beats the table above; every automatable export is scheduled, not manual.
- Known gaps are disclosed with remediation dates before the auditor finds them.
- The four classic engineering findings are pre-empted: 24-hour deprovisioning, documented change management, configuration proof for encryption, and a tested incident response plan.

## Escalation

This skill is not legal or compliance advice; the audit opinion belongs to a licensed CPA firm, and scoping questions with contractual consequences belong with counsel. Route incident writeups to postmortem-writer, vulnerability prioritization to vulnerability-triage, and secrets handling to secrets-hygiene.
