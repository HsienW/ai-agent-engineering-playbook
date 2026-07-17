---
name: tool-renderer-contract
description: Design and review structured tool-result rendering. Use when rendering tool calls, tool messages, structured tool outputs, status cards, fallback JSON/text displays, parser boundaries, display status mappings, or unknown tool results.
---

# Tool Renderer Contract

## Skill Interface

- Name: tool-renderer-contract.
- Description: Design and review structured tool-result rendering for tool calls, tool messages, structured tool outputs, status cards, fallback JSON or text displays, parser boundaries, display status mappings, and unknown tool results.
- Parameters: Tool result schema, raw message content, parser boundary, display status mapping, known specialized renderers, fallback rendering behavior, sensitive field policy, and verification cases.
- Instructions: Use this skill when rendering tool output in UI. Parse raw output as untrusted data, validate minimum structured fields, map status through a closed display union, provide safe fallbacks for unknown tools, and verify malformed, future-version, and sensitive-data cases.

Tool rendering should be schema-driven and resilient to unknown tools. The UI
must not trust raw tool output as display-ready data.

## Layers

1. Message container: owns message grouping and association between tool call
   metadata and tool output.
2. Generic tool renderer: parses common metadata, displays status, and chooses a
   specialized renderer when one is available.
3. Specialized renderer: renders a known tool result schema.
4. Fallback renderer: safely displays unknown JSON, text, or parse failures.

## Structured Result Shape

Use a generic shape that can be specialized:

```ts
type ToolResult<TStatus extends string, TData = unknown> = {
  schemaVersion: string;
  tool: string;
  status: TStatus;
  data?: TData;
  summary?: string;
  errorCode?: string;
};
```

The frontend parser should accept `unknown` or raw message content, validate the
minimum fields, and return a typed result or `undefined`.

## Status Mapping

Map tool status through a closed display status union:

```ts
type ToolDisplayStatus =
  | 'running'
  | 'success'
  | 'needs_input'
  | 'not_found'
  | 'denied'
  | 'error'
  | 'timeout'
  | 'cancelled'
  | 'unknown';
```

Use a single `Record<ToolDisplayStatus, DisplayConfig>` for labels, icons,
colors, and ARIA text. Do not scatter status rendering across component
branches.

## Safety Rules

- Treat tool output as untrusted.
- Never render raw HTML from tool output unless sanitized by a trusted policy.
- Never expose stack traces, credentials, provider secrets, or raw internal
  payloads.
- Do not infer status from user-visible text.
- Do not hardcode one tool result as the only rendering path.
- Unknown tools must have a safe fallback.

## Parser Rules

- Validate `schemaVersion`, `tool`, and `status`.
- Preserve unknown fields for diagnostics only when safe.
- Be forward compatible with unknown minor schema versions.
- Provide unknown-status fallback.
- Keep type assertions behind runtime checks.

## Verification

Test known structured results, unknown tool results, malformed JSON, plain text
output, unknown status, future schema versions, error envelope rendering, and
sensitive field redaction.
