---
name: react-code-review
description: Reviews React components and hooks for correctness, rendering performance, accessibility, and maintainability. Use when the user asks for a review of React or TSX/JSX code, a pull request touching React components, or help diagnosing re-render, state, or effect bugs.
license: MIT
---

# React Code Review

Perform a focused, evidence-based review of React code. Every finding must point at a
specific file and line (or code excerpt), explain the concrete consequence, and propose
the smallest correct fix.

## Scope

Review only the code you were given or the files changed in the pull request. Read
surrounding files (parent components, custom hooks, shared types) when you need them to
confirm a finding, but do not report issues in code outside the change unless the change
makes them worse.

## Review procedure

1. **Understand intent first.** Summarize in one or two sentences what the change is meant
   to do. If you cannot tell, say so and ask before reviewing deeply.
2. **Walk the checklist below** in order of severity. Stop listing minor items once you
   have more than ten findings; report the most important ones.
3. **Verify each finding.** Before reporting, re-read the code path and confirm the
   problem is real (for example, that a dependency really changes on every render).
   Drop anything you cannot substantiate.
4. **Write the report** using the output format at the end of this file.

## Checklist

### Correctness (blocker / major)

- **Hooks rules:** hooks called conditionally, inside loops, or after an early return.
- **Effect dependencies:** missing dependencies that cause stale closures; objects,
  arrays, or inline functions in dependency arrays that change every render and cause
  effect loops.
- **Effect cleanup:** subscriptions, timers, event listeners, `AbortController`s, and
  WebSocket connections that are never cleaned up.
- **Race conditions:** async effects that can set state after a newer request finished or
  after unmount. Recommend an `ignore` flag or `AbortController`.
- **State derivation:** state that duplicates props or other state and can drift. Prefer
  computing the value during render.
- **State updates:** updates based on previous state that do not use the functional form
  (`setCount(c => c + 1)`), and direct mutation of state objects or arrays.
- **Keys:** list items using array index as key when items can be reordered, inserted,
  or removed; missing keys; non-unique keys.
- **Controlled inputs:** inputs switching between controlled and uncontrolled
  (`value` becoming `undefined`).

### Performance (major / minor)

- Expensive computation during render without memoization, when inputs rarely change.
- Context providers whose `value` is a new object on every render, causing all consumers
  to re-render.
- `React.memo`, `useMemo`, or `useCallback` used where props change every render anyway
  (the memoization has cost but no benefit). Recommend removing it.
- Large lists rendered without virtualization (more than a few hundred rows).
- Components defined inside other components (remounts on every parent render).

### Accessibility (major)

- Clickable `div`/`span` elements without a role, keyboard handler, and focusability.
  Prefer a real `<button>` or `<a>`.
- Images without meaningful `alt` text (or `alt=""` for decorative images).
- Form fields without an associated `<label>` or `aria-label`.
- Focus not managed when dialogs open/close; missing `aria-expanded` on disclosure
  controls.
- Color as the only means of conveying state.

### Maintainability (minor / nit)

- Components longer than roughly 250 lines that mix data fetching, state management, and
  presentation. Suggest a concrete split.
- Prop drilling through three or more levels where a context or composition would be
  simpler.
- Loose types: `any`, non-null assertions without justification, missing return types on
  exported hooks.
- Dead code, commented-out code, and leftover `console.log` calls.

### Security

- `dangerouslySetInnerHTML` with data that is not sanitized.
- URLs built from user input placed in `href` or `src` without scheme validation
  (`javascript:` URLs).
- Secrets or tokens embedded in client bundles.

## Severity levels

- **Blocker:** incorrect behavior, data loss, security issue, or crash.
- **Major:** likely bug under realistic conditions, accessibility barrier, or measurable
  performance problem.
- **Minor:** maintainability issue that will slow future work.
- **Nit:** style or naming preference. Report at most three nits.

## Output format

```
## Summary
<one paragraph: what the change does and overall assessment>

## Findings
### [Severity] <short title>
Location: <file>:<line>
Problem: <what is wrong and what happens because of it>
Fix: <minimal change, with a code snippet when helpful>

## What looks good
<two or three specific strengths, if any>
```

If there are no findings at a given severity, omit that severity. Never invent findings
to fill the report.
