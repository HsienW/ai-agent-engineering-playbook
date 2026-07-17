---
name: frontend-streaming-contract
description: Design and review frontend streaming contracts. Use when handling server-sent events, WebSocket messages, async iterables, stream parsers, reducer state, terminal states, progress timelines, cancellation, retries, or UI updates driven by runtime events.
---

# Frontend Streaming Contract

## Skill Interface

- Name: frontend-streaming-contract.
- Description: Design and review frontend streaming contracts for server-sent events, WebSocket messages, async iterables, stream parsers, reducer state, terminal states, progress timelines, cancellation, retries, and UI updates driven by runtime events.
- Parameters: Stream transport, runtime event schema, parser behavior, reducer state shape, ordering and duplicate rules, terminal states, cancellation and retry behavior, UI rendering states, and verification scenarios.
- Instructions: Use this skill when frontend state or UI is driven by streamed runtime events. Treat each event as a versioned contract, validate unknown payloads, keep terminal states final, make reducers resilient to future events, and test interruption, ordering, cancellation, and timeout cases.

Treat streaming as a contract, not as ad hoc text appended to the UI. Every
event should have a stable type, runtime validation, ordering behavior, and a
defined effect on state.

## Contract Shape

Define a discriminated union for runtime events:

```ts
type RuntimeEvent =
  | { type: 'workflow.started'; runId: string; ts: number }
  | { type: 'step.started'; stepId: string; label: string; ts: number }
  | { type: 'step.progress'; stepId: string; message?: string; ts: number }
  | { type: 'step.completed'; stepId: string; durationMs?: number; ts: number }
  | { type: 'step.failed'; stepId: string; errorCode: string; ts: number }
  | { type: 'answer.delta'; delta: string; ts: number }
  | { type: 'card.emitted'; cardType: string; payload: unknown; ts: number };
```

Use a parser that accepts `unknown`, validates the event, and returns either a
known event or an explicit unknown-event fallback.

## State Rules

- Terminal states are final: `success`, `error`, `cancelled`, and `timeout`
  must not return to `running`.
- Progress events must not overwrite completed results.
- Duplicate events should be idempotent when an event id is available.
- Out-of-order events should be ignored, buffered, or reconciled by a documented
  rule.
- Reducers must handle unknown event types without crashing.
- Event state should be keyed by stable identifiers such as `runId`, `stepId`,
  `messageId`, or `toolCallId`.

## Transport Rules

- For SSE, parse event frames separately from event payloads.
- For WebSockets, validate each message before reducing it.
- For async iterables, define cleanup behavior when the consumer stops.
- Propagate cancellation to the upstream operation when supported.
- Respect backpressure when bridging streams between runtimes.

## UI Rules

- Keep parsing, validation, and event reduction outside presentational
  components.
- Show running, partial, completed, failed, cancelled, timeout, and unknown
  states explicitly.
- Do not infer machine state from localized text.
- Use fallback rendering for unknown future events.
- Keep timelines stable when events arrive late or duplicate.

## Verification

Test valid event sequences, unknown event types, duplicate events, out-of-order
events, stream interruption, cancellation, timeout, and terminal events followed
by progress.
