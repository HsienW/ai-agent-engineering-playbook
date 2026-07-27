---
name: compensation-saga-runtime
description: 設計與審查 agent workflows 的 compensation 與 Saga runtime behavior。用於處理 multi-step side effects、rollback plans、irreversible actions、compensation ordering、manual escalation、cancellation cleanup，或 audit-linked recovery flows。
---

# Compensation Saga Runtime

## Skill 介面

- 名稱：compensation-saga-runtime。
- 描述：為 multi-step side effects、rollback plans、irreversible actions、compensation ordering、manual escalation、cancellation cleanup 與 audit-linked recovery flows 設計與審查 compensation 和 Saga runtime behavior。
- 參數：Completed side-effecting steps、failure point、reversible 與 irreversible actions、dependency order、audit trail、idempotency keys、retry 和 timeout policy、manual escalation rules，以及 verification cases。
- 執行指令：當 agent workflow 跨多個 steps 執行 side effects 時使用此 skill。根據 completed steps 建立 compensation，依 policy-defined order 執行，記錄 failures，標記 irreversible actions，並測試 cancellation、duplicate compensation、timeout 與 audit reconstruction。

Compensation 定義後續 step 失敗或使用者取消時，已完成 side effects 的 recovery plan。

## Core Types

```ts
type CompensationAction = {
  actionId: string;
  description: string;
  execute: () => Promise<CompensationResult>;
  isReversible: boolean;
};

type CompensationPlan = {
  taskId: string;
  completedSteps: string[];
  failurePoint: string;
  actions: CompensationAction[];
};
```

## Planning Rules

- 只從 completed side-effecting steps 建立 compensation plans。
- 不要 compensate 未執行的 steps。
- 除非 domain policy 另有規定，否則依 reverse dependency order 執行 compensation。
- 標記 irreversible actions，並 escalate 給 manual handling。
- 將 compensation events 存在與 original operation 相同的 audit trail。
- 將 compensation 與 idempotency 配對，避免 retries 重複 rollback work。

## Execution Rules

- Failed compensation 不得被默默吞掉。
- Compensation failure 應記錄 state、reason code 與 manual follow-up。
- Side effects 已發生時，cancellation 可能需要 compensation。
- Compensation 應有自己的 timeout、retry policy 與 idempotency key。
- Runtime 應區分 original failure 與 compensation failure。

## Common Scenarios

- Resource 已 reserved 且使用者取消：release reservation。
- Status 已 changed 且後續 notification failed：依 policy 決定保留 status 或 restore。
- Irreversible notification 已 sent：標記為 irreversible，建立 correction 或 manual follow-up。
- Downstream create 成功但後續 enrichment failed：policy 允許時保留 created resource 並 retry enrichment。

## Verification

測試 reverse-order compensation、irreversible actions、compensation timeout、compensation failure escalation、duplicate compensation requests、cancellation after side effect，以及 audit trail reconstruction。
