---
name: performance-optimization
description: Optimize application performance with evidence. Use when latency, throughput, memory, bundle size, rendering speed, Core Web Vitals, query time, startup time, or resource usage matters.
---

# Performance Optimization

## Skill Interface

- Name: performance-optimization.
- Description: Optimize application performance with evidence when latency, throughput, memory, bundle size, rendering speed, Core Web Vitals, query time, startup time, or resource usage matters.
- Parameters: Target metric, acceptable threshold, slow path reproduction, realistic input, baseline measurement, profiling or tracing data, constraints, and correctness or regression tests.
- Instructions: Use this skill before performance changes. Measure first, locate the real bottleneck, make the smallest targeted change, compare before and after with the same method, and report residual validation risk.

Measure before optimizing. Optimize the bottleneck that affects users or system
capacity, then measure again.

## Workflow

1. Define the target metric and acceptable threshold.
2. Reproduce the slow path with realistic input.
3. Capture a baseline measurement.
4. Locate the bottleneck with profiling, tracing, logs, database explain output,
   bundle analysis, or runtime metrics.
5. Make the smallest change that addresses the bottleneck.
6. Compare before and after using the same measurement method.
7. Check correctness and regression risk.

## Common Areas

- Network waterfalls and unnecessary sequential awaits.
- Large bundles and eager imports.
- Expensive rendering, avoidable re-renders, and unstable props.
- Repeated parsing, sorting, filtering, or serialization.
- Inefficient queries, missing indexes, and excessive round trips.
- Unbounded concurrency or memory growth.
- Cache misses, cache stampedes, and stale invalidation rules.

## Rules

- Do not trade correctness or security for speed.
- Do not add caching without ownership, invalidation, and memory bounds.
- Do not optimize rare paths while the primary bottleneck is unknown.
- Do not rely on microbenchmarks for user-visible flows without validation.
- Keep performance changes local and reversible.

## Verification

Report:

- Baseline.
- Change made.
- New measurement.
- Test commands actually executed.
- Residual risk, such as missing production traffic validation.
