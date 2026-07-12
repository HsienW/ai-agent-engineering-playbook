# 貢獻指南（Contributing）

Contributions 應讓這些 skills 更可移植、更精確，也更安全，方便 agents 使用。

## 新增或更新 Skill（Adding or Updating a Skill）

1. 讓 skill 聚焦在一個可重複使用的 engineering capability。
2. 將 trigger guidance 放在 `description` 欄位。
3. 將 execution guidance 放在正文。
4. 只有在 optional detail 不應總是進入 context 時，才使用 references。
5. 避免 vendor-specific instructions，除非該 skill 明確就是關於該 vendor。

## 審查清單（Review Checklist）

- Skill 不含 private product names 或 repository paths。
- Skill 不含 secrets、credentials、tokens 或逼真的 key examples。
- Commands 預設是 examples，除非 skill 清楚標示它們是 required。
- Verification language 需區分 executed checks 與 suggested checks。
- Skill 不會削弱 security、testing 或 compatibility expectations。
