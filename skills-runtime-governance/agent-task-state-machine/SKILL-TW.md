---
name: agent-task-state-machine
description: 設計與審查 generic agent task 與 step state machines。用於新增 business-level task progress、step lifecycle tracking、task events、persistence、streaming timelines、resumable task state，或 cross-layer task status APIs。
---

# Agent Task State Machine

## Skill 介面

- 名稱：agent-task-state-machine。
- 描述：為 business-level task progress、step lifecycle tracking、task events、persistence、streaming timelines、resumable task state 與 cross-layer status APIs 設計與審查 generic agent task 和 step state machines。
- 參數：Task statuses、step statuses、legal transitions、event model、persistence requirements、retry 與 compensation rules、timeline consumers、redaction policy，以及 lifecycle verification cases。
- 執行指令：當 product 或 operator workflows 需要 graph checkpoints 之外的 task semantics 時使用此 skill。在單一 module 定義 legal transitions，保持 terminal states final，durable history 重要時保存 append-only events，並測試 lifecycle、retry、resume、cancellation 與 compensation paths。

將 business progress 與 graph execution state 分開建模。Graph 可以 checkpoint execution，但 product 與 operator workflows 常需要 task、step、audit 與 timeline semantics。

## Core Model

使用 generic names，讓 caller 注入 domain-specific step names：

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

- 在單一 owned module 定義 legal transitions。
- 用 stable error codes 拒絕 illegal transitions。
- Terminal states 不得轉回 running。
- Step completion 只能透過 explicit policy 推動 task completion。
- Durable history 重要時，先存 transition events，再通知 downstream listeners。
- 保留足夠資料，用來 resume 或解釋 interrupted task。

## Event Model

使用 append-only task events：

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

Events 應包含 identifiers、event type、payload 與 creation time。Payloads 要保持 structured、redacted，並適合 persistence。

## Verification

測試 complete lifecycle、illegal transitions、retryable 與 terminal failures、waiting and resume、cancellation、partial failure、compensation、event ordering，以及從 persisted events render timeline。
