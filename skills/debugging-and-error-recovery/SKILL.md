---
name: debugging-and-error-recovery
description: Guide systematic root-cause debugging. Use when tests fail, builds break, runtime behavior is unexpected, logs show errors, or repeated fixes are not resolving the same issue.
---

# Debugging and Error Recovery

Prove the failure before changing code. Keep the investigation narrow until the
failure layer is known.

## Triage

1. Capture the exact symptom, command, input, output, timestamp, and environment.
2. Reproduce with the smallest reliable case.
3. Identify the failing layer: UI rendering, state management, transport,
   validation, domain logic, persistence, provider, tool execution, synthesis,
   or infrastructure.
4. Compare expected and actual structured data.
5. Form one hypothesis and test it.
6. Fix the root cause.
7. Re-run the original reproduction and adjacent regression checks.

## Stop Rules

Stop local patching and re-evaluate when:

- Two fixes fail to resolve the same symptom.
- A fix for one case breaks another case.
- The failure appears to move between layers.
- Passing requires more special cases or prompt examples.
- Mocks pass but real runtime behavior still fails.

## Evidence to Preserve

- Failing test output.
- Minimal input and actual output.
- Relevant logs or traces.
- Diff between previous and current structured data.
- The command used to verify the fix.

## Recovery Rules

- Do not delete failing tests to get green output.
- Do not weaken assertions without explaining why the old expectation was wrong.
- Do not ignore caught errors unless the behavior is intentional and tested.
- Do not mask race conditions with fixed sleeps.
- Do not claim a live integration is fixed when only a mock was tested.

## Final Check

Report what failed, what changed, what evidence proves the fix, and what remains
unverified.
