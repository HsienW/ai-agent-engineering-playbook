---
name: performance-optimization
description: 以證據最佳化應用程式效能。用於 latency、throughput、memory、bundle size、rendering speed、Core Web Vitals、query time、startup time，或 resource usage 重要時。
---

# 效能最佳化

## Skill 介面

- 名稱：performance-optimization。
- 描述：當 latency、throughput、memory、bundle size、rendering speed、Core Web Vitals、query time、startup time 或 resource usage 重要時，以 evidence 最佳化 application performance。
- 參數：Target metric、acceptable threshold、slow path reproduction、realistic input、baseline measurement、profiling 或 tracing data、constraints，以及 correctness 或 regression tests。
- 執行指令：在進行 performance changes 前使用此 skill。先 measurement，找出真正 bottleneck，做最小且聚焦的變更，用同一方法比較 before/after，並回報剩餘 validation risk。

先量測，再最佳化。最佳化會影響使用者或系統容量的 bottleneck，然後再次量測。

## 工作流程

1. 定義目標 metric 與可接受 threshold。
2. 用真實輸入重現 slow path。
3. 擷取 baseline measurement。
4. 用 profiling、tracing、logs、database explain output、bundle analysis 或 runtime metrics 找出 bottleneck。
5. 做出能處理 bottleneck 的最小變更。
6. 用相同量測方法比較 before 與 after。
7. 檢查正確性與迴歸風險。

## 常見區域

- Network waterfalls 與不必要的 sequential awaits。
- Large bundles 與 eager imports。
- Expensive rendering、可避免的 re-renders，以及 unstable props。
- 重複 parsing、sorting、filtering 或 serialization。
- 低效 queries、缺少 indexes，以及過多 round trips。
- Unbounded concurrency 或 memory growth。
- Cache misses、cache stampedes 與 stale invalidation rules。

## 規則

- 不要為了速度犧牲 correctness 或 security。
- 不要在缺少 ownership、invalidation 與 memory bounds 時新增 caching。
- 不要在主要 bottleneck 未知時最佳化罕見路徑。
- 不要在缺少驗證時，依賴 microbenchmarks 判斷 user-visible flows。
- 讓效能變更保持局部且可回復。

## 驗證

回報：

- Baseline。
- 做出的變更。
- 新量測結果。
- 實際執行的 test commands。
- 剩餘風險，例如缺少 production traffic validation。
