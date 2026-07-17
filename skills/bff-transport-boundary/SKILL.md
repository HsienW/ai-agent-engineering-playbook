---
name: bff-transport-boundary
description: Design and review Backend-for-Frontend transport boundaries. Use when implementing or changing request parsing, response mapping, streaming proxy behavior, CORS, header allowlists, authentication boundaries, timeouts, cancellation, error envelopes, or upstream orchestration.
---

# BFF Transport Boundary

## Skill Interface

- Name: bff-transport-boundary.
- Description: Design and review Backend-for-Frontend transport boundaries for request parsing, response mapping, streaming proxy behavior, CORS, header allowlists, authentication boundaries, timeouts, cancellation, error envelopes, and upstream orchestration.
- Parameters: BFF route or transport boundary, inbound request shape, upstream targets, authentication and authorization context, header policy, streaming behavior, timeout and cancellation rules, error envelope shape, and verification commands.
- Instructions: Use this skill before implementing or changing BFF transport behavior. Keep orchestration and transport safety in the BFF, validate untrusted inputs, propagate cancellation, bound streaming resources, map errors explicitly, and verify success plus failure paths.

A BFF owns transport safety and orchestration. It should not absorb product
reasoning, model prompts, workflow planning, or domain decisions that belong to
backend services.

## Ownership

The BFF owns:

- Request parsing and runtime validation.
- Authentication and authorization boundaries.
- Header allowlists.
- CORS and security headers.
- Request-size limits and rate limits.
- Timeout and cancellation propagation.
- Upstream request orchestration.
- Stream forwarding and backpressure.
- Transport error mapping.
- Request tracing and audit metadata.

The BFF does not own:

- Model reasoning.
- Prompt construction.
- Agent planning.
- Workflow graph routing.
- Natural-language intent classification.
- Tool implementation details.

## Request Rules

- Treat headers, cookies, query parameters, route parameters, body fields,
  multipart metadata, filenames, MIME types, and forwarded-address headers as
  untrusted.
- Validate at the transport boundary.
- Do not forward arbitrary inbound headers upstream.
- Do not let clients control upstream hosts, internal URLs, authorization
  targets, trace ownership, or filesystem paths.

## Streaming Rules

- Do not buffer unbounded upstream streams in memory.
- Respect backpressure.
- Do not write after the response is closed or destroyed.
- Release listeners, timers, and readers on every terminal path.
- When the downstream client disconnects, abort upstream work when supported.

Define terminal reasons:

- `completed`
- `client_cancelled`
- `client_disconnected`
- `bff_timeout`
- `upstream_timeout`
- `upstream_error`
- `malformed_upstream_event`
- `process_shutdown`

## Error Contract

Separate HTTP status, stable error code, developer message, user message key,
retryability, and terminal reason.

Never expose stack traces, internal URLs, provider responses, authorization
headers, cookies, tokens, or raw internal exception names.

## Observability

Include request id, trace id, route, method, status, upstream target category,
duration, bytes in/out, terminal reason, and error code where relevant.

## Verification

Test success, invalid request, forbidden request, malformed upstream response,
client disconnect, timeout, cancellation, upstream error, duplicate events,
backpressure behavior, and cleanup of listeners and timers.
