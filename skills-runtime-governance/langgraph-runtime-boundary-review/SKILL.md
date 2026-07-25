---
name: langgraph-runtime-boundary-review
description: Review LangGraph native runtime boundaries before building custom agent runtime infrastructure. Use when deciding whether queueing, workers, checkpointers, stores, interrupts, resume flows, retries, task semantics, audit, locks, or cost tracking should be native runtime responsibilities or custom application runtime responsibilities.
---

# LangGraph Runtime Boundary Review

## Skill Interface

- Name: langgraph-runtime-boundary-review.
- Description: Review LangGraph native runtime boundaries before building custom agent runtime infrastructure for queueing, workers, checkpointers, stores, interrupts, resume flows, retries, task semantics, audit, locks, and cost tracking.
- Parameters: Proposed runtime capability, native LangGraph coverage, graph execution requirements, business task semantics, side-effect governance, audit needs, memory model, UI progress needs, evidence from a minimal run, and ownership decision.
- Instructions: Use this skill before adding custom runtime layers. Identify the capability, test native runtime coverage when possible, assign ownership to native runtime, custom task runtime, application service, or UI, and record rationale with evidence.

Clarify native runtime responsibilities before adding custom runtime layers.
Fill real gaps without duplicating queue, worker, checkpoint, or store behavior
that the runtime already provides.

## Review Process

1. List the capability being proposed.
2. Identify whether it belongs to graph execution, business task state,
   side-effect governance, audit, memory, or user-facing progress.
3. Verify native runtime coverage with a minimal run when possible.
4. Decide ownership: native runtime, custom task runtime, application service,
   or UI.
5. Record the rationale and evidence.

## Boundary Matrix

Evaluate these capabilities explicitly:

- Graph state persistence.
- Interrupt and resume.
- Background queue and worker behavior.
- Thread-scoped checkpointing.
- Cross-thread long-term memory.
- Business task and step state.
- Step-level retry budget.
- Side-effect idempotency.
- Persistent audit.
- Distributed concurrency control.
- Cost tracking.
- User-facing progress timeline.

## Decision Rules

- Prefer native runtime features for graph execution state.
- Use custom runtime state for business task and step semantics.
- Use custom governance for side effects, audit, idempotency, compensation,
  distributed locks, and cost policy.
- Keep long-term memory distinct from checkpointed graph execution state.
- Do not store non-serializable resources in graph state.
- Do not treat a graph node name as a business step unless that contract is
  explicit and stable.

## Evidence

Capture:

- Minimal run configuration.
- Interrupt and resume behavior.
- Checkpoint data ownership.
- Store read and write behavior.
- Failure and recovery behavior.
- The capability gap that justifies any custom runtime component.

## Output

Produce a short decision record with:

- Capability.
- Native coverage.
- Custom responsibility, if any.
- Rationale.
- Verification evidence.
- Risks and follow-up tests.
