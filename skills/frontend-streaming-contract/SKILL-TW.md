---
name: frontend-streaming-contract
description: 設計與審查 frontend streaming contracts。用於處理 server-sent events、WebSocket messages、async iterables、stream parsers、reducer state、terminal states、progress timelines、cancellation、retries，或由 runtime events 驅動的 UI updates。
---

# Frontend Streaming Contract

## Skill 介面

- 名稱：frontend-streaming-contract。
- 描述：為 server-sent events、WebSocket messages、async iterables、stream parsers、reducer state、terminal states、progress timelines、cancellation、retries 與由 runtime events 驅動的 UI updates 設計與審查 frontend streaming contracts。
- 參數：Stream transport、runtime event schema、parser behavior、reducer state shape、ordering 與 duplicate rules、terminal states、cancellation 與 retry behavior、UI rendering states，以及 verification scenarios。
- 執行指令：當 frontend state 或 UI 由 streamed runtime events 驅動時使用此 skill。將每個 event 視為 versioned contract，驗證 unknown payloads，保持 terminal states final，讓 reducers 能承受 future events，並測試 interruption、ordering、cancellation 與 timeout cases。

將 streaming 視為 contract，而不是任意 append 到 UI 的文字。每個 event 都應具備 stable type、runtime validation、ordering behavior，以及對 state 的明確 effect。

## Contract Shape

為 runtime events 定義 discriminated union：

```ts
type RuntimeEvent =
  | { type: 'workflow.started'; runId: string; ts: number }
  | { type: 'step.started'; stepId: string; label: string; ts: number }
  | { type: 'step.progress'; stepId: string; message?: string; ts: number }
  | { type: 'step.completed'; stepId: string; durationMs?: number; ts: number }
  | { type: 'step.failed'; stepId: string; errorCode: string; ts: number }
  | { type: 'answer.delta'; delta: string; ts: number }
  | { type: 'card.emitted'; cardType: string; payload: unknown; ts: number };
```

使用接受 `unknown` 的 parser，驗證 event，並回傳 known event 或明確的 unknown-event fallback。

## State Rules

- Terminal states 是 final：`success`、`error`、`cancelled` 與 `timeout` 不得回到 `running`。
- Progress events 不得覆蓋 completed results。
- 有 event id 時，duplicate events 應保持 idempotent。
- Out-of-order events 應依文件化規則 ignore、buffer 或 reconcile。
- Reducers 必須處理 unknown event types 且不 crash。
- Event state 應以 stable identifiers 作為 key，例如 `runId`、`stepId`、`messageId` 或 `toolCallId`。

## Transport Rules

- 對 SSE，分開 parse event frames 與 event payloads。
- 對 WebSockets，在 reducing 前驗證每個 message。
- 對 async iterables，定義 consumer 停止時的 cleanup behavior。
- 支援時，將 cancellation 傳遞到 upstream operation。
- 在 runtimes 間 bridge streams 時尊重 backpressure。

## UI Rules

- 將 parsing、validation 與 event reduction 放在 presentational components 之外。
- 明確呈現 running、partial、completed、failed、cancelled、timeout 與 unknown states。
- 不要從 localized text 推論 machine state。
- 對 unknown future events 使用 fallback rendering。
- 當 events late arrival 或 duplicate 時，保持 timelines 穩定。

## Verification

測試 valid event sequences、unknown event types、duplicate events、out-of-order events、stream interruption、cancellation、timeout，以及 terminal events 後又接 progress 的情境。
