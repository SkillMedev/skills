---
name: Alert Tuning
description: Reduces alert noise by making alerts actionable, symptom-based, and tied to SLOs. Identifies and kills flappy, duplicate, and cause-based alerts. Use when alert fatigue is degrading on-call response quality.
---

# Alert Tuning

Every alert that fires without requiring human action trains engineers to ignore alerts. Alert fatigue kills real incidents. The goal: every page wakes someone up for a reason they cannot automate away.

## The Audit — Run First

Before writing new alert rules, audit what you have.

1. Pull the last 30 days of alert history. Calculate: total pages, pages outside business hours, pages that resulted in no action taken.
2. Identify flappy alerts: any alert that fires and resolves more than 3 times in a 24-hour window without a corresponding incident.
3. Identify cause-based alerts: anything that fires on a system-internal metric (queue depth, pod restart count, CPU) without a direct user-impact correlation.
4. Calculate the actionability rate: (pages with a documented remediation / total pages). Below 80% means your alert set is broken.

## Symptom vs Cause

Page on symptoms, not causes. Symptoms are what users experience. Causes are what engineers investigate.

- Wrong: alert on CPU above 80%.
- Right: alert on p99 latency above SLO threshold.
- Wrong: alert on pod restart count above 2.
- Right: alert on error rate above 1% for 5 minutes.

For every cause-based alert, ask: 'Would a user notice if this fired and nobody acted?' If no, delete it. If yes, rewrite it as the symptom the user would notice.

## SLO-Based Alerting

Tie alerts to error budget burn rate, not raw thresholds.

- Fast burn: burn rate above 14x for 5 minutes (consumes 1 hour of 30-day budget). Page immediately.
- Slow burn: burn rate above 1x for 1 hour. Ticket or warn.
- No burn: no page, regardless of cause-based signals.

This approach eliminates most threshold-tuning debates and makes alert severity self-documenting.

## Fixing Flappy Alerts

- Add a 'for' duration to the alert rule. A threshold must be sustained for at least 2-5 minutes before paging.
- Add hysteresis: alert fires at 95% threshold, resolves only when it drops below 85%.
- For bursty metrics, use a rate or average over a longer window (10-15 minutes) instead of instantaneous value.

## Routing and Severity

- Route by SLO impact, not by which team owns the metric. The team that can fix it fastest gets the page.
- SEV1/2-level alerts: immediate page.
- SEV3-level alerts: ticket, notify via Slack, no wake-up.
- Never route the same alert to more than one person simultaneously. Shared ownership is no ownership.

## Do / Don't

- Do: delete alerts you cannot improve rather than leaving them muted. A muted alert is a lie.
- Do: review alert history quarterly. Thresholds drift as systems scale.
- Don't: add a new alert without deleting or merging one. Alert sets grow monotonically unless you enforce a budget.
- Don't: alert on absence of data without accounting for scrape intervals. Missing data is not always an outage.
