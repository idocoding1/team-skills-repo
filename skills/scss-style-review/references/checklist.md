# SCSS review checklist

## Tokens
- Raw colours, spacing, font sizes, radii, shadows, or z-indexes instead of tokens
- Duplicate values that should be one token

## Structure
- Nesting deeper than three levels
- Selectors that mirror the DOM tree
- Unused selectors or rules overridden immediately below

## Specificity
- IDs, `!important`, element-qualified classes
- Long selector chains to win a specificity fight

## Direction (RTL)
- `margin-left`/`margin-right`, `padding-left`/`padding-right`, `left`/`right`, `float`
- `text-align: left` or `right` instead of `start` or `end`
- Arrows or chevrons that should mirror in RTL

## Angular
- `::ng-deep` without a `:host` scope
- `ViewEncapsulation.None` on a regular component
- Global styles placed in a component stylesheet

## Responsive and accessibility
- Fixed widths that overflow small screens
- Focus outlines removed without a visible replacement
- Text colour contrast that fails against its background
