---
name: agent-role-boundary-preflight
description: Check an agent's role, lifecycle ownership, target scope, and authorization immediately before any side effect. Use in multi-agent or role-separated workflows before writing code, specifications, configuration, tests, documentation, runtime artifacts, version control, or external systems, and whenever a task shifts from analysis, planning, or review into execution.
---

# Agent Role Boundary Preflight

Tool access is capability, not authority. Run this preflight immediately before
every side effect. Repeat it when the task mode, role, phase, owner, target, or
requested effect changes.

## Required Inputs

Resolve these values from authoritative project rules and current workflow
state. Do not infer missing values from the model name, host, available tools,
or earlier conversation momentum.

- `role`: the actor role assigned for the current phase.
- `phase`: the current lifecycle phase, if the workflow has one.
- `owner`: the role allowed to act in that phase.
- `action`: the exact operation and its side effects.
- `target`: the file, resource, repository, branch, runtime artifact, or external
  system that would change.
- `authoritySource`: the rule, state record, or explicit role reassignment that
  grants the action.
- `handoff`: the active handoff and its permitted scope, when applicable.

## Preflight Procedure

1. Classify the next tool call as read-only or side-effecting. Treat writes,
   edits, shell commands, version-control mutations, generated artifacts,
   messages, deployments, and external API mutations as side effects.
2. Resolve the actual role. A coordinator, planner, reviewer, implementer, and
   verifier may run in the same host while holding different permissions.
3. Resolve phase and owner from the workflow's canonical state. If no lifecycle
   state exists, use the closest authoritative role and scope rules.
4. Compare the exact action and target with the role's positive write scope and
   explicit prohibitions.
5. Check the user's message precisely. Approval of a decision, finding, plan,
   or desired outcome does not reassign execution. Treat reassignment as valid
   only when it is explicit and allowed by higher-priority project policy.
6. Apply the decision table below. Missing, stale, malformed, contradictory, or
   unverifiable authority fails closed.
7. Record a compact decision before invoking the tool. Re-run this procedure
   instead of reusing an earlier decision when anything relevant changes.

## Decision Table

| Condition | Decision | Required response |
| --- | --- | --- |
| Action is read-only and within the role's read scope | `ALLOW_READ` | Continue without mutation |
| Role, phase, owner, target, and action all match authoritative scope | `ALLOW_WRITE` | Execute only the checked action |
| Another role owns the action | `HANDOFF` | Produce a structured handoff; change nothing |
| Authority data is missing, invalid, stale, or contradictory | `HANDOFF` | Route to the coordinator or authority owner; change nothing |
| Tool side effects cannot be bounded or classified | `HANDOFF` | Do not invoke the tool |
| User approved a direction but did not validly reassign execution | `HANDOFF` | Preserve the decision in the handoff; change nothing |

Edit size never changes the result. A one-line change requires the same role
authorization as a large change.

## Compact Check Record

Use this internal or chat-visible record when the host supports it:

```yaml
roleBoundaryCheck:
  role: reviewer
  phase: implementation_review
  owner: implementer
  action: edit
  target: src/api/users.ts
  authoritySource: workflow-state
  decision: HANDOFF
  reason: implementation is owned by another role
```

Do not treat this record as authority. It only explains how authority was
evaluated.

## Structured Handoff

When the decision is `HANDOFF`, return enough information for the owning role to
continue without repeating the analysis:

```yaml
handoff:
  fromRole: reviewer
  toRole: implementer
  task: Correct the validated response mapping.
  reason: The reviewer found the issue but does not own implementation.
  scope:
    - src/api/users.ts
    - src/api/users.test.ts
  evidence:
    - Finding F-2
    - Failing case: unknown response field is discarded
  constraints:
    - Preserve the public response schema
    - Do not change unrelated modules
  acceptance:
    - Regression test passes
    - Existing package checks pass
```

After producing the handoff, do not make a temporary, convenient, or partial
edit.

## Enforcement Guidance

Prompt instructions are necessary but cannot be the only control. When the host
supports pre-tool hooks or policy middleware, mirror the same decision at the
tool boundary:

```text
if authority evidence is missing or invalid:
    deny and require handoff
if role != owner for this action and target:
    deny and require handoff
if the tool's side effects cannot be bounded:
    deny and require handoff
allow only the action and target that were checked
```

Prefer a narrow allowlist for side-effecting tools. Cover alternate write paths
such as shell commands, batch edits, notebooks, version-control commands, MCP
tools, and external APIs. A newly added tool is untrusted until its effects are
classified.

## Verification

Test the policy as executable behavior, not only as prose. Include:

- a one-line out-of-role edit;
- a valid role with the wrong phase or owner;
- missing, malformed, stale, and terminal workflow state;
- valid in-role writes to the exact permitted target;
- shell, batch-edit, notebook, MCP, and external-action bypass attempts;
- a user decision that does not reassign execution;
- task drift from read-only analysis into implementation;
- hook or policy-engine failure, which must deny rather than silently allow.

The check is successful only when denied operations leave the target unchanged
and produce a usable handoff.

For the incident pattern behind this guidance, read the [role override
postmortem](references/role-override-postmortem-zh-tw.md).
