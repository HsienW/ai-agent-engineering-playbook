---
name: test-driven-development
description: Drive implementation with tests. Use when fixing bugs, changing behavior, adding logic, modifying contracts, or proving that agent-written code works and remains safe across regressions.
---

# Test-Driven Development

Use tests to prove behavior, not to document implementation trivia.

## Cycle

1. Red: write or identify a failing test that captures the required behavior or
   bug.
2. Green: make the smallest implementation change that passes.
3. Refactor: simplify while keeping tests green.
4. Regression: run adjacent existing tests that could be affected.

## Bug Fix Pattern

1. Reproduce the bug with a failing test, fixture, command, trace, or fixed
   input/output pair.
2. Confirm the failure matches the reported issue.
3. Fix the root cause.
4. Re-run the same reproduction and relevant regression tests.
5. Keep the regression test unless it is too expensive and a documented manual
   check is more appropriate.

## Test Selection

- Unit tests for deterministic logic.
- Integration tests for boundaries, adapters, persistence, and schemas.
- Contract tests for APIs, events, messages, and error envelopes.
- End-to-end tests for critical user workflows.
- Smoke tests for live providers only when environment and cost are acceptable.

## Rules

- Do not delete failing tests to get a green run.
- Do not loosen assertions unless the old assertion was wrong.
- Do not mock the core behavior being tested.
- Do not hide races with fixed sleeps.
- Do not claim unexecuted tests passed.

## Coverage Checklist

Consider:

- Normal success.
- Invalid input.
- Unknown fields or variants.
- Upstream failure.
- Permission denied.
- Timeout and cancellation.
- Retry or duplicate event.
- Partial result or degraded behavior.
