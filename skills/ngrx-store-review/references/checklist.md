# NgRx review checklist

## Actions
- Event-style names with a source in brackets
- Props are serializable (no class instances, functions, or DOM objects)
- No action dispatched from inside a reducer or selector

## Reducers
- Spread or entity-adapter updates only; no `push`, `splice`, or property assignment
- Loading and error flags reset on success and failure
- Initial state covers empty, loading, and error

## Selectors
- Derived data computed in selectors, not stored
- Parameterized selectors created once (factory) rather than on every call
- Projectors do not return new arrays or objects when inputs are unchanged

## Effects
- `catchError` inside the flattening operator, returning a failure action
- Correct operator: switchMap (reads), concatMap (ordered writes), exhaustMap (submits)
- Non-dispatching effects marked `{ dispatch: false }`
- No subscriptions inside effects; no nested `subscribe`

## Components
- `selectSignal` or async pipe; no manual subscribe without cleanup
- No reading store state synchronously to branch on before dispatching

## Tests
- Reducers tested with plain inputs and outputs
- Effects tested with mocked services and marble or observer tests
- Selectors tested through their projectors
