---
name: frontend-ui-engineering
description: 建構 production-quality frontend interfaces。用於建立或修改 UI components、layouts、interaction states、responsive behavior、accessibility、visual polish，或 user-facing workflows。
---

# Frontend UI 工程

優先建構真正可用的體驗。貼合產品情境：operational tools 應該高效率且容易掃讀；creative 或 consumer experiences 可以更有表現力。

## Component 流程

1. 識別使用者 workflow 與主要 task。
2. 重用既有 design system、component patterns、icons、spacing 與 state conventions。
3. 明確建模 states：loading、empty、success、partial、error、disabled、permission denied、offline 與 long content。
4. 將 data parsing 與 transport concerns 放在 presentational components 之外。
5. 為 labels、focus order、keyboard behavior 與 status updates 加上 accessibility semantics。
6. 使用真實內容長度驗證 desktop 與 mobile layouts。

## Layout 規則

- 對 fixed-format controls、grids、boards、counters 與 toolbars 使用穩定尺寸。
- 避免降低掃讀性的 nested cards 與 decorative wrappers。
- 不要讓文字重疊、截斷關鍵資訊，或讓 controls 意外 resize。
- 對常見命令，在 icon 清楚時偏好熟悉的 icon buttons。
- 對不熟悉的 icons 使用 tooltips。
- 將 display type 保留給真正的 page-level hierarchy。

## State 規則

- 從 typed data 與 explicit status fields 推導 UI state。
- 不要從 display text 推測 backend semantics。
- 讓 optimistic updates 可回復。
- 破壞性 actions 應明確，並在可能時可復原。
- 顯示 partial progress，不要隱藏 errors。

## 驗證

檢查：

- 窄版與寬版寬度下的 responsive behavior。
- Long labels、long values 與 missing data。
- Keyboard navigation 與 focus visibility。
- Loading、empty、error 與 permission states。
- 適用時檢查 console errors 與 hydration warnings。
