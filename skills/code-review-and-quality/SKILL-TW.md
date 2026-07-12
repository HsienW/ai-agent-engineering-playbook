---
name: code-review-and-quality
description: 執行多面向 code review。用於合併 agent 或人類寫的程式碼前，評估 diffs 的 correctness、maintainability、security、tests、observability 與 contract risk。
---

# Code Review 與品質

先審查 bugs。相較於摘要，帶有證據的 findings 更重要。

## 審查順序

1. Correctness：實作在真實 edge cases 中是否滿足需求？
2. Contract safety：public APIs、events、schemas、errors 或 state machines 是否安全地變更？
3. Security：untrusted inputs、credentials、authorization 與 data exposure 是否正確處理？
4. Reliability：timeouts、retries、cancellation、concurrency 與 partial failure 是否有處理？
5. Tests：測試是否證明新行為並保護重要迴歸？
6. Maintainability：程式碼是否簡單、局部、可讀，且與附近 patterns 一致？
7. Observability：是否能診斷 production behavior，同時不洩漏 secrets？

## 嚴重程度

- Blocker：security issue、data loss、contract break、build failure、core flow failure，或無法安全發布的變更。
- Major：可能的 edge-case failure、不完整的 error handling、缺少 meaningful behavior 的 regression test，或 layers 之間責任漂移。
- Minor：naming、duplication、readability，或低風險的 maintainability issue。

## Finding 格式

每個 finding 都包含：

- Severity。
- 可用時提供 file 與 line。
- Problem。
- Triggering scenario。
- Consequence。
- Suggested fix。
- Related contract、requirement 或 invariant。

## 審查紀律

- 不要只審查 happy path。
- 不要假設 generated code 是正確的。
- 當局部修復足夠時，不要要求大型 refactors。
- 沒有證據時，不要標記 concern 已解決。
- 將未執行的驗證與失敗的驗證分開說明。

## 無 Findings

如果未發現問題，清楚說明，並列出剩餘風險，例如未測試的 live integrations、缺少 load tests，或無法取得的 environment checks。
