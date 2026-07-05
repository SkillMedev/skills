---
name: Error Budget Policy
description: Defines and applies an SLO error-budget policy that gates feature work versus reliability work based on remaining budget. Use when setting up SLOs or when high burn rate lacks a formal team response protocol.
---

# Error Budget Policy

An error budget without a policy is a number nobody acts on. The policy converts budget state into team behavior automatically, without requiring a manager decision each time.

## Define the Budget

Error budget = (1 - SLO target) x window duration.

Example: 99.9% SLO over 30 days = 0.1% x 30 days x 24 hours = 43.2 minutes of allowed downtime.

Track budget as a percentage remaining, not as raw minutes. 'Budget: 34% remaining' is actionable. 'Budget: 14.7 minutes remaining' is not.

## Four Policy States

Define exactly four states and the automatic team behavior each one triggers.

### Green (budget above 50%)

Feature work proceeds at full velocity. No reliability-only sprints required. On-call improvements are encouraged but not mandatory. Treat this state as the reward for good reliability work.

### Yellow (budget between 20% and 50%)

Feature work continues but every sprint must include at least one reliability or observability ticket. The team reviews burn rate trend weekly. No new non-critical migrations or risky deploys without IC sign-off.

### Orange (budget between 0% and 20%)

Feature work is paused for engineers on the service. The team shifts to reliability work: fixing root causes of recent incidents, improving alert coverage, reducing toil. No new features ship until budget is back above 20%. Exception: security patches and compliance work.

### Red (budget exhausted or SLO breach active)

Freeze all feature deploys immediately. Escalate to engineering lead and product manager. Convene a reliability review within 48 hours to decide: (a) improve reliability, or (b) formally lower the SLO to match actual capability. Do not silently operate below SLO.

## Budget Consumption Rules

- Planned maintenance counts against the budget unless users were notified and no user-impacting errors occurred.
- Incidents caused by a dependency consume budget. Track them separately and use the data in vendor reviews.
- Budget does not roll over month to month. Surplus in January does not excuse a breach in February.

## Burn-Rate Alerting

State colors describe where the budget is; burn-rate alerts catch how fast it is moving. Alert on multiples of the sustainable burn rate (1x = exactly exhausting the budget over the window). The standard multiwindow setup for a 30-day window:

- Page: 14.4x burn over 1 hour (2% of the monthly budget gone in an hour).
- Page: 6x burn over 6 hours (5% of the budget gone in six hours).
- Ticket: 1x burn over 3 days (10% of the budget consumed at a slow, steady leak).

Pair each long window with a short window (typically 1/12 the duration) so alerts stop firing once the burn stops. Without burn-rate alerts, a fast outage can eat the entire budget before the weekly state review ever sees it.

## Governance

- Publish the current budget state in the team's Slack channel weekly. One line: service name, SLO, budget remaining, state color.
- Review SLO targets annually. An SLO that is always green without effort is set too low.
- Product and engineering agree on the policy in writing before it is enforced. Policy surprises cause more damage than missed SLOs.

## Deliverable

Produce a written error budget policy document containing: the SLO and window per service tier, the budget math shown (target, window, allowed bad minutes or bad events), the four states with their numeric boundaries and the automatic team behavior each triggers, the burn-rate alert thresholds and where they page versus ticket, the budget consumption rules (maintenance, dependencies, no rollover), and the sign-off line where engineering and product both agree before enforcement begins. The document should let a new on-call engineer determine the current state and the required behavior without asking anyone.

## Quality bar

- Every state boundary is a number, not an adjective — "below 20%" not "getting low."
- Each state names an automatic behavior a team can execute without a manager's decision.
- Burn-rate alerts exist for fast, medium, and slow burns, each routed to page or ticket.
- The policy is signed by both engineering and product before it gates anything.
- The state calculation is automated and published on a fixed cadence.

## Do / Don't

- Do: automate the state calculation and publish it. Manual tracking fails within two months.
- Do: tie the policy to a real window (rolling 30 days is standard). Calendar month windows create perverse incentives at month-end.
- Don't: let teams renegotiate the policy during an Orange or Red state. That is when pressure to bend it is highest and discipline matters most.
- Don't: apply the same SLO to every endpoint. Tier your services: critical, standard, best-effort. Different tiers, different policies.
