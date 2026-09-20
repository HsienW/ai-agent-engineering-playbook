# AI Agent Engineering Playbook

[繁體中文](README.md) | [English](README-en.md)

## 🔨 主要內容（Main content）
- 提供 AI coding agents 重複使用的工程技能（engineering skills）。
- 這個 repository 收錄常見工程與 agent runtime skills，並依用途分成 contract boundaries、delivery practices、application engineering、operational hardening，以及 runtime governance。

## 📂 結構（Repository Structure）

```text
ai-agent-engineering-playbook/
  skills-application-engineering/
  skills-contract-boundaries/
  skills-delivery-practices/
  skills-operational-hardening/
  skills-runtime-governance/
```

- `skills-application-engineering/`：
  - 用於日常 application implementation。涉及 Node.js/TypeScript service、CLI、async workflow、module boundary、frontend component、layout、interaction state、responsive behavior 或 accessibility。
- `skills-contract-boundaries/`：
  - 用於設計或修改跨邊界 contract。涉及 API/interface design、BFF transport、frontend streaming events、LangGraph backend contracts、tool result rendering、schema、type boundary、error envelope 或 versioned payload。
- `skills-delivery-practices/`：
  - 用於控制實作節奏與品質。包含 code review、debugging、root-cause analysis、incremental implementation、test-driven development、bug reproduction 或 regression checks。
- `skills-operational-hardening/`：
  - 用於強化 production readiness。包含 observability、logging、metrics、tracing、health checks、performance optimization、latency、memory、security hardening、auth、secrets、file uploads 或 privileged actions。
- `skills-runtime-governance/`：
  - 用於 agent runtime 與長流程治理。若任務涉及 agent state machine、runtime boundary review、context budget、retry policy、idempotency、distributed locking、compensation/saga 或 runtime fault tolerance，可先看這一類。
- 每個 skill 都包含一個 `SKILL.md`，內容只保留可移植的 guidance。Project-specific paths、product names、internal workflows、credentials 與 private examples 都已移除。

## 如何使用（Usage）

- 將需要的 `skills-*` 分類目錄複製到支援 skill discovery 的 agent environment，或依需求複製個別 skill folders。
- 這些 skills 可作為 engineering guardrails 使用。它們不取代 project-specific rules、API contracts、security policies 或 verification requirements。

## 授權（License）

- MIT。請見 `LICENSE`。
