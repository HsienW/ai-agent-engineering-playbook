---
name: langgraph-backend-contract
description: 設計與審查 LangGraph backend contracts。用於建立或變更 graph state、nodes、edges、reducers、commands、sends、checkpoints、runtime events、provider adapters、tool adapters、prompts、terminal states，或 resumable workflows。
---

# LangGraph Backend Contract

## Skill 介面

- 名稱：langgraph-backend-contract。
- 描述：為 graph state、nodes、edges、reducers、commands、sends、checkpoints、runtime events、provider adapters、tool adapters、prompts、terminal states 與 resumable workflows 設計與審查 LangGraph backend contracts。
- 參數：Graph state fields、node 與 edge contracts、reducer behavior、checkpoint 與 resume requirements、tool 或 provider schemas、runtime event shape、prompt-sensitive behavior、terminal states，以及 verification scenarios。
- 執行指令：在建立或變更 LangGraph backend behavior 前使用此 skill。保持 state 可 JSON serialize，在寫入 state 前驗證 model 與 tool outputs，隔離 provider-specific logic，emit structured runtime events，並驗證 resumability 與 terminal-state invariants。

LangGraph backend work 必須保留 graph state contracts、resumability、tool boundaries 與 terminal-state behavior。將 model output 與 tool output 視為 untrusted，直到完成 validation。

## Graph Rules

- 保持 graph state 可 JSON serialize。
- 不要在 state 中存放 clients、streams、sockets、functions、abort controllers、raw responses、credentials 或其他 non-serializable objects。
- 對每個 new state field 定義 owner、default value、reducer behavior、readers 與 checkpoint impact。
- 不要假設 node 只會執行一次。
- External side effects 需要 idempotency 與 resume behavior。
- Terminal states 不得回到 running。

## Node and Edge Rules

- 用 structured values route，不要 match natural-language prose。
- 保持 node inputs 與 outputs 明確。
- 在寫入 state 前驗證 model 與 tool outputs。
- 對 graph ids、node names、event types、tool names、error codes 與 terminal statuses 使用 stable machine identifiers。
- 避免把 concrete provider logic import 到 generic graph contracts。

## Tool and Provider Boundaries

- Tool arguments 在驗證前都是 untrusted。
- Tool results 被跨 layers consumption 時，應使用 typed structured output 與 schema versions。
- Provider adapters 應隔離 provider-specific response formats。
- Models 不得決定未檢查的 paths、hosts、URLs、commands、permissions 或 credential use。
- Tool 與 MCP-like capabilities 應預設 deny-by-default。

## Runtime Events

Emit structured runtime events 以觀測 workflow progress：

- Graph started。
- Node started 與 completed。
- Tool started 與 completed。
- Retry attempted。
- Context built。
- Partial answer emitted。
- Terminal state reached。
- Error 或 cancellation recorded。

可用時包含 correlation identifiers，例如 run id、thread id、graph id、node name、tool call id、provider、duration、retry count、terminal status 與 error code。

## Prompt and Model Output Rules

- Prompt changes 是 code changes。
- 為 prompt-sensitive behavior 加入 golden cases 或 regression fixtures。
- 對 structured model output 使用 schema validation。
- 不要用 prompt wording 補 missing product capability。
- 區分 deterministic tests、mocked provider tests 與 live smoke tests。

## Verification

測試 graph success、invalid model output、invalid tool output、no-tool path、wrong-tool path、retry、timeout、cancellation、resume from checkpoint、duplicate node execution，以及 terminal-state invariants。
