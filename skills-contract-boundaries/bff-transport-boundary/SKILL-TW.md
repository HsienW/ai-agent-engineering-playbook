---
name: bff-transport-boundary
description: 設計與審查 Backend-for-Frontend transport boundaries。用於實作或變更 request parsing、response mapping、streaming proxy behavior、CORS、header allowlists、authentication boundaries、timeouts、cancellation、error envelopes，或 upstream orchestration。
---

# BFF Transport Boundary

## Skill 介面

- 名稱：bff-transport-boundary。
- 描述：為 request parsing、response mapping、streaming proxy behavior、CORS、header allowlists、authentication boundaries、timeouts、cancellation、error envelopes 與 upstream orchestration 設計與審查 Backend-for-Frontend transport boundaries。
- 參數：BFF route 或 transport boundary、inbound request shape、upstream targets、authentication 與 authorization context、header policy、streaming behavior、timeout 與 cancellation rules、error envelope shape，以及 verification commands。
- 執行指令：在實作或變更 BFF transport behavior 前使用此 skill。讓 BFF 專注於 orchestration 與 transport safety，驗證 untrusted inputs，傳遞 cancellation，限制 streaming resources，明確 map errors，並驗證 success 與 failure paths。

BFF 負責 transport safety 與 orchestration。它不應吸收屬於 backend services 的 product reasoning、model prompts、workflow planning 或 domain decisions。

## Ownership

BFF 負責：

- Request parsing 與 runtime validation。
- Authentication 與 authorization boundaries。
- Header allowlists。
- CORS 與 security headers。
- Request-size limits 與 rate limits。
- Timeout 與 cancellation propagation。
- Upstream request orchestration。
- Stream forwarding 與 backpressure。
- Transport error mapping。
- Request tracing 與 audit metadata。

BFF 不負責：

- Model reasoning。
- Prompt construction。
- Agent planning。
- Workflow graph routing。
- Natural-language intent classification。
- Tool implementation details。

## Request Rules

- 將 headers、cookies、query parameters、route parameters、body fields、multipart metadata、filenames、MIME types 與 forwarded-address headers 視為 untrusted。
- 在 transport boundary 驗證。
- 不要把任意 inbound headers forward 到 upstream。
- 不要讓 clients 控制 upstream hosts、internal URLs、authorization targets、trace ownership 或 filesystem paths。

## Streaming Rules

- 不要在 memory 中 buffer 無界的 upstream streams。
- 尊重 backpressure。
- Response closed 或 destroyed 後不要繼續 write。
- 在每個 terminal path 釋放 listeners、timers 與 readers。
- Downstream client disconnect 時，在支援的情況下 abort upstream work。

定義 terminal reasons：

- `completed`
- `client_cancelled`
- `client_disconnected`
- `bff_timeout`
- `upstream_timeout`
- `upstream_error`
- `malformed_upstream_event`
- `process_shutdown`

## Error Contract

分離 HTTP status、stable error code、developer message、user message key、retryability 與 terminal reason。

絕不暴露 stack traces、internal URLs、provider responses、authorization headers、cookies、tokens 或 raw internal exception names。

## Observability

在相關位置包含 request id、trace id、route、method、status、upstream target category、duration、bytes in/out、terminal reason 與 error code。

## Verification

測試 success、invalid request、forbidden request、malformed upstream response、client disconnect、timeout、cancellation、upstream error、duplicate events、backpressure behavior，以及 listeners 與 timers cleanup。
