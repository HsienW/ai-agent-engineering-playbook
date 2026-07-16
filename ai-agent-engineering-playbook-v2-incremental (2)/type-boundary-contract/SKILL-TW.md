---
name: type-boundary-contract
description: 設計與審查跨 package、service、frontend、backend、tool、event 與 transport boundaries 的 type contracts。用於定義 schemas、discriminated unions、runtime parsers、error envelopes、schemaVersion fields，或 duplicated transport/domain types。
---

# Type Boundary Contract

## Skill 介面

- 名稱：type-boundary-contract。
- 描述：為跨 package、service、frontend、backend、tool、event 與 transport boundaries 的 type contracts 設計與審查，涵蓋 schemas、discriminated unions、runtime parsers、error envelopes、schemaVersion fields，以及 duplicated transport 或 domain types。
- 參數：Boundary owner、producer 與 consumer types、domain 與 transport shapes、runtime parser、schema version、unknown-field behavior、error envelope、compatibility expectations，以及 contract test scenarios。
- 執行指令：在定義或變更 cross-boundary types 前使用此 skill。將 boundary types 視為 runtime contracts，分離 domain 與 transport types，在接收端驗證 external data，保留 compatibility，並測試 valid、invalid、unknown 與 future-version payloads。

Boundary 上的 types 是 contracts。Compile-time TypeScript types 不會驗證來自 clients、servers、tools、providers、files、queues 或 models 的 runtime data。

## Boundary Process

1. 識別 boundary owner。
2. 分離 domain type 與 transport type。
3. 在 receiving boundary 定義 runtime validation。
4. 定義 versioning 與 unknown-field behavior。
5. 定義 error envelope shape。
6. 為 valid 與 invalid data 加入 contract tests。
7. 變更既有 fields 前，先記錄 compatibility expectations。

## Type Rules

- 對 state、event 與 result variants 使用 discriminated unions。
- External data 在完成 parse 前使用 `unknown`。
- 避免在 boundaries 使用 `any`。
- 不要用 type assertions 隱藏 unvalidated data。
- 當 union variant 能更好地 model required state 時，避免 optional fields。
- 將 stable machine identifiers 與 localized messages 分開。
- 對 structured cross-boundary payloads 使用 explicit schema versions。

## Generic Result Pattern

```ts
type BoundaryResult<TKind extends string, TStatus extends string, TData> = {
  schemaVersion: string;
  kind: TKind;
  status: TStatus;
  data?: TData;
  summary?: string;
};
```

Producer 可以使用 narrow literal types。Consumer 若必須容忍 future versions，可以接受較寬的 compatible types。

## Error Envelope

使用 stable machine-readable fields：

```ts
type ErrorEnvelope = {
  error: {
    source: string;
    stage: string;
    code: string;
    message: string;
    retryable?: boolean;
    details?: Record<string, unknown>;
  };
};
```

不要暴露 stack traces、private paths、tokens、secrets 或 raw provider responses。

## Compatibility Checklist

- Request schema。
- Response schema。
- Event schema。
- Tool input schema。
- Tool output schema。
- Error code 與 retryability。
- Terminal state。
- Correlation identifiers。
- Unknown fields 與 unknown variants。
- Migration 或 deprecation plan。

## Verification

測試 valid data、missing fields、unknown fields、unknown variants、invalid schema versions、error envelopes，以及 future-compatible payloads。
