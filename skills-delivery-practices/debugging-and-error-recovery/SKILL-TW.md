---
name: debugging-and-error-recovery
description: 指導系統化 root-cause debugging。用於 tests fail、builds break、runtime behavior 異常、logs 顯示 errors，或重複修復仍無法解決同一問題時。
---

# Debugging 與錯誤復原

## Skill 介面

- 名稱：debugging-and-error-recovery。
- 描述：在 tests fail、builds break、runtime behavior 異常、logs 顯示 errors，或重複修復仍無法解決同一問題時，提供系統化 root-cause debugging 指引。
- 參數：精確 symptom、失敗 command 或 workflow、inputs、outputs、logs、timestamps、environment details、近期 diffs，以及可用的 reproduction 或 verification commands。
- 執行指令：在不明確 failure 下改 code 前使用此 skill。先 reproduce issue，辨識 failing layer，一次測試一個 hypothesis，保留 evidence，修復後重新執行原始 reproduction。

先證明 failure，再變更程式碼。在 failure layer 尚未確定前，讓調查範圍保持狹窄。

## Triage

1. 捕捉 exact symptom、command、input、output、timestamp 與 environment。
2. 用最小且可靠的 case 重現。
3. 識別 failing layer：UI rendering、state management、transport、validation、domain logic、persistence、provider、tool execution、synthesis 或 infrastructure。
4. 比較 expected 與 actual structured data。
5. 形成一個 hypothesis 並測試它。
6. 修復 root cause。
7. 重新執行原始重現步驟與鄰近迴歸檢查。

## 停止規則

出現以下情況時，停止 local patching 並重新評估：

- 兩次修復都未解決同一 symptom。
- 某個修復解決一個 case，卻破壞另一個 case。
- Failure 看起來在 layers 之間移動。
- 必須加入更多 special cases 或 prompt examples 才能通過。
- Mocks 通過，但真實 runtime behavior 仍然失敗。

## 要保留的證據

- Failing test output。
- Minimal input 與 actual output。
- 相關 logs 或 traces。
- Previous 與 current structured data 的 diff。
- 用於驗證修復的 command。

## 復原規則

- 不要為了得到 green output 而刪除失敗測試。
- 不要在未解釋舊 expectation 為何錯誤的情況下放寬 assertions。
- 不要忽略 caught errors，除非該行為是刻意設計且已測試。
- 不要用 fixed sleeps 掩蓋 race conditions。
- 當只有 mock 被測試時，不要宣稱 live integration 已修復。

## 最終檢查

回報失敗內容、變更內容、哪些證據證明修復，以及仍未驗證的部分。
