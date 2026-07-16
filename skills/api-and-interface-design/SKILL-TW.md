---
name: api-and-interface-design
description: 指導穩定的 API 與 interface design。用於建立或變更 public module boundaries、REST 或 GraphQL endpoints、event contracts、SDK methods、data transfer objects、error envelopes，或 cross-team interfaces。
---

# API 與 Interface 設計

## Skill 介面

- 名稱：api-and-interface-design。
- 描述：為 public module boundaries、REST 或 GraphQL endpoints、event contracts、SDK methods、data transfer objects、error envelopes 與 cross-team interfaces 提供穩定的 API 與 interface design 指引。
- 參數：目標 interface 或 contract、預期 consumers、request 與 response shapes、error model、相容性要求、版本預期，以及可用的 schema 或 test commands。
- 執行指令：在實作或變更 public contract 前使用此 skill。先定義 contract，檢查 backward compatibility，明確處理 runtime validation，並用 contract tests 或 schema tests 驗證行為。

先設計 contracts，再實作。將每個 public shape 視為其他 caller 可能依賴的東西，即使目前 codebase 只有一個 caller。

## 流程

1. 識別 consumers、owners 與 compatibility expectations。
2. 定義 request、response、event、error 與 versioning contract。
3. 將 domain types 與 transport types 分離。
4. 在 runtime 驗證 untrusted input。
5. 加入 unknown-field 與 future-version behavior。
6. 撰寫 success、validation failure、upstream failure、timeout、cancellation 與 permission denial 的 examples。
7. 根據 contract 驗證 implementation，而不是根據 incidental current behavior。

## 設計規則

- 偏好 additive changes，而不是 renames 或 removals。
- 對 unions 與 state machines 使用 explicit discriminators。
- 回傳穩定的 error codes，human-readable messages 作為次要細節。
- 避免讓 display text、log messages 或 exception names 成為 machine contract 的一部分。
- 不要在 API layer 中用 keyword maps 編碼 natural-language understanding。
- 讓 idempotency、pagination、sorting、filtering 與 retry semantics 保持明確。
- 讓 defaults 的 ownership 清楚：server default、client default 或 product default。

## 相容性審查

變更 interface 前，回答：

- 哪些 callers 可能壞掉？
- 變更是否 backward compatible？
- 舊 clients 與新 clients 是否能共存？
- Unknown enum values 或 event types 會發生什麼？
- 是否需要 migration 或 deprecation？

## 驗證

對以下項目使用 contract tests 或 schema tests：

- Valid request 與 response。
- 缺少 required fields。
- Unknown fields。
- Unknown enum 或 union variants。
- Error envelope shape。
- Version mismatch。
- Timeout 與 cancellation propagation。
