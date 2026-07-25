---
name: agent-observability-fault-tolerance
description: 設計與審查 agent runtime observability 與 model/provider fault tolerance。用於新增 metrics、traces、cost tracking、audit alignment、structured runtime events、model fallback、structured output repair、refusal handling、provider timeouts，或 local execution tree diagnostics。
---

# Agent Observability 與 Fault Tolerance

## Skill 介面

- 名稱：agent-observability-fault-tolerance。
- 描述：為 metrics、traces、cost tracking、audit alignment、runtime events、fallback、repair、refusal handling、timeouts 與 execution tree diagnostics 設計與審查 agent runtime observability 和 model/provider fault tolerance。
- 參數：Task 與 step lifecycle、model 與 tool calls、retry 和 fallback policy、trace identifiers、metric names、cost fields、redaction policy、terminal states，以及 verification scenarios。
- 執行指令：當 runtime behavior 需要覆蓋 success、retry、fallback、refusal、timeout 與 terminal paths 的觀測能力時使用此 skill。新增有界 metrics，保留 trace continuity，分類 failures，redact sensitive data，並測試每條 recovery path 的 telemetry。

Agent runtime 應讓 task、step、model、tool、retry 與 terminal paths 都看得見。Fault tolerance 要明確，也要能被觀測。

## Signals

在以下位置捕捉 metrics 與 traces：

- Task lifecycle。
- Step lifecycle。
- Tool calls。
- Model calls。
- Retry attempts。
- Structured output repair。
- Fallback provider selection。
- Human approval 或 refusal。
- Compensation。
- Terminal state。

## Metrics

追蹤：

- Task success rate。
- Task completion latency。
- Step failure rate。
- Retry recovery rate。
- Resume success rate。
- Idempotency hit rate。
- Duplicate side-effect prevention rate。
- Tool success、timeout 與 latency。
- Permission denial rate。
- Compensation success rate。
- Model fallback rate。
- Structured output repair success rate。
- Cost per successful task。

## Trace Shape

使用符合實際 execution chain 的 trace hierarchy：

```text
request
  agent task
    graph node
      model call
      tool call
      retry attempt
      repair attempt
```

可用時加入 stable identifiers：request id、task id、step id、run id、thread id、graph id、node name、tool call id、provider、model category、duration、retry count、terminal status 與 error code。

## Fault Tolerance

- Provider temporary failure：在 budget 內 retry，policy 允許時 fallback。
- Provider timeout：用 backoff retry，再 fallback 或回傳 timeout result。
- Structured output parse error：執行有界 repair。
- Structured output validation error：用明確 validation hint 修復。
- Refusal 或 policy denial：不要盲目 retry；回傳 structured refusal signal。
- Tool timeout 或 upstream failure：分類後套用 retry policy。

## Safety Rules

- 不要記錄 credentials、raw authorization headers、private payloads、full prompts、full conversations 或 unredacted personal data。
- 不要讓 fallback、repair、retry 或 degraded execution 從 telemetry 中消失。
- 不要把 provider failure、validation failure、refusal 與 timeout 混成同一種 generic error。

## Verification

測試 provider outage、timeout、structured parse failure、validation failure、refusal、retry exhaustion、fallback success、cost accounting、trace continuity、metric emission 與 redaction。
