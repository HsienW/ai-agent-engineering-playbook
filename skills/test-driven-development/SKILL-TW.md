---
name: test-driven-development
description: 以測試驅動實作。用於修復 bug、變更行為、新增邏輯、修改合約，或證明 agent 寫出的程式碼能正常運作並在迴歸風險下保持安全。
---

# 測試驅動開發

用測試證明行為，而不是記錄實作細節。

## 循環

1. Red：撰寫或找出一個失敗測試，捕捉必要行為或 bug。
2. Green：做出能通過測試的最小實作變更。
3. Refactor：在測試維持通過的前提下簡化程式碼。
4. Regression：執行鄰近且可能受影響的既有測試。

## Bug 修復模式

1. 用失敗測試、fixture、命令、trace，或固定的 input/output pair 重現 bug。
2. 確認失敗狀況符合回報的問題。
3. 修復根本原因。
4. 重新執行同一個重現步驟與相關迴歸測試。
5. 保留迴歸測試，除非成本過高，且有文件化的手動檢查更適合。

## 測試選擇

- Unit tests 用於確定性邏輯。
- Integration tests 用於邊界、adapters、持久化與 schemas。
- Contract tests 用於 APIs、events、messages 與 error envelopes。
- End-to-end tests 用於關鍵使用者流程。
- Smoke tests 僅在環境與成本可接受時用於 live providers。

## 規則

- 不要為了讓執行結果變綠而刪除失敗測試。
- 不要放寬 assertions，除非舊 assertion 本來就是錯的。
- 不要 mock 正在被測試的核心行為。
- 不要用固定 sleeps 隱藏 race conditions。
- 不要宣稱未執行的測試已通過。

## 覆蓋檢查清單

考量：

- 正常成功。
- 無效輸入。
- 未知欄位或 variants。
- 上游失敗。
- 權限遭拒。
- Timeout 與 cancellation。
- Retry 或 duplicate event。
- Partial result 或 degraded behavior。
