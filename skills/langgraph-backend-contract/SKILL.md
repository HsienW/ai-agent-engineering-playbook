---
name: langgraph-backend-contract
description: Design and review LangGraph backend contracts. Use when creating or changing graph state, nodes, edges, reducers, commands, sends, checkpoints, runtime events, provider adapters, tool adapters, prompts, terminal states, or resumable workflows.
---

# LangGraph Backend Contract

## Skill Interface

- Name: langgraph-backend-contract.
- Description: Design and review LangGraph backend contracts for graph state, nodes, edges, reducers, commands, sends, checkpoints, runtime events, provider adapters, tool adapters, prompts, terminal states, and resumable workflows.
- Parameters: Graph state fields, node and edge contracts, reducer behavior, checkpoint and resume requirements, tool or provider schemas, runtime event shape, prompt-sensitive behavior, terminal states, and verification scenarios.
- Instructions: Use this skill before creating or changing LangGraph backend behavior. Keep state serializable, validate model and tool outputs before writing state, isolate provider-specific logic, emit structured runtime events, and verify resumability plus terminal-state invariants.

LangGraph backend work must preserve graph state contracts, resumability, tool
boundaries, and terminal-state behavior. Treat model output and tool output as
untrusted until validated.

## Graph Rules

- Keep graph state JSON serializable.
- Do not store clients, streams, sockets, functions, abort controllers, raw
  responses, credentials, or other non-serializable objects in state.
- Define owner, default value, reducer behavior, readers, and checkpoint impact
  for every new state field.
- Do not assume a node runs only once.
- External side effects need idempotency and resume behavior.
- Terminal states must not return to running.

## Node and Edge Rules

- Route by structured values, not by matching natural-language prose.
- Keep node inputs and outputs explicit.
- Validate model and tool outputs before writing them into state.
- Use stable machine identifiers for graph ids, node names, event types, tool
  names, error codes, and terminal statuses.
- Avoid importing concrete provider logic into generic graph contracts.

## Tool and Provider Boundaries

- Tool arguments are untrusted until validated.
- Tool results should use typed structured output with schema versions when
  consumed across layers.
- Provider adapters should isolate provider-specific response formats.
- Models must not decide unchecked paths, hosts, URLs, commands, permissions, or
  credential use.
- Tool and MCP-like capabilities should be deny-by-default.

## Runtime Events

Emit structured runtime events for observable workflow progress:

- Graph started.
- Node started and completed.
- Tool started and completed.
- Retry attempted.
- Context built.
- Partial answer emitted.
- Terminal state reached.
- Error or cancellation recorded.

Include correlation identifiers when available, such as run id, thread id,
graph id, node name, tool call id, provider, duration, retry count, terminal
status, and error code.

## Prompt and Model Output Rules

- Prompt changes are code changes.
- Add golden cases or regression fixtures for prompt-sensitive behavior.
- Use schema validation for structured model output.
- Do not patch missing product capability with prompt wording.
- Distinguish deterministic tests, mocked provider tests, and live smoke tests.

## Verification

Test graph success, invalid model output, invalid tool output, no-tool path,
wrong-tool path, retry, timeout, cancellation, resume from checkpoint, duplicate
node execution, and terminal-state invariants.
