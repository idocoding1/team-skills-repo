---
name: angular-component-review
description: Reviews Angular standalone components for signal usage, change detection, template correctness, subscriptions, accessibility, and folder conventions. Use when the user asks for a review of an Angular component, a pull request that touches components, or help with a component that re-renders too often or leaks subscriptions.
license: MIT
---

# Angular Component Review

Review the component files you were given (`.component.ts`, `.component.html`, styles
and spec) and report concrete, verifiable findings with the smallest correct fix.

## Procedure

1. State in one sentence what the component is for. If unclear, ask before reviewing.
2. Read the component class, then its template, then its spec.
3. Walk the checklist in [references/checklist.md](references/checklist.md) in order.
4. Confirm each finding against the code before reporting it. Drop anything you cannot
   point to.
5. Write the report in the format below.

## Key rules

- **State:** prefer `signal()`, `computed()` and `input()` / `output()` over mutable
  fields and decorators in new code. Derived values belong in `computed()`, not in
  `effect()` that writes another signal.
- **Change detection:** components should use `ChangeDetectionStrategy.OnPush`.
  Flag template method calls that do real work on every check.
- **Subscriptions:** every manual `subscribe()` needs `takeUntilDestroyed()` or must be
  replaced with `toSignal()` or the `async` pipe.
- **Templates:** use `@if` / `@for` with a stable `track` expression. Never
  `track $index` for lists that can be reordered or filtered.
- **Accessibility:** clickable elements are `<button>` or `<a>`; images have `alt`;
  form controls have labels.
- **Conventions:** one component per folder, `name.component.ts` with class
  `NameComponent`, spec next to the file it tests. Repository conventions win over this
  guide.

## Output format

```
## Summary
<what the component does and the overall verdict>

## Findings
### [Blocker | Major | Minor | Nit] <title>
Location: <file>:<line>
Problem: <what is wrong and its effect>
Fix: <minimal change, with a snippet when helpful>

## What looks good
<two or three specific strengths>
```

Report at most three nits. Never invent findings to fill the report.
