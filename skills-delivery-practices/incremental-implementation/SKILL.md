---
name: incremental-implementation
description: Deliver changes in small verifiable increments. Use when implementing a multi-file feature, refactor, migration, bug fix, or any task that is large enough to become risky if completed in one pass.
---

# Incremental Implementation

## Skill Interface

- Name: incremental-implementation.
- Description: Deliver changes in small verifiable increments for multi-file features, refactors, migrations, bug fixes, or any task that becomes risky when completed in one pass.
- Parameters: Target outcome, affected files or modules, current constraints, risk areas, available tests or checks, and any required compatibility or rollout conditions.
- Instructions: Use this skill when the task is broad or risky. Define the smallest useful slice, implement only that slice, verify it, fix failures before expanding scope, and report completed slices plus remaining work.

Build one complete slice at a time. Each slice should leave the workspace in a
working, testable state.

## Process

1. Define the smallest useful slice.
2. Implement only that slice.
3. Run the most relevant verification.
4. Fix failures before expanding scope.
5. Record what changed and what remains.
6. Move to the next slice.

## Slicing Strategies

- Vertical slice: one path through the stack from input to observable result.
- Contract-first slice: define types, schemas, or API shape before parallel
  implementation.
- Risk-first slice: prove the most uncertain technical point early.
- Additive slice: introduce new behavior behind safe defaults before replacing
  old behavior.

## Scope Rules

- Do not mix feature work with unrelated cleanup.
- Do not rename or reformat files outside the slice.
- Do not add abstractions before repeated use proves they are needed.
- Do not leave broken intermediate states.
- If a feature is incomplete but must coexist with production code, gate it with
  an explicit safe default.

## Verification

After each slice, run the narrowest meaningful check. At the end, run the full
checks required by the changed area. Do not repeat the same passing command
without intervening changes.

## Handoff

Report:

- Completed slices.
- Commands actually executed.
- Failures and fixes.
- Unverified areas.
- Suggested next slice.
