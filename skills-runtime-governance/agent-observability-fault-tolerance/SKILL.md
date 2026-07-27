---
name: agent-observability-fault-tolerance
description: Design and review agent runtime observability and model/provider fault tolerance. Use when adding metrics, traces, cost tracking, audit alignment, structured runtime events, model fallback, structured output repair, refusal handling, provider timeouts, or local execution tree diagnostics.
---

# Agent Observability and Fault Tolerance

## Skill Interface

- Name: agent-observability-fault-tolerance.
- Description: Design and review agent runtime observability and model or provider fault tolerance for metrics, traces, cost tracking, audit alignment, runtime events, fallback, repair, refusal handling, timeouts, and execution tree diagnostics.
- Parameters: Task and step lifecycle, model and tool calls, retry and fallback policy, trace identifiers, metric names, cost fields, redaction policy, terminal states, and verification scenarios.
- Instructions: Use this skill when runtime behavior must be observable across success, retry, fallback, refusal, timeout, and terminal paths. Add bounded metrics, preserve trace continuity, classify failures, redact sensitive data, and test telemetry for each recovery path.

An agent runtime should make work visible across task, step, model, tool,
retry, and terminal paths. Fault tolerance should be explicit and observable.

## Signals

Capture metrics and traces across:

- Task lifecycle.
- Step lifecycle.
- Tool calls.
- Model calls.
- Retry attempts.
- Structured output repair.
- Fallback provider selection.
- Human approval or refusal.
- Compensation.
- Terminal state.

## Metrics

Track:

- Task success rate.
- Task completion latency.
- Step failure rate.
- Retry recovery rate.
- Resume success rate.
- Idempotency hit rate.
- Duplicate side-effect prevention rate.
- Tool success, timeout, and latency.
- Permission denial rate.
- Compensation success rate.
- Model fallback rate.
- Structured output repair success rate.
- Cost per successful task.

## Trace Shape

Use a trace hierarchy that follows the actual execution chain:

```text
request
  agent task
    graph node
      model call
      tool call
      retry attempt
      repair attempt
```

Include stable identifiers when available: request id, task id, step id, run
id, thread id, graph id, node name, tool call id, provider, model category,
duration, retry count, terminal status, and error code.

## Fault Tolerance

- Provider temporary failure: retry within budget, then fallback if policy
  allows.
- Provider timeout: retry with backoff, then fallback or return timeout result.
- Structured output parse error: attempt bounded repair.
- Structured output validation error: repair with a precise validation hint.
- Refusal or policy denial: do not retry blindly; return a structured refusal
  signal.
- Tool timeout or upstream failure: classify and apply retry policy.

## Safety Rules

- Do not log credentials, raw authorization headers, private payloads, full
  prompts, full conversations, or unredacted personal data.
- Do not hide fallback, repair, retry, or degraded execution from telemetry.
- Do not conflate provider failure, validation failure, refusal, and timeout
  into one generic error.

## Verification

Test provider outage, timeout, structured parse failure, validation failure,
refusal, retry exhaustion, fallback success, cost accounting, trace continuity,
metric emission, and redaction.
