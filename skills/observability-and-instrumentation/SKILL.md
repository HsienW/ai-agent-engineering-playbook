---
name: observability-and-instrumentation
description: Add or review observability. Use when adding logs, metrics, traces, audit events, health checks, alerts, or production diagnostics for features, background jobs, APIs, tools, and integrations.
---

# Observability and Instrumentation

Instrument the questions operators need to answer. Do not add noisy logs as a
substitute for clear signals.

## Process

1. Define what "working" means for the feature.
2. Identify the questions needed during an incident.
3. Choose the signal: log, metric, trace, audit event, health check, or alert.
4. Add correlation identifiers at request, job, tool, or workflow boundaries.
5. Capture success, failure, timeout, cancellation, retry, and degraded paths.
6. Redact sensitive values.
7. Verify signals appear in the expected local or staging sink.

## Signal Guidance

- Logs: explain discrete events and decisions.
- Metrics: track rates, latency, saturation, errors, and business counters.
- Traces: connect work across services, tools, providers, queues, and retries.
- Audit events: record security-sensitive actions with actor, target, and
  outcome.
- Health checks: prove dependency readiness without exposing internals.

## Redaction Rules

Never log credentials, tokens, passwords, authorization headers, private keys,
session identifiers, full request bodies, or personal data unless a policy
explicitly allows a redacted field.

## Alert Rules

- Alert on user impact, data risk, security risk, or exhausted capacity.
- Avoid alerts that require no action.
- Include runbook context when possible.

## Verification

Confirm:

- Expected success and failure signals are emitted.
- Identifiers allow one request or workflow to be followed.
- Sensitive data is not present.
- Metric names and labels are bounded and stable.
