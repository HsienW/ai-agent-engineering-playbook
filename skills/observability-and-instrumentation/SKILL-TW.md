---
name: observability-and-instrumentation
description: 新增或審查 observability。用於為 features、background jobs、APIs、tools 與 integrations 新增 logs、metrics、traces、audit events、health checks、alerts，或 production diagnostics。
---

# Observability 與 Instrumentation

## Skill 介面

- 名稱：observability-and-instrumentation。
- 描述：為 features、background jobs、APIs、tools 與 integrations 新增或審查 logs、metrics、traces、audit events、health checks、alerts 與 production diagnostics。
- 參數：要觀測的 feature 或 workflow、operator questions、success 與 failure paths、correlation identifiers、sensitive fields、telemetry sinks、alert thresholds，以及 verification method。
- 執行指令：新增或評估 production signals 時使用此 skill。定義 operators 需要回答的問題，選擇 bounded signals，redact sensitive data，加入 correlation，並在預期 sink 驗證 emitted telemetry。

針對 operators 需要回答的問題加上 instrumentation。不要用 noisy logs 取代清楚的 signals。

## 流程

1. 定義 feature 的 "working" 是什麼意思。
2. 識別 incident 期間需要回答的問題。
3. 選擇 signal：log、metric、trace、audit event、health check 或 alert。
4. 在 request、job、tool 或 workflow boundaries 加入 correlation identifiers。
5. 捕捉 success、failure、timeout、cancellation、retry 與 degraded paths。
6. Redact sensitive values。
7. 驗證 signals 出現在預期的 local 或 staging sink。

## Signal 指引

- Logs：說明離散 events 與 decisions。
- Metrics：追蹤 rates、latency、saturation、errors 與 business counters。
- Traces：串連跨 services、tools、providers、queues 與 retries 的工作。
- Audit events：以 actor、target 與 outcome 記錄 security-sensitive actions。
- Health checks：證明 dependency readiness，而不暴露 internals。

## Redaction 規則

絕不記錄 credentials、tokens、passwords、authorization headers、private keys、session identifiers、完整 request bodies 或 personal data，除非 policy 明確允許 redacted field。

## Alert 規則

- 對 user impact、data risk、security risk 或 exhausted capacity 發出 alert。
- 避免不需要 action 的 alerts。
- 可能時包含 runbook context。

## 驗證

確認：

- 預期的 success 與 failure signals 有被 emitted。
- Identifiers 能追蹤單一 request 或 workflow。
- Sensitive data 不存在。
- Metric names 與 labels 有界且穩定。
