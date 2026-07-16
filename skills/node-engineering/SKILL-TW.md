---
name: node-engineering
description: 指導穩健的 Node.js 與 TypeScript 工程實作。用於 Node services、scripts、CLIs、tests、streams、async workflows、module boundaries、environment configuration、logging、caching、profiling，或 graceful shutdown。
---

# Node 工程

## Skill 介面

- 名稱：node-engineering。
- 描述：為 Node.js 與 TypeScript services、scripts、CLIs、tests、streams、async workflows、module boundaries、environment configuration、logging、caching、profiling 與 graceful shutdown 提供穩健工程指引。
- 參數：Runtime entry points、package scripts、environment variables、async resources、dependency boundaries、logging requirements、test commands，以及代表性 input 或 fixtures。
- 執行指令：在 Node.js 或 TypeScript runtimes 工作時使用此 skill。明確定義 runtime contracts，在邊界驗證 external data 與 environment，謹慎管理 async resources，並執行適用的本地 lint、type-check、test 或 build commands。

偏好明確的 runtime 行為，而不是 framework assumptions。將 process lifetime、async resources、environment variables 與 module boundaries 視為 production contracts。

## 核心規則

- 在 startup 時驗證 environment variables，並用安全訊息 fail fast。
- 將 secrets 放在 environment 或 secret managers，不要放在 code 或 logs。
- 對預期內失敗使用帶有穩定 codes 的 structured errors。
- 外部資料在完成解析前使用 `unknown`。
- 讓 module imports 與專案的 module system 保持一致。
- 在 tests 與 shutdown paths 中關閉 servers、database clients、workers、timers、file handles 與 streams。
- 對 network、file 與 CPU-heavy work 設定 concurrency 上限。

## Async Patterns

- 對長時間操作使用 `AbortSignal` 或等效的 cancellation propagation。
- 只有在工作彼此獨立且 failure behavior 可接受時，才偏好 `Promise.all`。
- 當 fan-out 可能隨 input size 成長時，使用 concurrency limits。
- 避免 unhandled promises 與 fire-and-forget work，除非有監督機制。

## 測試

- 讓 tests 具備 determinism。
- 避免 fixed sleeps；等待可觀察條件。
- 隔離 process-level state，例如 environment variables、timers 與 global caches。
- 確保 tests 在建立資源的同一個 scope 中釋放資源。
- 當 process hangs 時，先檢查 open handles，再新增 timeouts。

## 可觀測性

- 在 boundaries 與 failures 記錄 structured events。
- 可用時包含 correlation identifiers。
- 不要記錄 tokens、credentials、raw authorization headers 或 sensitive payloads。

## 驗證

如果 local package 有 lint、test、type-check 與 build commands，就使用它們。對 scripts，使用安全 fixture input 執行最小代表性命令。
