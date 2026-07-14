# Agent 指示（Agent Instructions）

這些 instructions 適用於本 playbook repository。

## 範圍（Scope）

本 repository 包含可重複使用的 agent skills。除非 skill 明確記錄 public、generic pattern，否則內容應保持可移植，並避免 project-specific facts。

## Skill 規則（Skill Rules）

- 每個 skill folder 都必須包含 `SKILL.md`。
- `SKILL.md` frontmatter 只能包含 `name` 與 `description`。
- Skill names 必須使用小寫字母、數字與 hyphens。
- 正文應保持精簡。只有在 large optional details 需要 progressive disclosure 時，才移到 `references/`。
- 不要包含 private repository paths、customer names、internal service names、API keys、tokens、secrets、passwords 或 credentials。
- 優先使用 `src/api/users.ts` 這類 generic examples，而不是 private repository 的真實 paths。
- 除非實際執行過，否則不要宣稱 command、test、live provider call 或 security scan 已通過。

## 貢獻標準（Contribution Standard）

發布變更前：

1. 檢查每個 `SKILL.md` 都有 valid frontmatter。
2. 搜尋 private names、absolute paths、tokens 與 credentials。
3. 確認 examples 可移植，且不依賴任何單一公司的 stack。
4. 讓變更聚焦在正在編輯的 skill。
