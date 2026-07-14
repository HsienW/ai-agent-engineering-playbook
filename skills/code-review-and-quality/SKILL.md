---
name: code-review-and-quality
description: Conduct multi-axis code review. Use before merging code written by an agent or human, when assessing diffs for correctness, maintainability, security, tests, observability, and contract risk.
---

# Code Review and Quality

Review for bugs first. Summaries are secondary to findings with evidence.

## Review Order

1. Correctness: Does the implementation satisfy the requirement in real edge
   cases?
2. Contract safety: Did public APIs, events, schemas, errors, or state machines
   change safely?
3. Security: Are untrusted inputs, credentials, authorization, and data exposure
   handled correctly?
4. Reliability: Are timeouts, retries, cancellation, concurrency, and partial
   failure handled?
5. Tests: Do tests prove the new behavior and protect important regressions?
6. Maintainability: Is the code simple, local, readable, and consistent with
   nearby patterns?
7. Observability: Can production behavior be diagnosed without leaking secrets?

## Severity

- Blocker: security issue, data loss, contract break, build failure, core flow
  failure, or a change that cannot safely ship.
- Major: likely edge-case failure, incomplete error handling, missing regression
  test for meaningful behavior, or responsibility drift across layers.
- Minor: naming, duplication, readability, or low-risk maintainability issue.

## Finding Format

For every finding include:

- Severity.
- File and line when available.
- Problem.
- Triggering scenario.
- Consequence.
- Suggested fix.
- Related contract, requirement, or invariant.

## Review Discipline

- Do not review only the happy path.
- Do not assume generated code is correct.
- Do not request broad refactors when a local fix is enough.
- Do not mark a concern as resolved without evidence.
- Call out unexecuted verification separately from failed verification.

## No Findings

If no issues are found, state that clearly and list residual risk, such as
untested live integrations, missing load tests, or unavailable environment
checks.
