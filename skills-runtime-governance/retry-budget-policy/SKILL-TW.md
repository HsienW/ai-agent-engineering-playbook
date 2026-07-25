---
name: retry-budget-policy
description: 設計與審查 agent steps、tools、providers 與 transport calls 的 retry budgets。用於分類 retryable errors、enforcing max attempts、respecting Retry-After、applying backoff and jitter、handling timeouts，或避免對 permission、validation、business、user-cancelled failures retry。
---

# Retry Budget Policy

## Skill 介面

- 名稱：retry-budget-policy。
- 描述：為 agent steps、tools、providers 與 transport calls 設計與審查 retry budgets，處理 retryable errors 分類、max attempts、Retry-After、backoff and jitter、timeouts，以及 permission、validation、business 與 user-cancelled failures 的 retry 阻擋。
- 參數：Error taxonomy、retryable codes、max attempts、max elapsed time、backoff strategy、jitter policy、Retry-After handling、cancellation signal、persisted retry state、idempotency requirements，以及 verification cases。
- 執行指令：新增 retry behavior 前使用此 skill。先分類 failure，只 retry 允許的類別，達到 budget limits 時停止，讓 cancellation 穿過 waits 與 upstream calls，需要時 persist retry state，並測試 retryable、non-retryable、exhausted、cancelled 與 idempotent paths。

Retries 是有 budget 的 recovery mechanism。每個 policy 都需要 error taxonomy、budget、backoff strategy 與 stop rule。

## Error Classification

Retry 前先分類 failures：

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

依 step 或 operation 追蹤 attempts、elapsed time、last error 與 next retry time。

## Rules

- 只 retry classified as retryable 的 errors。
- `maxAttempts` 或 `maxElapsedMs` 超過後 hard-stop。
- 將 cancellation signals 傳過 sleeps 與 upstream calls。
- 不要 retry permission、policy、business rejection 或 user cancellation。
- Retry 可能跨 process restarts 時，persist retry state。
- Emit structured events：retry scheduled、retry started、retry succeeded 與 budget exhausted。
- Retry side effects 前先確保 idempotent。

## Verification

測試 timeout retry、rate-limit retry hints、temporary failure recovery、non-retryable errors、budget exhaustion、cancellation during delay、jitter boundaries、persisted retry state，以及與 idempotency 的互動。
