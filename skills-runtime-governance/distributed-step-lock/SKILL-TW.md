---
name: distributed-step-lock
description: 設計與審查 agent step transitions 的 distributed locks。用於防止 concurrent workers、duplicate resumes、retries、compensation，或 external requests 同時 mutate 同一個 task 或 step state。
---

# Distributed Step Lock

## Skill 介面

- 名稱：distributed-step-lock。
- 描述：當 concurrent workers、duplicate resumes、retries、compensation 或 external requests 可能同時 mutate 同一個 task 或 step state 時，設計與審查 agent step transitions 的 distributed locks。
- 參數：Step identifier、lock owner、TTL、transition guard、allowed state transition、lock store behavior、retry policy、failure behavior、event names，以及 concurrency verification cases。
- 執行指令：在多個 runtimes 可能變更 shared task 或 step state 前使用此 skill。Acquire lock，release 與 extension 時驗證 owner，使用 compare-and-swap 作為第二道 guard，定義 TTL behavior，並測試 contention、expiry、owner mismatch 與 lock store outage。

Idempotency 能防止同一 key 的 duplicate side effects。它無法單獨防止兩個 workers 同時競爭同一個 step transition。搭配 lock 與 durable compare-and-swap guard。

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

- 改變 step state 前先 acquire lock。
- Release 或 extend lock 時使用 owner identity。
- 使用 TTL，避免 crashed workers 造成 permanent locks。
- Long-running steps 要 renew lock。
- 拒絕不同 owner 的 release attempts。
- 使用 database compare-and-swap transition 作為第二道防線。
- Emit structured events：lock acquired、contended、extended、released 與 expired。

## Failure Behavior

- Lock unavailable：回傳 retryable concurrency result，或依 policy wait。
- Lock expired during work：停止 mutation，或 commit 前重新驗證 ownership。
- Durable transition mismatch：不要 overwrite；reload state 後決定 next action。
- Lock store unavailable：依 safety policy degrade，通常拒絕 mutation，而不是允許 unsafe concurrency。

## Verification

測試 two concurrent workers、owner mismatch on release、TTL expiry after worker crash、lock extension、transition compare-and-swap failure、lock store outage、compensation racing with normal flow，以及 resume racing with retry。
