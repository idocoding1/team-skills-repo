---
name: angular-unit-tests
description: Writes and reviews unit tests for Angular standalone components, services, and pipes using the project's test runner, covering inputs and outputs, signals, HTTP calls, and empty, loading, and error states. Use when the user asks for tests for an Angular file, wants to raise coverage, or asks why a spec is flaky or fails.
license: MIT
---

# Angular Unit Tests

Write focused, readable specs that test behaviour a user or caller can observe, not
implementation details.

## Before writing

1. Find the test runner and conventions from the repository: `package.json` scripts,
   `angular.json` test target, and two or three existing `*.spec.ts` files. Match their
   style (describe naming, helpers, `TestBed` setup). Repository conventions win over
   this guide.
2. Read the file under test and list its observable behaviour: rendered output, emitted
   outputs, service calls, navigation, and error handling.
3. Place the spec next to the file it tests, named `<file>.spec.ts`.

## What to cover

- **Happy path:** the main thing the unit does.
- **States:** empty, loading, and error, when the unit has them.
- **Inputs and outputs:** set inputs with `fixture.componentRef.setInput()`, assert
  outputs by subscribing or spying on the emitter.
- **Signals:** set source signals and assert derived values after
  `fixture.detectChanges()`.
- **HTTP:** use `provideHttpClient()` with `provideHttpClientTesting()` and
  `HttpTestingController`; assert method, URL, and body, then `verify()` in `afterEach`.
- **Edge cases** found in the code: guards, early returns, and boundary values.

See [references/patterns.md](references/patterns.md) for setup snippets.

## Rules

- One behaviour per `it`, named as a sentence: `it('shows an error when loading fails')`.
- Query the DOM by role, label, or text the user sees; avoid deep CSS selectors.
- No real timers or network: use fake timers and testing providers.
- Do not test private methods or framework internals.
- Never weaken an assertion to make a failing test pass; report the bug instead.

## Output

Return the complete spec file, then a short list of what is covered and anything left
untested on purpose. When reviewing existing specs, report each problem with its
location and a corrected snippet.
