---
name: frontend-ui-engineering
description: Build production-quality frontend interfaces. Use when creating or modifying UI components, layouts, interaction states, responsive behavior, accessibility, visual polish, or user-facing workflows.
---

# Frontend UI Engineering

Build the real usable experience first. Match the product context: operational
tools should be efficient and scannable; creative or consumer experiences can be
more expressive.

## Component Process

1. Identify the user workflow and primary task.
2. Reuse the existing design system, component patterns, icons, spacing, and
   state conventions.
3. Model states explicitly: loading, empty, success, partial, error, disabled,
   permission denied, offline, and long content.
4. Keep data parsing and transport concerns outside presentational components.
5. Add accessibility semantics for labels, focus order, keyboard behavior, and
   status updates.
6. Verify desktop and mobile layouts with realistic content lengths.

## Layout Rules

- Use stable dimensions for fixed-format controls, grids, boards, counters, and
  toolbars.
- Avoid nested cards and decorative wrappers that reduce scanability.
- Do not let text overlap, truncate critical information, or resize controls
  unexpectedly.
- Prefer familiar icon buttons for common commands when the icon is clear.
- Use tooltips for unfamiliar icons.
- Keep display type reserved for true page-level hierarchy.

## State Rules

- Derive UI state from typed data and explicit status fields.
- Do not infer backend semantics from display text.
- Keep optimistic updates reversible.
- Make destructive actions explicit and recoverable when possible.
- Show partial progress without hiding errors.

## Verification

Check:

- Responsive behavior at narrow and wide widths.
- Long labels, long values, and missing data.
- Keyboard navigation and focus visibility.
- Loading, empty, error, and permission states.
- Console errors and hydration warnings when applicable.
