---
name: agent-task-state-machine
description: Design and review generic agent task and step state machines. Use when adding business-level task progress, step lifecycle tracking, task events, persistence, streaming timelines, resumable task state, or cross-layer task status APIs.
---

# Agent Task State Machine

## Skill Interface

- Name: agent-task-state-machine.
- Description: Design and review generic agent task and step state machines for business-level task progress, step lifecycle tracking, task events, persistence, streaming timelines, resumable task state, and cross-layer status APIs.
- Parameters: Task statuses, step statuses, legal transitions, event model, persistence requirements, retry and compensation rules, timeline consumers, redaction policy, and lifecycle verification cases.
- Instructions: Use this skill when product or operator workflows need task semantics beyond graph checkpoints. Define legal transitions in one module, keep terminal states final, persist append-only events when history matters, and test lifecycle, retry, resume, cancellation, and compensation paths.

Model business progress separately from graph execution state. A graph may
checkpoint execution, but product and operator workflows often need task,
step, audit, and timeline semantics.

## Core Model

Use generic names and inject domain-specific step names from the caller:

```ts
type TaskStatus =
  | 'created'
  | 'running'
  | 'waiting_confirmation'
  | 'completed'
  | 'partially_failed'
  | 'compensating'
  | 'failed'
  | 'cancelled';

type StepStatus =
  | 'pending'
  | 'running'
  | 'waiting_confirmation'
  | 'succeeded'
  | 'retryable_failed'
  | 'terminal_failed'
  | 'compensating'
  | 'compensated'
  | 'skipped';

type AgentTask<TStep extends string = string> = {
  taskId: string;
  taskType: string;
  status: TaskStatus;
  steps: AgentStep<TStep>[];
  createdAt: string;
  updatedAt: string;
  metadata?: Record<string, unknown>;
};

type AgentStep<TStep extends string = string> = {
  stepId: string;
  stepName: TStep;
  status: StepStatus;
  attempt: number;
  maxAttempts: number;
  input?: unknown;
  output?: unknown;
  error?: StepError;
  startedAt?: string;
  completedAt?: string;
};
```

## Transition Rules

- Define legal transitions in one owned module.
- Reject illegal transitions with stable error codes.
- Terminal states must not transition back to running.
- Step completion should drive task completion only through an explicit policy.
- Store transition events before notifying downstream listeners when durable
  history matters.
- Preserve enough data to resume or explain an interrupted task.

## Event Model

Use append-only task events:

- `task_created`
- `step_started`
- `step_completed`
- `step_failed`
- `step_retrying`
- `waiting_confirmation`
- `resumed`
- `task_completed`
- `task_failed`
- `compensation_triggered`
- `compensation_completed`

Events should include identifiers, event type, payload, and creation time. Keep
payloads structured, redacted, and safe for persistence.

## Verification

Test complete lifecycle, illegal transitions, retryable and terminal failures,
waiting and resume, cancellation, partial failure, compensation, event ordering,
and timeline rendering from persisted events.
