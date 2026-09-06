---
name: agent-role-boundary-preflight
description: 在任何副作用發生前，檢查 Agent 的角色、lifecycle ownership、target scope 與 authorization。用於 multi-agent 或角色分離 workflow，在寫入程式碼、規格、設定、測試、文件、Runtime Artifact、version control 或外部系統前，以及任務從分析、規劃或審查轉入實作時。
---

# Agent 角色邊界預檢

工具存取能力不等於操作授權。每次產生副作用前都要執行這項預檢。任務模式、角色、phase、owner、target 或預期副作用改變時，必須重新檢查。

## 必要輸入

從具權威性的專案規則與目前 workflow state 取得下列資訊。不得根據模型名稱、host、可用工具或先前對話的執行動量推測缺少的值。

- `role`：目前 phase 指定的執行角色。
- `phase`：目前 lifecycle phase；若 workflow 有定義才使用。
- `owner`：目前 phase 中獲准執行操作的角色。
- `action`：確切操作及其副作用。
- `target`：將被修改的檔案、資源、repository、branch、Runtime Artifact 或外部系統。
- `authoritySource`：授權這項操作的規則、state record 或明確角色改派。
- `handoff`：目前有效的 Handoff 及其允許範圍；若適用才使用。

## 預檢程序

1. 將下一次 tool call 分類為 read-only 或 side-effecting。寫入、編輯、Shell command、version-control mutation、generated artifact、訊息發送、deployment 與外部 API mutation 都屬於副作用。
2. 確認實際角色。Coordinator、Planner、Reviewer、Implementer 與 Verifier 可能在同一 host 執行，但擁有不同權限。
3. 從 workflow 的 canonical state 取得 phase 與 owner。若沒有 lifecycle state，使用最接近目標的權威角色與 scope 規則。
4. 將確切 action 與 target 對照角色的正面寫入範圍及明確禁止事項。
5. 精確判讀使用者訊息。批准 decision、finding、plan 或預期結果不等於重新指派執行角色。只有明確改派，而且較高優先序的專案 policy 允許時，才視為有效。
6. 套用下方 decision table。Authority 缺失、過期、格式錯誤、互相矛盾或無法驗證時，一律 fail-closed。
7. 呼叫工具前，記錄精簡的判定結果。任何相關條件改變時，重新執行本程序，不得沿用舊判定。

## Decision Table

| 條件 | 判定 | 必要回應 |
| --- | --- | --- |
| Action 是 read-only，且位於角色的讀取範圍 | `ALLOW_READ` | 繼續，但不得產生修改 |
| Role、phase、owner、target 與 action 全部符合權威範圍 | `ALLOW_WRITE` | 只執行已檢查的操作 |
| Action 由另一個角色持有 | `HANDOFF` | 產出 Structured Handoff，不得修改任何內容 |
| Authority data 缺失、無效、過期或互相矛盾 | `HANDOFF` | 交給 Coordinator 或 authority owner，不得修改任何內容 |
| 無法界定或分類工具副作用 | `HANDOFF` | 不得呼叫工具 |
| 使用者批准方向，但沒有有效改派執行角色 | `HANDOFF` | 將決策保留在 Handoff，不得修改任何內容 |

改動大小不會改變判定結果。一行修改和大型修改需要相同的角色授權。

## 精簡檢查紀錄

Host 支援時，使用下列內部或聊天可見紀錄：

```yaml
roleBoundaryCheck:
  role: reviewer
  phase: implementation_review
  owner: implementer
  action: edit
  target: src/api/users.ts
  authoritySource: workflow-state
  decision: HANDOFF
  reason: implementation is owned by another role
```

不得把這份紀錄視為授權來源。它只說明系統如何評估 authority。

## Structured Handoff

判定為 `HANDOFF` 時，提供足以讓 owner 繼續執行、且不必重做分析的資訊：

```yaml
handoff:
  fromRole: reviewer
  toRole: implementer
  task: 修正已驗證的回應映射問題。
  reason: Reviewer 發現問題，但不持有 implementation ownership。
  scope:
    - src/api/users.ts
    - src/api/users.test.ts
  evidence:
    - Finding F-2
    - 失敗案例：未知 response field 被丟棄
  constraints:
    - 保持 public response schema
    - 不修改無關 modules
  acceptance:
    - Regression test 通過
    - 既有 package checks 通過
```

產出 Handoff 後，不得進行暫時、方便或部分修改。

## Enforcement Guidance

Prompt instructions 有其必要，但不能成為唯一控制。Host 支援 pre-tool hooks 或 policy middleware 時，在 tool boundary 執行相同判定：

```text
if authority evidence is missing or invalid:
    deny and require handoff
if role != owner for this action and target:
    deny and require handoff
if the tool's side effects cannot be bounded:
    deny and require handoff
allow only the action and target that were checked
```

Side-effecting tools 優先採用範圍狹窄的 allowlist。涵蓋 Shell command、batch edit、notebook、version-control command、MCP tool 與 external API 等替代寫入路徑。新工具在副作用完成分類前，視為不受信任。

## Verification

將 policy 當成可執行行為測試，不要只審查文字。至少涵蓋：

- 只有一行的 out-of-role edit；
- Role 正確，但 phase 或 owner 錯誤；
- Workflow state 缺失、格式錯誤、過期或已進入 terminal；
- 合法 in-role write 只寫入明確允許的 target；
- Shell、batch-edit、notebook、MCP 與 external-action bypass；
- 使用者決策沒有重新指派執行角色；
- 任務從 read-only analysis 漂移到 implementation；
- Hook 或 policy engine 執行失敗，必須 deny，不得默默 allow。

只有在拒絕操作後 target 保持不變，且系統產出可執行的 Handoff，才能視為檢查成功。

若要了解這份 guidance 背後的事故模式，請閱讀 [角色越權復盤](references/role-override-postmortem-zh-tw.md)。

