---
name: node-engineering
description: Guide robust Node.js and TypeScript engineering. Use when working on Node services, scripts, CLIs, tests, streams, async workflows, module boundaries, environment configuration, logging, caching, profiling, or graceful shutdown.
---

# Node Engineering

## Skill Interface

- Name: node-engineering.
- Description: Guide robust Node.js and TypeScript engineering for services, scripts, CLIs, tests, streams, async workflows, module boundaries, environment configuration, logging, caching, profiling, and graceful shutdown.
- Parameters: Runtime entry points, package scripts, environment variables, async resources, dependency boundaries, logging requirements, test commands, and representative input or fixtures.
- Instructions: Use this skill when working in Node.js or TypeScript runtimes. Make runtime contracts explicit, validate external data and environment at boundaries, manage async resources deliberately, and run the local lint, type-check, test, or build commands that apply.

Prefer explicit runtime behavior over framework assumptions. Treat process
lifetime, async resources, environment variables, and module boundaries as
production contracts.

## Core Rules

- Validate environment variables at startup and fail fast with safe messages.
- Keep secrets in environment or secret managers, never in code or logs.
- Use structured errors with stable codes for expected failures.
- Use `unknown` for external data until parsed.
- Keep module imports consistent with the project module system.
- Close servers, database clients, workers, timers, file handles, and streams in
  tests and shutdown paths.
- Bound concurrency for network, file, and CPU-heavy work.

## Async Patterns

- Use `AbortSignal` or equivalent cancellation propagation for long operations.
- Prefer `Promise.all` only when work is independent and failure behavior is
  acceptable.
- Use concurrency limits when fan-out can grow with input size.
- Avoid unhandled promises and fire-and-forget work unless supervised.

## Testing

- Make tests deterministic.
- Avoid fixed sleeps; wait for observable conditions.
- Isolate process-level state such as environment variables, timers, and global
  caches.
- Ensure tests release resources in the same scope that creates them.
- When a process hangs, inspect open handles before adding timeouts.

## Observability

- Log structured events at boundaries and failures.
- Include correlation identifiers when available.
- Do not log tokens, credentials, raw authorization headers, or sensitive
  payloads.

## Verification

Use the local package's lint, test, type-check, and build commands when they
exist. For scripts, run the smallest representative command with safe fixture
input.
