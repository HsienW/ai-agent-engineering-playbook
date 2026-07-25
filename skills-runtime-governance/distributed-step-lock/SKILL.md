---
name: distributed-step-lock
description: Design and review distributed locks for agent step transitions. Use when preventing concurrent workers, duplicate resumes, retries, compensation, or external requests from mutating the same task or step state at the same time.
---

# Distributed Step Lock

## Skill Interface

- Name: distributed-step-lock.
- Description: Design and review distributed locks for agent step transitions when concurrent workers, duplicate resumes, retries, compensation, or external requests may mutate the same task or step state at the same time.
- Parameters: Step identifier, lock owner, TTL, transition guard, allowed state transition, lock store behavior, retry policy, failure behavior, event names, and concurrency verification cases.
- Instructions: Use this skill before changing shared task or step state from multiple runtimes. Acquire a lock, verify owner on release and extension, use compare-and-swap as a second guard, define TTL behavior, and test contention, expiry, owner mismatch, and lock store outage.

Idempotency prevents duplicate side effects for the same key. It does not by
itself prevent two workers from racing on the same step transition. Use a lock
plus a durable compare-and-swap guard.

## Lock Interface

```ts
type StepLock = {
  acquire(stepId: string, owner: string, ttlMs: number): Promise<boolean>;
  release(stepId: string, owner: string): Promise<void>;
  extend(stepId: string, owner: string, ttlMs: number): Promise<boolean>;
};

type StepTransitionGuard = {
  transition(
    stepId: string,
    from: StepStatus,
    to: StepStatus,
    owner: string
  ): Promise<TransitionResult>;
};
```

## Rules

- Acquire the lock before changing step state.
- Use owner identity when releasing or extending a lock.
- Use a TTL so crashed workers do not create permanent locks.
- Renew the lock for long-running steps.
- Reject release attempts from a different owner.
- Use a database compare-and-swap transition as a second line of defense.
- Emit structured events for lock acquired, contended, extended, released, and
  expired.

## Failure Behavior

- Lock unavailable: return a retryable concurrency result or wait according to
  policy.
- Lock expired during work: stop mutation or revalidate ownership before
  committing.
- Durable transition mismatch: do not overwrite; reload state and decide the
  next action.
- Lock store unavailable: degrade according to safety policy, usually by
  rejecting mutation rather than allowing unsafe concurrency.

## Verification

Test two concurrent workers, owner mismatch on release, TTL expiry after worker
crash, lock extension, transition compare-and-swap failure, lock store outage,
compensation racing with normal flow, and resume racing with retry.
