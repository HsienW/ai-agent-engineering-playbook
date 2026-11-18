# AI Agent Engineering Playbook

[English](README-en.md) | [繁體中文](README.md) | [简体中文](README-cn.md)

## 🔨 主要内容（Main content）
- 提供可供 AI 编程智能体重复使用的工程技能（engineering skills）。
- 这个仓库收录常见的工程技能与智能体运行时技能，并按用途分为契约边界、交付实践、应用工程、运行加固以及运行时治理。

## 📂 结构（Repository Structure）

```text
ai-agent-engineering-playbook/
  skills-application-engineering/
  skills-contract-boundaries/
  skills-delivery-practices/
  skills-operational-hardening/
  skills-runtime-governance/
```

- `skills-application-engineering/`：
  - 用于日常应用开发。涉及 Node.js/TypeScript 服务、CLI、异步工作流、模块边界、前端组件、布局、交互状态、响应式行为或无障碍访问。
- `skills-contract-boundaries/`：
  - 用于设计或修改跨边界契约。涉及 API/接口设计、BFF 传输、前端流式事件、LangGraph 后端契约、工具结果渲染、schema、类型边界、错误封装或带版本的载荷。
- `skills-delivery-practices/`：
  - 用于控制实现节奏与质量。包含代码审查、调试、根因分析、渐进式实现、测试驱动开发、缺陷复现或回归检查。
- `skills-operational-hardening/`：
  - 用于强化生产就绪程度。包含可观测性、日志、指标、链路追踪、健康检查、性能优化、延迟、内存、安全加固、身份认证、机密信息、文件上传或特权操作。
- `skills-runtime-governance/`：
  - 用于智能体运行时与长流程治理。如果任务涉及智能体状态机、运行时边界审查、上下文预算、重试策略、幂等性、分布式锁、补偿/saga 或运行时容错，可先查看这一类。
- 每个技能都包含一个 `SKILL.md`，内容仅保留可移植的指导。已移除项目专用路径、产品名称、内部工作流、凭据与私有示例。

## 如何使用（Usage）

- 将需要的 `skills-*` 分类目录复制到支持技能发现的智能体环境中，或根据需要复制单个技能文件夹。
- 这些技能可作为工程护栏使用。它们不能取代项目专用规则、API 契约、安全策略或验证要求。

## 授权（License）

- MIT。请参阅 `LICENSE`。
