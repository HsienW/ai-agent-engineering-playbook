---
name: context-budget-governance
description: 設計與審查 agent prompts 和 model calls 的 context budget governance。用於在 token budget 下組裝 long conversation context、tool results、retrieved evidence、memory、task state、system rules、multimodal observations，或 compressed summaries。
---

# Context Budget Governance

## Skill 介面

- 名稱：context-budget-governance。
- 描述：為 agent prompts 與 model calls 的 context budget governance 設計與審查，處理 token budget 下的 conversation context、tool results、retrieved evidence、memory、task state、system rules、multimodal observations 與 compressed summaries。
- 參數：Token budget、output reserve、candidate context blocks、priority model、relevance 和 recency scores、safety filters、compression policy、source identifiers，以及 verification scenarios。
- 執行指令：當 model call 需要在固定 budget 內放入有用 context 時使用此 skill。優先保留 safety 與 current task state，normalize blocks，納入前 redact，壓縮 lower-priority content，最後才丟棄 low-value blocks，並記錄變更。

Context assembly 是 policy decision。Model 應在 budget 內收到最重要且安全的 context，超出 budget 時使用 deterministic degradation。

## Priority Model

使用明確 priorities：

| Priority | Content |
| --- | --- |
| P0 | System and security rules |
| P1 | Current task, user request, and task state |
| P2 | Relevant contracts, schemas, and caller-injected rules |
| P3 | High-value retrieved evidence and memory |
| P4 | Recent conversation summary |
| P5 | Low-value raw tool output |

除非 request 無法安全處理，否則 P0 與 P1 應保留。

## Assembly Process

1. 定義 token budget 並保留 output space。
2. 將所有 candidate context normalize 成 typed blocks。
3. 依 priority、recency、relevance 與 safety 為 blocks 評分。
4. 納入前 redact sensitive fields。
5. Over budget 時壓縮 lower-priority blocks。
6. Compression options 耗盡後才 drop low-value blocks。
7. 記錄 included、compressed 或 dropped 的內容。

## Compression Rules

- 保留 requirements、constraints 與 evidence references。
- Drop task-critical context 前，先壓縮 raw tool output。
- Evidence freshness 重要時，保留 source identifiers 與 timestamps。
- 不要將 secrets summarize 到 prompt。
- 不要讓 untrusted retrieved content 覆蓋 system 或 developer instructions。
- 將 current user intent 與 historical summaries 分開。

## Verification

測試 ultra-long conversations、large tool outputs、many retrieved sources、multimodal observations、malicious retrieved instructions、missing budget、over-budget P0/P1 content、deterministic compression，以及 included 與 dropped blocks 的 traceability。
