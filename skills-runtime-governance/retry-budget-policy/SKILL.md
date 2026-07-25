---
name: retry-budget-policy
description: Design and review retry budgets for agent steps, tools, providers, and transport calls. Use when classifying retryable errors, enforcing max attempts, respecting Retry-After, applying backoff and jitter, handling timeouts, or preventing retries for permission, validation, business, or user-cancelled failures.
---

# Retry Budget Policy

## Skill Interface

- Name: retry-budget-policy.
- Description: Design and review retry budgets for agent steps, tools, providers, and transport calls when classifying retryable errors, enforcing max attempts, respecting Retry-After, applying backoff and jitter, handling timeouts, or blocking retries for permission, validation, business, and user-cancelled failures.
- Parameters: Error taxonomy, retryable codes, max attempts, max elapsed time, backoff strategy, jitter policy, Retry-After handling, cancellation signal, persisted retry state, idempotency requirements, and verification cases.
- Instructions: Use this skill before adding retry behavior. Classify the failure first, retry only allowed classes, stop at budget limits, carry cancellation through waits and upstream calls, persist retry state when needed, and test retryable, non-retryable, exhausted, cancelled, and idempotent paths.

Retries are a budgeted recovery mechanism. Each policy needs an error taxonomy,
a budget, a backoff strategy, and a stop rule.

## Error Classification

Classify failures before retrying:

| Error class | Retry | Strategy |
| --- | --- | --- |
| Timeout | Yes | Exponential backoff with jitter |
| Rate limited | Yes | Respect server-provided retry hints |
| Temporary upstream failure | Yes | Limited retry budget |
| Structured output parse error | Conditional | One repair or retry attempt |
| Structured output validation error | Conditional | Repair with explicit hint |
| Permission denied | No | Escalate or terminate |
| Business rejected | No | Return explainable result |
| User cancelled | No | Stop and compensate if needed |
| Invalid input | No | Ask for correction or return validation error |

## Policy Shape

```ts
type RetryPolicy = {
  maxAttempts: number;
  maxElapsedMs: number;
  retryableCodes: string[];
  backoffStrategy: 'exponential' | 'fixed' | 'retry_after';
  jitter: boolean;
};
```

Track attempts, elapsed time, last error, and next retry time per step or
operation.

## Rules

- Retry only errors classified as retryable.
- Hard-stop when `maxAttempts` or `maxElapsedMs` is exceeded.
- Carry cancellation signals through sleeps and upstream calls.
- Do not retry permission, policy, business rejection, or user cancellation.
- Persist retry state when a retry can span process restarts.
- Emit structured events for retry scheduled, retry started, retry succeeded,
  and budget exhausted.
- Make retries idempotent before retrying side effects.

## Verification

Test timeout retry, rate-limit retry hints, temporary failure recovery,
non-retryable errors, budget exhaustion, cancellation during delay, jitter
boundaries, persisted retry state, and interaction with idempotency.
