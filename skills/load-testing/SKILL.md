---
name: Load Testing
description: Designs and interprets load tests - smoke, load, stress, soak, and spike - with realistic ramp profiles, percentile-based pass/fail thresholds, and a bottleneck-reading procedure, producing a runnable test plan. Use when someone asks "how many users can we handle", "how do I write a k6 or Gatling test", "will this survive the launch", "why did the site die at 500 users", or is preparing for a traffic event. Do NOT use for diagnosing frontend page-speed or Core Web Vitals - use web-performance instead - and do NOT use for tuning database connection pools found as the bottleneck - use connection-pool-tuner instead.
---

# Load Testing

The point of a load test is to find the capacity limit and the failure mode before users do, and to turn "will it hold?" into a number with a date on it. The costly mistake this skill prevents is the vanity test: hammering one cached endpoint from one laptop, reading the average latency, and declaring readiness - a test that passes while production dies at one-third the "tested" load.

## Operating procedure

### Step 1: Gather inputs

Collect before writing any script; label estimates as guesses.

1. Target load: expected peak in requests/sec or concurrent users, and where the number came from (analytics, marketing forecast, guess).
2. SLOs: the p95/p99 latency and error-rate targets that define pass/fail. If none exist, propose p95 < 500ms, p99 < 1500ms, error rate < 1% as defaults and get sign-off.
3. Top 3-5 user journeys with their real traffic mix (e.g. 80% browse, 15% search, 5% checkout) - from production access logs, not intuition.
4. Environment: production-like staging (same instance sizes, same data volume) or coordinated production test. Toy data lies: a 1,000-row table performs nothing like a 100M-row one.
5. The traffic event or deadline driving the test.

### Step 2: Pick the test types - each answers a different question

| Type | Question it answers | Profile |
|---|---|---|
| Smoke | Does the script and system work at all? | 1-5 VUs, 1-2 min. Run first, always. |
| Load | Do SLOs hold at expected peak? | Ramp to 100% of peak over 5-10 min, hold 10-30 min |
| Stress | Where is the breaking point and what breaks first? | Step up 25% every 5 min past peak until failure |
| Soak | Do leaks or degradation appear over time? | 60-80% of peak held 2-8 hours |
| Spike | Does autoscaling survive a surge? | 10% -> 150%+ of peak in under 1 min, then drop |

Minimum credible pre-launch battery: smoke, then load, then stress. Add soak if the service holds long-lived resources (connections, memory caches, file handles); add spike if traffic arrives in bursts (launches, notifications, TV moments).

### Step 3: Script realistic scenarios

1. Model journeys, not single-endpoint hammering - a user browses, pauses, searches, buys.
2. Use think time (1-5s between actions) and pacing; zero think time simulates a botnet, not customers.
3. Mix reads and writes per the production ratio.
4. Parameterize data so each virtual user sends distinct inputs - 1,000 VUs requesting the same id measures your cache, not your system.
5. Ramp gradually; a cold-start wall of traffic measures connection-establishment overhead, which is only the right measurement in a spike test.

### Step 4: Encode pass/fail thresholds in the script

Percentiles, never averages: the average hides the tail, and the tail is where users churn. p99 over average, always - a 100ms average with a 4s p99 means 1 in 100 requests is unacceptable, which at 1M requests/day is 10,000 bad experiences.

```js
import http from 'k6/http';
import { check, sleep } from 'k6';
export const options = {
  stages: [
    { duration: '5m', target: 100 },   // ramp to expected peak
    { duration: '15m', target: 100 },  // hold at peak
    { duration: '2m', target: 0 },     // ramp down
  ],
  thresholds: {
    http_req_duration: ['p(95)<500', 'p(99)<1500'],
    http_req_failed: ['rate<0.01'],
  },
};
export default function () {
  const res = http.get(`https://api.example.com/items?u=${__VU}-${__ITER}`);
  check(res, { 'status 200': (r) => r.status === 200 });
  sleep(Math.random() * 3 + 1); // 1-4s think time
}
```

### Step 5: Run, watching both sides

- Monitor the load generator too - if generator CPU exceeds ~70% or it hits its own socket limits, the "latency" you measure is the generator's, not the system's. Distribute generators when the target exceeds what one machine honestly produces (rule of thumb: a few thousand RPS per generator node, less with TLS and large payloads).
- Allowlist generator IPs through rate limits and WAF, or you are load-testing the WAF.
- Never load-test production without coordination, a kill switch on the test, and an agreed abort threshold.

### Step 6: Interpret

1. Find the knee: plot latency vs load; the point where p95 bends upward is the saturation point. Your safe capacity is 70-80% of the knee, not the knee itself.
2. Correlate with server metrics - CPU, memory, DB connections, GC pauses, queue depths - at the timestamp of the knee to name the bottleneck. Connection-pool exhaustion typically appears before CPU saturation; check pool wait time first.
3. In soak runs, look for monotonic drift: memory climbing, latency creeping 1%/hour - both are leaks that a 10-minute test can never see.
4. Distinguish system errors from generator errors before reporting anything.
5. Record the baseline; every subsequent run compares against it, which is how regressions get caught before launch.

## Worked artifact: test plan skeleton

```
LOAD TEST PLAN - [FILL: service] - owner: [FILL]
Driving event: [FILL: launch/campaign/capacity review], date [FILL]

TARGETS
  Expected peak:      [FILL] req/s ([FILL: source of number])
  SLO pass/fail:      p95 < [FILL]ms, p99 < [FILL]ms, errors < [FILL]%

SCENARIOS (mix from production logs dated [FILL])
  [FILL]% - [FILL: journey, e.g. browse -> product -> cart]
  [FILL]% - [FILL]
  Think time: [FILL: 1-4s random]   Data: [FILL: parameterization source]

RUNS (in order)
  1. Smoke   - 5 VU, 2 min - script sanity
  2. Load    - ramp 5m to [FILL] VU, hold 15m - SLO verdict
  3. Stress  - +25% steps every 5m until first threshold breach - find the knee
  4. Soak    - [FILL]% of peak for [FILL]h - leak check   [delete if N/A]
  5. Spike   - 10% -> [FILL]% in 60s - autoscaling check  [delete if N/A]

ENVIRONMENT: [FILL: staging spec vs prod]   Data volume: [FILL rows/GB]
GENERATORS:  [FILL: count/location]         Allowlisted: yes/no
ABORT IF:    [FILL: e.g. prod error rate > 2% during coordinated test]

RESULTS
  Knee found at:      ____ req/s    Safe capacity (80%): ____ req/s
  Bottleneck:         ____ (evidence: ____)
  Verdict vs SLO:     PASS / FAIL   Baseline stored at: ____
```

## Deliverable

Produce the filled test plan, the runnable script with thresholds encoded, and a results section stating: the knee, the safe capacity at 80% of knee, the named bottleneck with evidence, and a pass/fail verdict against the SLOs.

## Do NOT

- Do not report averages - a passing average with a failing p99 is a failing test.
- Do not hammer one endpoint with identical parameters; you are benchmarking your cache.
- Do not trust results from a generator running hot; its own saturation reads as target latency.
- Do not test with toy data volume; query plans change shape with table size.
- Do not skip the ramp - except in a spike test, instant full load measures the wrong thing.
- Do not declare capacity at the knee itself; run production at 70-80% of it.
- Do not soak-test for 10 minutes and call it a soak; leaks need hours to show.

## Quality bar

- Every run has numeric pass/fail thresholds set before it starts.
- Traffic mix and data are traceable to production logs, not invented.
- The bottleneck is named with correlated server-side evidence, not guessed.
- A baseline is stored and the next run compares against it.
- The verdict states safe capacity as a number with the 20-30% headroom applied.

## Escalation

When the bottleneck turns out to be connection acquisition, hand to connection-pool-tuner; slow queries go to sql-query-optimizer or explain-plan-reader; frontend delivery problems go to web-performance. If the fix is caching, design it with caching-strategy rather than re-testing blindly.
