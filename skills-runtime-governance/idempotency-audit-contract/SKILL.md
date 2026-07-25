---
name: idempotency-audit-contract
description: Design and review idempotency and audit contracts for agent runtimes. Use when preventing duplicate side effects, propagating idempotency keys, persisting operation history, redacting sensitive payloads, auditing tool decisions, or reconstructing task execution after retries and resumes.
---

# Idempotency and Audit Contract

## Skill Interface

- Name: idempotency-audit-contract.
- Description: Design and review idempotency and audit contracts for agent runtimes that prevent duplicate side effects, propagate idempotency keys, persist operation history, redact sensitive payloads, audit tool decisions, and reconstruct task execution after retries and resumes.
- Parameters: Side-effecting operation, idempotency key scope and version, record lifecycle, expiry policy, audit event fields, actor and resource identifiers, redaction policy, replay rules, and verification cases.
- Instructions: Use this skill when a workflow writes to external systems or needs durable audit history. Lock before side effects, return recorded results for repeated keys, persist enough metadata for replay decisions, redact sensitive payloads, and test duplicate, expired, failed, concurrent, and resumed operations.

Side effects must be safe across retries, duplicate requests, worker crashes,
and resume flows. Audit must explain what happened without storing sensitive
payloads.

## Idempotency Key

Use a scoped, versioned key:

```ts
type IdempotencyKey = {
  namespace: string;
  resourceKey: string;
  version: string;
};

type IdempotencyRecord = {
  key: string;
  status: 'locked' | 'completed' | 'failed';
  result?: unknown;
  createdAt: string;
  expiresAt: string;
};
```

## Idempotency Rules

- Require idempotency for side-effecting operations.
- Lock before executing the side effect.
- Return the recorded result for repeated completed keys.
- Allow re-execution only after expiry or explicit invalidation.
- Include policy version in the key to avoid unsafe cross-version replay.
- Persist enough result metadata for deterministic replay decisions.
- Pair idempotency with a state transition guard for concurrent step updates.

## Audit Event

```ts
type AuditEvent = {
  eventId: string;
  taskId?: string;
  stepId?: string;
  operationId?: string;
  actorType: 'system' | 'user' | 'agent';
  actorId: string;
  action: string;
  resourceType: string;
  resourceId: string;
  decision: 'allow' | 'deny' | 'pending_confirmation';
  reasonCode?: string;
  beforeStateRef?: string;
  afterStateRef?: string;
  createdAt: string;
};
```

## Redaction Rules

Do not persist credentials, raw secrets, full prompts, full conversations,
private uploaded content, or unmasked personal data. Persist structured
summaries, hashes, opaque references, policy decisions, reason codes, and
redacted payload fragments when needed.

## Verification

Test repeated requests with the same key, expired keys, failed keys, concurrent
key acquisition, audit reconstruction, redaction, missing idempotency keys for
side effects, and resume after process restart.
