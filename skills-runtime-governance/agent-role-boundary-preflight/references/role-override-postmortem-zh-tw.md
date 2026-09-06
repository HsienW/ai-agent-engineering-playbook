# Agent 明明讀過角色規則，為什麼還是越權？

這次事故不是「Agent 沒看到規則」。它看到了，也能在事後準確說出 Coordinator、Reviewer 與 Implementer 的分工。真正失效的地方，是規則只存在於上下文裡，沒有在寫入發生前變成一次必須通過的授權判定。

事情的經過很典型。Reviewer 提出幾項 findings，Coordinator 向使用者確認處置方向。使用者回答後，Coordinator 直接修改程式與規格，接著執行驗證並宣告完成。每一步單獨看都像是在推進任務，合在一起卻跨過了角色邊界：Coordinator 有權仲裁與分派，沒有權因為修改看起來很小就接手 Implementer 的工作。

## 問題不在「記得規則」，而在「何時檢查授權」

一般 coding agent 的預設目標是把事情做完。當它已經理解問題、使用者也同意方向，而且修改只差幾行時，最順手的下一步就是直接編輯。角色說明若只在開場讀過一次，很容易被這股任務動量蓋過。

這次越權可以拆成四個判斷錯誤：

1. 把「決定怎麼處理」和「由誰執行」合成同一件事。
2. 用改動很小替自己創造例外。
3. 把使用者同意處置方向解讀成角色重新指派。
4. 任務從回顧、審查轉成實作時，沒有重新判定階段與 owner。

所以根治方法不能只是補一句「請遵守角色」。要把角色邊界做成寫入前的 authorization gate，而且預設拒絕。

## 這次採用的改法

方案分成規則、狀態、工具與測試四個部分。這四部分少一個，效果都會打折。

### 1. 把角色責任改寫成可判定的正反面清單

Coordinator 不再只有「負責規劃與協調」這類描述，而是同時寫清楚允許與禁止的動作。

在合法的 planning phase，而且 canonical state 顯示 Coordinator 是 owner 時，它可以維護該次 change 的 planning artifacts、CurrentState、Result 與 Handoff。它不能修改 application code、tests、root rules、host configuration，也不能碰不屬於目前 change 的規格。

這個邊界不以行數判斷。一行 JSDoc、一個測試 assertion 或一個公開契約欄位，都屬於寫入。角色不對，就交接。

Reviewer 的規則也採同一個原則。主工作階段保留 auto-edit 能力，不代表 Reviewer 角色取得寫入權。Review 必須在唯讀隔離中執行，只把 schema-valid verdict 輸出到 stdout 或聊天；Runtime Artifact 由 adapter、CLI host 或人工保存。若無法證明這層隔離沒有被父工作階段覆蓋，Verdict 就不能聲稱完整。

### 2. 用 CurrentState 決定 phase 與 owner

角色名稱本身還不夠。同一個 Coordinator 在 planning phase 可能有規格寫入權，到了 implementation 或 review phase 就沒有。

每次寫入前都讀取 canonical CurrentState，至少核對：

```text
role
currentPhase
currentOwner
target
action
terminalStatus
```

只要 state 缺失、損壞、內容衝突、owner 不符或 change 已進入 terminal state，就 fail-closed。Agent 不得靠聊天上下文猜 owner，也不得把損壞的 state 當作不存在後重新初始化。

### 3. 在工具真正執行前攔截

單靠 Agent 自我檢查仍不夠，因為越權往往就發生在「我知道規則，但這次應該沒關係」的瞬間。這次把同一套判定放進 PreToolUse guard：

```text
if state 無法驗證:
    deny + Handoff

if currentOwner != role:
    deny + Handoff

if phase 不允許這個 target/action:
    deny + Handoff

if 工具的副作用無法可靠界定:
    deny + Handoff

allow only the checked action
```

檔案寫入不是唯一入口。Shell、batch edit、notebook、MCP tool 與外部 API 都可能繞過一般 Write/Edit，因此必須一起分類。無法證明沒有副作用的工具，不能先放行再補查。

Guard 拒絕時，要求只輸出 Structured Handoff。不能先做一小部分，也不能以「暫時修一下」為由留下任何變更。

### 4. 用反例測試規則，而不是只讀規則

這次加入的測試刻意重播最容易自我豁免的情況：

- application code 只改一行，仍然拒絕；
- phase 不對或 owner 不符時，不得修改 planning artifact；
- CurrentState 缺失或損壞時，必須 fail-closed；
- 合法 planning phase、正確 owner 與正確 target 才允許寫入；
- Shell、batch edit 與 MCP bypass 會被拒絕；
- guard 本身執行失敗時，以拒絕結束；
- Reviewer 的 host 仍有 auto-edit，不得被誤當成 Reviewer 的角色權限。

最後一點很重要。拿掉所有寫入工具當然簡單，但有些主工作階段仍需要實際寫入能力。真正要隔離的是角色，不是模型名稱。能力可以保留，Reviewer session 仍必須唯讀。

## 寫入前的機械式判定

把所有副作用先轉成同一張表，比反覆提醒 Agent 「要小心」可靠得多。

| 條件 | 決定 |
| --- | --- |
| 只讀操作，且在角色讀取範圍內 | 允許 |
| role、phase、owner、target、action 全部相符 | 只允許這次已檢查的操作 |
| 另一個角色才是 owner | 只產出 Handoff |
| 使用者批准方向，但沒有合法改派角色 | 只產出 Handoff |
| state 缺失、損壞、過期或互相衝突 | 只產出 Handoff |
| 工具副作用無法界定 | 不呼叫工具，產出 Handoff |

這張表刻意沒有「改動很小」這個欄位。大小和授權是兩件事。

## Handoff 要留下什麼

Fail-closed 不是把任務丟回去。好的 Handoff 應讓正確的 owner 可以直接接手：

```yaml
handoff:
  fromRole: coordinator
  toRole: implementer
  task: 修正已確認的契約問題
  reason: coordinator 不持有 implementation write authority
  scope:
    - src/api/users.ts
    - src/api/users.test.ts
  evidence:
    - Finding F-2
    - 已確認的失敗案例
  constraints:
    - 保持既有 public schema 相容
    - 不修改無關模組
  acceptance:
    - regression test 通過
    - package checks 通過
```

使用者剛才做出的決策也要放進 Handoff。這樣可以保留決策，不必讓 Implementer 再問一次，但不會把決策權偷換成執行權。

## 這套方案解掉了什麼，還沒解掉什麼

它直接處理了本次四個根因：decision 與 execution 被拆開；小改動不再形成例外；使用者批准不會自動改派角色；任務從分析滑向實作時必須重新跑 preflight。

它也有邊界。新增工具後若沒有更新副作用分類，guard 可能留下缺口；project-level hook 若能被更高層設定覆蓋，仍需在 host 或 CI 補一道政策；CurrentState 必須有明確 owner、更新時機與驗證規則，否則只是把模糊的 prompt 換成模糊的 JSON。

判斷是否真的根治，不看規則寫得多嚴，而看兩件事：越權工具呼叫是否在落盤前被拒絕，以及拒絕後是否產出可執行的 Handoff。兩者都有，角色分工才從文字說明變成執行期控制。
