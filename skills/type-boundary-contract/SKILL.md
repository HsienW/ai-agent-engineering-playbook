---
name: type-boundary-contract
description: Design and review type contracts across package, service, frontend, backend, tool, event, and transport boundaries. Use when defining schemas, discriminated unions, runtime parsers, error envelopes, schemaVersion fields, or duplicated transport/domain types.
---

# Type Boundary Contract

## Skill Interface

- Name: type-boundary-contract.
- Description: Design and review type contracts across package, service, frontend, backend, tool, event, and transport boundaries, including schemas, discriminated unions, runtime parsers, error envelopes, schemaVersion fields, and duplicated transport or domain types.
- Parameters: Boundary owner, producer and consumer types, domain and transport shapes, runtime parser, schema version, unknown-field behavior, error envelope, compatibility expectations, and contract test scenarios.
- Instructions: Use this skill before defining or changing cross-boundary types. Treat boundary types as runtime contracts, separate domain and transport types, validate external data on receipt, preserve compatibility, and test valid, invalid, unknown, and future-version payloads.

Types at a boundary are contracts. Compile-time TypeScript types do not validate
runtime data from clients, servers, tools, providers, files, queues, or models.

## Boundary Process

1. Identify the boundary owner.
2. Separate domain type from transport type.
3. Define runtime validation at the receiving boundary.
4. Define versioning and unknown-field behavior.
5. Define error envelope shape.
6. Add contract tests for both valid and invalid data.
7. Document compatibility expectations before changing existing fields.

## Type Rules

- Use discriminated unions for state, event, and result variants.
- Use `unknown` for external data until parsed.
- Avoid `any` at boundaries.
- Do not use type assertions to hide unvalidated data.
- Avoid optional fields when a union variant would model required state better.
- Keep stable machine identifiers separate from localized messages.
- Use explicit schema versions for structured cross-boundary payloads.

## Generic Result Pattern

```ts
type BoundaryResult<TKind extends string, TStatus extends string, TData> = {
  schemaVersion: string;
  kind: TKind;
  status: TStatus;
  data?: TData;
  summary?: string;
};
```

The producer may use narrow literal types. The consumer may accept wider
compatible types when it must tolerate future versions.

## Error Envelope

Use stable machine-readable fields:

```ts
type ErrorEnvelope = {
  error: {
    source: string;
    stage: string;
    code: string;
    message: string;
    retryable?: boolean;
    details?: Record<string, unknown>;
  };
};
```

Do not expose stack traces, private paths, tokens, secrets, or raw provider
responses.

## Compatibility Checklist

- Request schema.
- Response schema.
- Event schema.
- Tool input schema.
- Tool output schema.
- Error code and retryability.
- Terminal state.
- Correlation identifiers.
- Unknown fields and unknown variants.
- Migration or deprecation plan.

## Verification

Test valid data, missing fields, unknown fields, unknown variants, invalid
schema versions, error envelopes, and future-compatible payloads.
