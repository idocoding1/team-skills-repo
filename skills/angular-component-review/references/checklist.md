# Angular component review checklist

## Correctness
- Inputs read in the constructor before they are set
- `effect()` writing signals that a `computed()` should derive
- Missing `track` in `@for`, or `track $index` on reorderable lists
- Forms: controls switching between enabled and disabled without updating validators

## Performance
- Default change detection instead of `OnPush`
- Expensive pipes or method calls in templates
- Large lists without virtual scrolling

## Resource leaks
- `subscribe()` without `takeUntilDestroyed()`
- `setInterval` / event listeners without cleanup in `DestroyRef.onDestroy`

## Accessibility
- Clickable `div` / `span`
- Icon-only buttons without `aria-label`
- Dialogs that do not move or restore focus

## Tests
- Spec only checks that the component is created
- No test for the empty, loading and error states
