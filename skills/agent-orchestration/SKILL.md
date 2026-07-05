---
name: Agent Orchestration
description: Designs reliable multi-agent LLM systems - choosing a topology, writing handoff contracts between agents, deciding what runs in parallel vs series, and setting context, retry, and cost budgets that stop runaway loops. Use when someone asks "should I split this into multiple agents", "my agents keep looping", "how do I pass context between agents", "orchestrator vs pipeline vs router", or is architecting an agent workflow. Do NOT use for tuning a single prompt - use prompt-engineer instead; do NOT use for measuring agent output quality - use llm-evaluation instead; for making a product agent-operable end to end, use build-on-agent-native.
---

# Agent Orchestration

Multi-agent systems fail two ways: teams split work across agents that a single capable agent handles better (paying latency, cost, and failure surface for nothing), or they wire agents together with vague handoffs and no budgets, producing loops that burn tokens until someone notices the bill. This skill designs the coordination layer - topology, contracts, budgets - so agent count is justified and every failure mode has a pre-decided response.

## Operating procedure

### Step 1: Gather inputs

- The end-to-end task and the acceptance criteria for its final output.
- Why one agent is insufficient - name the specific failure (context overflow, skill mismatch, needed parallelism). If the user cannot name one, the answer is one agent, and this design is over.
- Volume of context the task consumes vs the model's window; latency and cost ceilings per run.
- Which steps have side effects (writes, sends, purchases) vs are pure reads.

### Step 2: Justify each agent

Split only when at least one holds:

- The task decomposes into distinct skills (research, then write, then review) whose instructions conflict in one prompt.
- Working context would exceed roughly 60-70% of the window, so subtasks must be isolated and summarized (past that point, retrieval quality inside the window degrades and cost balloons).
- Independent subtasks can genuinely run in parallel.

Each added hop adds latency, cost, and a new failure surface. Default to one agent until it demonstrably can't cope.

### Step 3: Pick the topology

- Orchestrator-workers: a planner decomposes the task, routes subtasks to specialized workers, and synthesizes. Use when subtasks vary run-to-run.
- Sequential pipeline: fixed stages, each output feeds the next. Use when the stages never change - it is the cheapest and most debuggable shape.
- Router: classify the input, dispatch to one specialist. Use for heterogeneous inbound work, not decomposition.

Keep the control flow in deterministic code wherever possible; let the LLM fill in content, not decide the graph. An LLM-decided graph is uninspectable and untestable.

### Step 4: Parallelize or serialize

- Parallelize only subtasks with no data dependency and no shared side effects. Reads parallelize; writes serialize.
- If subtask B needs even a summary of A, serialize them - parallel-then-reconcile costs more than the latency it saves.
- Cap fan-out at what you can review: 3-5 parallel workers per wave is the practical ceiling before synthesis quality drops and the orchestrator's merge step becomes the new bottleneck.
- Anything with side effects runs serially with validation between steps.

### Step 5: Write the handoff contract

Every edge between agents gets an explicit contract, not prose. Minimum fields:

- `task`: one-sentence objective the worker can act on without the full history.
- `inputs`: the exact artifacts passed, each summarized to budget (below).
- `output_schema`: the structure the worker must return (fields, types, length caps).
- `done_when`: acceptance criteria the orchestrator will validate against.
- `budget`: max tokens/steps/time for this subtask.

Validate every worker output against `output_schema` before use. An unvalidated handoff is where garbage enters the system and compounds downstream.

### Step 6: Set context and cost budgets

- Pass minimal, relevant context to each worker - never the whole history. A worker's input should target well under half its window, leaving room to work.
- Summarize intermediate results before forwarding; cap summaries (a few hundred tokens is usually enough - if a worker needs more, pass a reference to the full artifact in a shared scratchpad/blackboard, not the artifact inline).
- Global run budgets, enforced by a circuit breaker: max total steps, max total tokens/cost, max wall-clock time. Pick numbers meaningfully above the p95 of a healthy run and halt hard at the ceiling.
- Retry a failed subtask at most 2-3 times, each retry carrying the validator's specific feedback; identical-input retries are wasted spend.
- Cap delegation depth at 2-3 and forbid re-dispatching a subtask already seen (track a task fingerprint) - this kills infinite delegation loops.

### Step 7: Design failure handling

- Add a critic/reviewer step for high-stakes outputs; skip it for cheap, low-risk ones.
- Conflicting worker outputs: the orchestrator arbitrates with a pre-decided tie-break rule (prefer the sourced answer, or escalate to the critic) - never silently picks the first.
- Partial failure: return best-effort results with an explicit note on what failed; a silent partial success is worse than an error.
- Make every tool idempotent; agents retry, and a non-idempotent write plus a retry equals a double charge.
- Log every agent call - inputs, outputs, tokens, cost, latency - keyed by run id. This is non-negotiable: it is the only way to debug and the raw material for evals (pair with llm-evaluation).

## Worked artifact: handoff contract

```json
{
  "task": "Summarize the security posture of the attached vendor questionnaire",
  "inputs": [
    { "name": "questionnaire", "ref": "blackboard://run-42/vendor-doc",
      "summary": "180-question SOC2-style vendor security questionnaire, completed" }
  ],
  "output_schema": {
    "risk_level": "one of: low | medium | high",
    "top_findings": "array, max 5, each { finding: string<=200chars, evidence: string }",
    "unanswered_questions": "array of question ids"
  },
  "done_when": "risk_level is set and every finding cites evidence from the document",
  "budget": { "max_tokens": 8000, "max_time_s": 120, "max_retries": 2 }
}
```

Bad contract, for contrast: "Worker 2: look at the doc and tell me if it's risky." No schema to validate, no budget to enforce, no acceptance criteria - the orchestrator cannot tell success from failure, so failure propagates.

## Deliverable

Produce an orchestration design doc containing: the agent justification (or the verdict "use one agent"), the chosen topology with a diagram of edges, one handoff contract per edge, the parallel/serial decision per subtask with reasoning, the global budget table (steps, tokens, time, retry caps, depth cap), and the failure playbook (validation, arbitration rule, partial-failure behavior).

## Do NOT

- Do not multiply agents for architecture's sake - each hop is latency, cost, and failure surface.
- Do not let an LLM own the control flow when code can - keep the graph inspectable.
- Do not pass full conversation history across a handoff; summarize or pass references.
- Do not retry without feedback, retry more than 2-3 times, or run without a circuit breaker.
- Do not skip output validation on any edge; one unvalidated handoff poisons everything downstream.
- Do not parallelize side-effectful steps.
- Do not ship without per-call logging - an unobservable agent system is undebuggable.

## Quality bar

- Every agent's existence is justified by a named single-agent failure.
- Every edge has a schema-validated contract with acceptance criteria and a budget.
- Global circuit breaker enforces step, token, and time ceilings; delegation depth capped; duplicate dispatch forbidden.
- The run log reconstructs any failure end-to-end from run id alone.
- A dry run of the failure playbook covers: worker timeout, invalid output, conflicting outputs, and budget exhaustion.
