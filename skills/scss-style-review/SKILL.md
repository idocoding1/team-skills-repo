---
name: scss-style-review
description: Reviews SCSS and component styles for maintainability, design-token use, nesting depth, specificity, right-to-left support, responsive breakpoints, and Angular view encapsulation. Use when the user asks for a review of SCSS or CSS, a pull request that changes styles, or help with styles that leak between components or break in RTL layouts.
license: MIT
---

# SCSS Style Review

Review the stylesheets you were given and report concrete findings with the smallest
correct fix. Match the repository's existing conventions (variables or CSS custom
properties, mixins, file layout, BEM or other naming); they win over this guide.

## Procedure

1. Find the project's design tokens (variables, maps, or custom properties), shared
   mixins, and breakpoints before reviewing, so fixes reuse them.
2. Walk the checklist in [references/checklist.md](references/checklist.md).
3. Confirm each finding in the code; drop anything you cannot point to.
4. Write the report in the format below.

## Key rules

- **Tokens over literals:** colours, spacing, radii, font sizes, and z-indexes come
  from the design tokens. Flag raw hex values and magic numbers.
- **Shallow nesting:** at most three levels; nesting that only mirrors the DOM raises
  specificity and breaks when markup changes.
- **Low specificity:** no IDs, no `!important` except for utility overrides, no
  qualifying classes with element names (`div.card`).
- **Direction-agnostic:** use logical properties (`margin-inline-start`,
  `padding-inline`, `inset-inline-end`, `text-align: start`) so layouts work in both
  LTR and RTL. Flag `left`/`right` pairs and directional icons that do not flip.
- **Component scope:** in Angular, keep styles in the component; avoid `::ng-deep`
  and `ViewEncapsulation.None` unless styling third-party content, and scope them under
  `:host` when used.
- **Responsive:** use the project's breakpoint mixins; mobile-first `min-width`
  queries unless the project does otherwise.

## Output format

```
## Summary
<what the styles cover, overall verdict, biggest risk>

## Findings
### [Major | Minor | Nit] <title>
Location: <file>:<line>
Problem: <what is wrong and what the user would see>
Fix: <minimal change, with a snippet>
```

Report at most three nits. Never invent findings to fill the report.
