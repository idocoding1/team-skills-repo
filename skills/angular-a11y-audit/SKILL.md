---
name: angular-a11y-audit
description: Audits Angular templates and components for accessibility against WCAG 2.2 AA, covering semantics, keyboard access, focus management, labels, ARIA, colour contrast, and right-to-left support. Use when the user asks for an accessibility review, a pull request touches templates or forms, or a screen reader or keyboard user reports a problem.
license: MIT
---

# Angular Accessibility Audit

Review the given templates and components for accessibility barriers and report each one
with the WCAG criterion, the affected users, and the smallest correct fix.

## Procedure

1. List the interactive elements in scope: buttons, links, form controls, dialogs,
   menus, tabs, and custom widgets.
2. Walk the checklist in [references/checklist.md](references/checklist.md).
3. Prefer fixing with native HTML (a real `<button>`, `<label for>`) over adding ARIA.
4. Confirm each finding in the code; drop anything you cannot point to.

## Key rules

- **Semantics first:** clickable things are `<button>` or `<a href>`; headings follow
  order; lists use list elements; landmarks (`<main>`, `<nav>`) exist once each.
- **Keyboard:** everything usable with a mouse works with Tab, Enter, Space, Escape, and
  arrow keys where the pattern requires; no keyboard traps; visible focus.
- **Focus management:** dialogs move focus in and return it on close; route changes
  move focus to the new page heading or main content.
- **Names and labels:** every control has an accessible name; icon-only buttons get
  `aria-label`; errors are linked with `aria-describedby` and announced.
- **ARIA:** only where native HTML cannot express it; states (`aria-expanded`,
  `aria-selected`) bound to the real component state.
- **Contrast and motion:** text 4.5:1 (3:1 for large text and UI parts); respect
  `prefers-reduced-motion`.
- **Direction:** layout and icons work in RTL; `dir` set on mixed-direction content.

## Output format

```
## Summary
<scope, overall verdict, most serious barrier>

## Findings
### [Critical | Serious | Moderate | Minor] <title> (WCAG <number>)
Location: <file>:<line>
Who is affected: <keyboard, screen reader, low vision, ...>
Problem: <what fails>
Fix: <minimal change, with a snippet>
```
