# AI Agent Engineering Playbook

[繁體中文](README.md) | [English](README-en.md)

## 🔨 Main content

- Provides reusable engineering skills for AI coding agents.
- This repository collects common engineering and agent runtime skills, organized by purpose into contract boundaries, delivery practices, application engineering, operational hardening, and runtime governance.

## 📂 Repository structure

```text
ai-agent-engineering-playbook/
  skills-application-engineering/
  skills-contract-boundaries/
  skills-delivery-practices/
  skills-operational-hardening/
  skills-runtime-governance/
```

- `skills-application-engineering/`:
  - For day-to-day application implementation involving Node.js/TypeScript services, CLIs, async workflows, module boundaries, frontend components, layouts, interaction states, responsive behavior, or accessibility.
- `skills-contract-boundaries/`:
  - For designing or modifying contracts across boundaries, including API and interface design, BFF transport, frontend streaming events, LangGraph backend contracts, tool result rendering, schemas, type boundaries, error envelopes, or versioned payloads.
- `skills-delivery-practices/`:
  - For managing implementation pace and quality, including code review, debugging, root cause analysis, incremental implementation, test-driven development, bug reproduction, or regression checks.
- `skills-operational-hardening/`:
  - For improving production readiness, including observability, logging, metrics, tracing, health checks, performance optimization, latency, memory, security hardening, authentication, secrets, file uploads, or privileged actions.
- `skills-runtime-governance/`:
  - For agent runtime and long-running workflow governance. Start here when a task involves agent state machines, runtime boundary reviews, context budgets, retry policies, idempotency, distributed locking, compensation or saga patterns, or runtime fault tolerance.
- Each skill contains a `SKILL.md` with portable guidance only. Project-specific paths, product names, internal workflows, credentials, and private examples have been removed.

## Usage

- Copy the required `skills-*` category directories into an agent environment that supports skill discovery, or copy individual skill folders as needed.
- These skills can serve as engineering guardrails. They do not replace project-specific rules, API contracts, security policies, or verification requirements.

## License

- MIT. See `LICENSE`.
