---
name: ngrx-store-review
description: Reviews NgRx state management code, including actions, reducers, selectors, effects, entity adapters, and component store usage, for correctness, immutability, memoization, and side-effect handling. Use when the user asks for a review of NgRx code, a pull request touching the store, or help with state that updates wrongly, effects that run twice, or selectors that recompute too often.
license: MIT
---

# NgRx Store Review

Review the store code you were given and report concrete, verifiable findings with the
smallest correct fix. Match the repository's existing NgRx style (createActionGroup or
createAction, feature creators, file layout); repository conventions win over this guide.

## Procedure

1. Map the feature: state shape, actions, reducer, selectors, effects, and the
   components that dispatch and select.
2. Walk the checklist in [references/checklist.md](references/checklist.md).
3. Confirm each finding against the code before reporting it; drop anything you cannot
   point to.
4. Write the report in the format below.

## Key rules

- **Actions** describe events, not commands: `[Booking Page] Seat Selected`, not
  `setSeat`. One action per event source; do not reuse an action from two places.
- **Reducers** are pure and immutable: no mutation, no side effects, no API calls, no
  `Date.now()` or random values. Unknown actions return the same state reference.
- **Selectors** derive data; store only the minimum state. Use `createSelector` for
  derived values and keep projector functions pure so memoization works.
- **Effects** own side effects. Every effect that calls an API handles errors inside the
  inner observable (`catchError` returning a failure action) so one error does not kill
  the effect stream. Choose the flattening operator on purpose: `switchMap` for
  cancellable reads, `concatMap` for ordered writes, `exhaustMap` for submits.
- **Components** select with signals (`selectSignal`) or the async pipe and dispatch
  actions; they do not read state synchronously to decide what to dispatch.

## Output format

```
## Summary
<feature, overall verdict, biggest risk>

## Findings
### [Blocker | Major | Minor | Nit] <title>
Location: <file>:<line>
Problem: <what is wrong and what the user would see>
Fix: <minimal change, with a snippet>

## What looks good
<two or three specific strengths>
```

Report at most three nits. Never invent findings to fill the report.
