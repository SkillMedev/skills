---
name: Observability Stack
description: Instruments systems with the three pillars - structured logs, RED/USE metrics, and distributed traces - correlated by one trace ID, with cardinality budgets and symptom-based alerts. Use when someone asks "how should I instrument this service", "what metrics should I emit", "why is my metrics bill exploding", "set up OpenTelemetry for my stack", or "we can't tell which service is slow". Do NOT use for tuning an existing noisy pager or alert thresholds - use alert-tuning instead; for defining SLO error-budget policy, use error-budget-policy; for the incident process when alerts fire, use sev-triage and postmortem-writer.
---

# Observability Stack

Instrumentation exists to answer one question during an incident: what is wrong and why, from telemetry alone, without SSH-ing into boxes. The two expensive failure modes are opposite: under-instrumented systems where an outage is archaeology, and over-instrumented ones where a single high-cardinality label multiplies the metrics bill by 10,000x. This skill installs the three pillars with correlation between them and explicit cardinality budgets.

## The three pillars and what each is for

- Logs: discrete events with context - for debugging a specific request. Expensive per byte; highest detail.
- Metrics: pre-aggregated numbers over time - for dashboards and alerts. Cheap to query; no per-request detail.
- Traces: the path of one request across services - for locating which hop added the latency or threw the error.

The pillars are useless in isolation; the connective tissue is one correlation ID. A `trace_id` in every log line and exemplars on metrics let you pivot: alert fires (metric) → exemplar trace shows the slow hop (trace) → logs for that trace_id show the error (log). Instrument on OpenTelemetry so all three stay vendor-neutral.

## Operating procedure

### Step 1: Gather inputs

1. Service inventory and the 2-3 critical user paths (checkout, login, search). These get instrumented first; everything else waits.
2. Current pain - "we don't know when we're down" (start with metrics/alerts) vs "we know but can't find where" (start with traces).
3. Backend and budget - the metrics bill is a function of series count, so the cardinality budget in Step 3 is a real cost decision.
4. Traffic volume, which sets the trace sampling rate in Step 4.

### Step 2: Structured logs

```json
{ "timestamp": "...", "level": "error", "service": "payments", "trace_id": "abc", "span_id": "def", "msg": "payment failed", "user_id": 42, "amount_cents": 999 }
```

- Always JSON, never free text. Mandatory fields on every line: timestamp, level, service, trace_id, span_id.
- Log at request/operation boundaries with outcome and duration; never inside hot loops - one log line per item in a 10k-item batch is 10k lines of noise and real CPU cost.
- Never log secrets, tokens, or PII. Add a scrub step at the logging library level, not by convention (pair with pii-scrubber for the scrub rules).

### Step 3: Metrics with a cardinality budget

- Services get the RED method: Rate, Errors, Duration - per endpoint.
- Resources (hosts, queues, pools, disks) get the USE method: Utilization, Saturation, Errors.
- Types: histograms for latency (enables p50/p95/p99), counters for events, gauges for levels. Averages hide tail pain; alert on p99, not mean.
- Cardinality is the cost model: total series = metric count x the product of each label's distinct values. One histogram with labels `endpoint` (50) x `status` (5) x `region` (4) = 1,000 series - fine. Add `user_id` (1M values) and it becomes 10^9 series - that is the entire bill and possibly the backend.
- Hard rule: labels must be bounded, low-cardinality sets known in advance (endpoint, status class, region). Unbounded values - user_id, request_id, email, raw URL with IDs - never become labels; they belong in logs and trace attributes, which are built for high cardinality.
- Budget: keep a service's total series under ~10k unless deliberately justified; review the top series-count offenders monthly.

### Step 4: Traces

- Propagate W3C `traceparent` across every service hop AND every async boundary - queue messages carry trace context in message metadata, or the trace dies at the first queue.
- Span each meaningful operation; attach attributes like `db.statement` and `http.status_code`.
- Sampling: head-sample a fixed percentage for volume control (1-10% is typical at high traffic; 100% below ~10 req/s where storage is trivial), and tail-sample to keep 100% of errors and slow outliers. Errors you sampled away are errors you cannot debug.

### Step 5: Alerts

- Alert on symptoms users feel - error rate, latency SLO burn - not on causes like CPU. High CPU with met SLOs is a fact, not an incident.
- Page only on urgent-AND-actionable; everything else is a ticket or a dashboard.
- Every page carries a runbook link. A page without a next action just wakes someone up to watch.

Bad alert:

```yaml
alert: HighCPU
expr: node_cpu_utilization > 0.80
for: 1m        # pages at 3am; user impact unknown; no action implied
```

Good alert:

```yaml
alert: CheckoutErrorBudgetBurn
expr: (sum(rate(http_requests_total{path="/checkout",code=~"5.."}[5m]))
      / sum(rate(http_requests_total{path="/checkout"}[5m]))) > 0.02
for: 5m
annotations:
  runbook: https://runbooks.internal/checkout-errors
  summary: ">2% of checkouts failing for 5m - users cannot pay"
```

The good alert names user impact, has a threshold tied to an SLO, waits long enough to skip blips, and tells the responder where to start.

### Step 6: Dashboards

One dashboard per service showing its RED metrics, plus one per critical user path. Kill dashboards nobody has opened in a quarter - dashboard sprawl is where signal goes to die.

## Deliverable

Produce an instrumentation plan containing: the mandatory log schema; the RED/USE metric list per service with each metric's labels and computed series count against the ~10k budget; the trace propagation map including every async boundary; the sampling policy (head % + tail rules); and the alert rules, each with symptom, threshold, `for` duration, and runbook link.

## Do NOT

- Do not put unbounded values (user_id, request_id, raw URLs) in metric labels - this is the single most expensive mistake in observability.
- Do not instrument everything at once; critical paths first, or you drown signal in noise before proving value.
- Do not alert on causes (CPU, memory) when you can alert on symptoms (errors, latency).
- Do not sample away errors - tail-sampling must keep them all.
- Do not log free text or drop trace_id from log lines - uncorrelated pillars are three separate silos, not observability.
- Do not page on anything without a runbook link.

## Quality bar

- Every log line carries trace_id; a pivot from alert → trace → logs works end to end in a live test.
- Every metric label enumerated with its cardinality; total series within budget.
- Trace context survives every queue and async hop in the propagation map.
- Every alert is symptom-based, threshold-justified, and runbook-linked.
- An on-call engineer unfamiliar with the service can answer "what is wrong and why" from telemetry alone.

## Escalation

Once instrumented, hand pager noise and threshold refinement to alert-tuning, budget policy to error-budget-policy, and live incident handling to sev-triage. Cardinality problems at the collector (dropping or aggregating offending labels) are a stopgap - fix the label at the source.
