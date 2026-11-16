# Accessibility checklist (WCAG 2.2 AA)

## Structure
- One `<h1>`, headings in order, landmarks present
- `lang` on `<html>`, `dir` where direction changes

## Keyboard and focus
- All interactive elements reachable and operable by keyboard
- Visible focus indicator on every focusable element
- Dialog: focus moves in, is contained, Escape closes, focus returns
- No positive `tabindex`

## Forms
- Every input has a visible `<label>` linked by `for`/`id`
- Required fields marked in text, not by colour only
- Errors announced and linked with `aria-describedby`

## Custom widgets
- Tabs, menus, comboboxes follow the WAI-ARIA Authoring Practices pattern
- ARIA states bound to component signals or inputs

## Media and colour
- Images have meaningful `alt` or `alt=""` when decorative
- Contrast 4.5:1 for text, 3:1 for large text and controls
- Animations respect `prefers-reduced-motion`
