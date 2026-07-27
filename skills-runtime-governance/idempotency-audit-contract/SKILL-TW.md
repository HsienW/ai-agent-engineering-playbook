---
name: idempotency-audit-contract
description: 設計與審查 agent runtimes 的 idempotency 與 audit contracts。用於防止 duplicate side effects、propagating idempotency keys、persisting operation history、redacting sensitive payloads、auditing tool decisions，或在 retries 與 resumes 後 reconstruct task execution。
---

# Idempotency and Audit Contract

## Skill 介面

- 名稱：idempotency-audit-contract。
- 描述：為 agent runtimes 設計與審查 idempotency 和 audit contracts，用於防止 duplicate side effects、propagate idempotency keys、persist operation history、redact sensitive payloads、audit tool decisions，並在 retries 與 resumes 後 reconstruct task execution。
- 參數：Side-effecting operation、idempotency key scope 與 version、record lifecycle、expiry policy、audit event fields、actor 與 resource identifiers、redaction policy、replay rules，以及 verification cases。
- 執行指令：當 workflow 會寫入 external systems 或需要 durable audit history 時使用此 skill。Side effects 前先 lock，對 repeated keys 回傳 recorded results，保留足夠 metadata 支援 replay decisions，redact sensitive payloads，並測試 duplicate、expired、failed、concurrent 與 resumed operations。

Side effects 在 retries、duplicate requests、worker crashes 與 resume flows 下都要安全。Audit 要能說明發生了什麼，且不能存 sensitive payloads。

## Idempotency Key

使用 scoped、versioned key：

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

- Side-effecting operations 必須有 idempotency。
- 執行 side effect 前先 lock。
- 對 repeated completed keys 回傳 recorded result。
- 只在 expiry 或 explicit invalidation 後允許 re-execution。
- Key 中包含 policy version，避免 unsafe cross-version replay。
- Persist 足夠 result metadata，支援 deterministic replay decisions。
- 將 idempotency 與 state transition guard 配對，處理 concurrent step updates。

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

不要 persist credentials、raw secrets、full prompts、full conversations、private uploaded content 或 unmasked personal data。需要時保留 structured summaries、hashes、opaque references、policy decisions、reason codes 與 redacted payload fragments。

## Verification

測試 repeated requests with the same key、expired keys、failed keys、concurrent key acquisition、audit reconstruction、redaction、side effects 缺少 idempotency keys，以及 process restart 後 resume。
