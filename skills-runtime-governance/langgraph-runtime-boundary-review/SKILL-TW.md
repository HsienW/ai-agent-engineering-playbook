---
name: langgraph-runtime-boundary-review
description: 在建立 custom agent runtime infrastructure 前審查 LangGraph native runtime boundaries。用於判斷 queueing、workers、checkpointers、stores、interrupts、resume flows、retries、task semantics、audit、locks，或 cost tracking 應屬於 native runtime responsibilities 還是 custom application runtime responsibilities。
---

# LangGraph Runtime Boundary Review

## Skill 介面

- 名稱：langgraph-runtime-boundary-review。
- 描述：在建立 custom agent runtime infrastructure 前審查 LangGraph native runtime boundaries，涵蓋 queueing、workers、checkpointers、stores、interrupts、resume flows、retries、task semantics、audit、locks 與 cost tracking。
- 參數：Proposed runtime capability、native LangGraph coverage、graph execution requirements、business task semantics、side-effect governance、audit needs、memory model、UI progress needs、minimal run evidence，以及 ownership decision。
- 執行指令：新增 custom runtime layers 前使用此 skill。識別 capability，可能時測試 native runtime coverage，將 ownership 指派給 native runtime、custom task runtime、application service 或 UI，並用 evidence 記錄 rationale。

先釐清 native runtime responsibilities，再新增 custom runtime layers。填補真實缺口，避免重複 runtime 已提供的 queue、worker、checkpoint 或 store behavior。

## Review Process

1. 列出 proposed capability。
2. 識別它屬於 graph execution、business task state、side-effect governance、audit、memory 或 user-facing progress。
3. 可行時用 minimal run 驗證 native runtime coverage。
4. 決定 ownership：native runtime、custom task runtime、application service 或 UI。
5. 記錄 rationale 與 evidence。

## Boundary Matrix

明確評估這些 capabilities：

- Graph state persistence。
- Interrupt and resume。
- Background queue and worker behavior。
- Thread-scoped checkpointing。
- Cross-thread long-term memory。
- Business task and step state。
- Step-level retry budget。
- Side-effect idempotency。
- Persistent audit。
- Distributed concurrency control。
- Cost tracking。
- User-facing progress timeline。

## Decision Rules

- Graph execution state 優先使用 native runtime features。
- Business task 與 step semantics 使用 custom runtime state。
- Side effects、audit、idempotency、compensation、distributed locks 與 cost policy 使用 custom governance。
- 將 long-term memory 與 checkpointed graph execution state 分開。
- 不要在 graph state 存 non-serializable resources。
- 不要把 graph node name 當成 business step，除非 contract 明確且穩定。

## Evidence

捕捉：

- Minimal run configuration。
- Interrupt and resume behavior。
- Checkpoint data ownership。
- Store read and write behavior。
- Failure and recovery behavior。
- 足以支持 custom runtime component 的 capability gap。

## Output

產生簡短 decision record，包含：

- Capability。
- Native coverage。
- Custom responsibility，如有。
- Rationale。
- Verification evidence。
- Risks 與 follow-up tests。
