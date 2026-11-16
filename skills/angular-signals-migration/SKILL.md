---
name: angular-signals-migration
description: Migrates Angular components and services to signals, replacing @Input and @Output decorators, BehaviorSubject state, and manual subscriptions with input(), output(), model(), signal(), computed(), and toSignal(), one safe step at a time. Use when the user asks to convert a component or service to signals, modernize older Angular code, or remove subscription boilerplate.
license: MIT
---

# Angular Signals Migration

Convert the given component or service to signals without changing its behaviour. Work
in small steps that each compile and keep the existing tests passing.

## Before changing code

1. Check the Angular version in `package.json`. `input()`, `output()`, and `model()` need
   Angular 17.1 or later; `linkedSignal()` and `resource()` need newer versions. Do not
   use APIs the project's version lacks.
2. Read the component, its template, its spec, and every parent that binds to it, so
   input and output names stay the same.
3. Note which callers subscribe to the class's observables; keep an observable API for
   them where needed (`toObservable()`).

## Migration order

1. **Inputs:** `@Input() x` becomes `x = input<T>()`; required inputs use
   `input.required<T>()`; `@Input() set x(v)` logic moves to `computed()` or an
   `effect()` only if it has side effects. Templates read `x()`.
2. **Outputs:** `@Output() changed = new EventEmitter<T>()` becomes
   `changed = output<T>()`; two-way bindings (`x` + `xChange`) become `x = model<T>()`.
3. **Local state:** `BehaviorSubject` and mutable fields read by the template become
   `signal()`; derived values become `computed()`.
4. **Subscriptions:** observables read only by the template become `toSignal()`; remove
   the `subscribe()` calls and their teardown.
5. **Change detection:** once the template reads only signals and inputs, set
   `ChangeDetectionStrategy.OnPush` if it is not set already.

See [references/before-after.md](references/before-after.md) for worked examples.

## Rules

- Keep public names identical so parent templates do not change.
- Never write a signal inside `computed()`; avoid `effect()` for derived state.
- Do not mix the migrated and old style for the same field.
- Update the spec in the same step: set inputs with `fixture.componentRef.setInput()`.

## Output

Return each changed file in full, then a short list of what was migrated, what was left
as observables on purpose, and any follow-up for parent components.
