---
name: compensation-saga-runtime
description: Design and review compensation and Saga runtime behavior for agent workflows. Use when handling multi-step side effects, rollback plans, irreversible actions, compensation ordering, manual escalation, cancellation cleanup, or audit-linked recovery flows.
---

# Compensation Saga Runtime

## Skill Interface

- Name: compensation-saga-runtime.
- Description: Design and review compensation and Saga runtime behavior for multi-step side effects, rollback plans, irreversible actions, compensation ordering, manual escalation, cancellation cleanup, and audit-linked recovery flows.
- Parameters: Completed side-effecting steps, failure point, reversible and irreversible actions, dependency order, audit trail, idempotency keys, retry and timeout policy, manual escalation rules, and verification cases.
- Instructions: Use this skill when an agent workflow performs side effects across several steps. Build compensation from completed steps, run it in policy-defined order, record failures, mark irreversible actions, and test cancellation, duplicate compensation, timeout, and audit reconstruction.

Compensation defines recovery for completed side effects when a later step
fails or the user cancels.

## Core Types

```ts
type CompensationAction = {
  actionId: string;
  description: string;
  execute: () => Promise<CompensationResult>;
  isReversible: boolean;
};

type CompensationPlan = {
  taskId: string;
  completedSteps: string[];
  failurePoint: string;
  actions: CompensationAction[];
};
```

## Planning Rules

- Build compensation plans from completed side-effecting steps only.
- Never compensate steps that did not execute.
- Run compensation in reverse dependency order unless a domain policy says
  otherwise.
- Mark irreversible actions and escalate them for manual handling.
- Store compensation events in the same audit trail as the original operation.
- Pair compensation with idempotency so retries do not duplicate rollback work.

## Execution Rules

- A failed compensation must not be silently swallowed.
- Compensation failure should record state, reason code, and manual follow-up.
- Cancellation may require compensation when side effects already happened.
- Compensation should have its own timeout, retry policy, and idempotency key.
- The runtime should distinguish original failure from compensation failure.

## Common Scenarios

- A resource was reserved and the user cancelled: release the reservation.
- A status was changed and a later notification failed: decide whether to keep
  the status or restore it based on policy.
- An irreversible notification was sent: mark as irreversible and create a
  correction or manual follow-up.
- A downstream create succeeded but later enrichment failed: keep the created
  resource and retry enrichment if policy allows.

## Verification

Test reverse-order compensation, irreversible actions, compensation timeout,
compensation failure escalation, duplicate compensation requests, cancellation
after side effect, and audit trail reconstruction.
