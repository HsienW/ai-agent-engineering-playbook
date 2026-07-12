---
name: incremental-implementation
description: 以小型且可驗證的 increments 交付變更。用於實作 multi-file feature、refactor、migration、bug fix，或任何大到若一次完成會有風險的任務。
---

# 漸進式實作

一次建構一個完整 slice。每個 slice 都應讓 workspace 維持在可運作、可測試的狀態。

## 流程

1. 定義最小有用 slice。
2. 只實作該 slice。
3. 執行最相關的驗證。
4. 在擴大 scope 前修復 failures。
5. 記錄變更內容與剩餘事項。
6. 移動到下一個 slice。

## 切分策略

- Vertical slice：從 input 到 observable result，貫穿 stack 的一條路徑。
- Contract-first slice：先定義 types、schemas 或 API shape，再平行實作。
- Risk-first slice：及早證明最不確定的技術點。
- Additive slice：先在 safe defaults 後方引入新行為，再取代舊行為。

## Scope 規則

- 不要把 feature work 與無關 cleanup 混在一起。
- 不要 rename 或 reformat slice 之外的檔案。
- 不要在 repeated use 證明需要前新增 abstractions。
- 不要留下 broken intermediate states。
- 如果 feature 未完成但必須與 production code 共存，請用明確的 safe default gate 住它。

## 驗證

每個 slice 後，執行最窄但有意義的檢查。最後執行 changed area 需要的完整檢查。沒有中間變更時，不要重複執行同一個已通過的命令。

## Handoff

回報：

- Completed slices。
- 實際執行的 commands。
- Failures 與 fixes。
- Unverified areas。
- Suggested next slice。
