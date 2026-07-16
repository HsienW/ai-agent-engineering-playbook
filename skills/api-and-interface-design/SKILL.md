---
name: api-and-interface-design
description: Guide stable API and interface design. Use when creating or changing public module boundaries, REST or GraphQL endpoints, event contracts, SDK methods, data transfer objects, error envelopes, or cross-team interfaces.
---

# API and Interface Design

## Skill Interface

- Name: api-and-interface-design.
- Description: Guide stable API and interface design for public module boundaries, REST or GraphQL endpoints, event contracts, SDK methods, data transfer objects, error envelopes, and cross-team interfaces.
- Parameters: Target interface or contract, expected consumers, request and response shapes, error model, compatibility requirements, versioning expectations, and available schema or test commands.
- Instructions: Use this skill before implementing or changing a public contract. Define the contract first, check backward compatibility, make runtime validation explicit, and verify behavior with contract or schema tests.

Design contracts before implementation. Treat every public shape as something
another caller may depend on, even if the current codebase has only one caller.

## Process

1. Identify consumers, owners, and compatibility expectations.
2. Define the request, response, event, error, and versioning contract.
3. Separate domain types from transport types.
4. Validate untrusted input at runtime.
5. Add unknown-field and future-version behavior.
6. Write examples for success, validation failure, upstream failure, timeout,
   cancellation, and permission denial.
7. Verify the implementation against the contract, not against incidental
   current behavior.

## Design Rules

- Prefer additive changes over renames or removals.
- Use explicit discriminators for unions and state machines.
- Return stable error codes with human-readable messages as secondary detail.
- Avoid making display text, log messages, or exception names part of the
  machine contract.
- Do not encode natural-language understanding as keyword maps in an API layer.
- Keep idempotency, pagination, sorting, filtering, and retry semantics explicit.
- Make ownership of defaults clear: server default, client default, or product
  default.

## Compatibility Review

Before changing an interface, answer:

- Which callers can break?
- Is the change backward compatible?
- Can old and new clients coexist?
- What happens to unknown enum values or event types?
- Is migration or deprecation required?

## Verification

Use contract tests or schema tests for:

- Valid request and response.
- Missing required fields.
- Unknown fields.
- Unknown enum or union variants.
- Error envelope shape.
- Version mismatch.
- Timeout and cancellation propagation.
