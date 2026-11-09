---
name: sql-style-guide
description: Writes and reviews SQL queries using the team's formatting and safety conventions, covering keyword casing, indentation, explicit joins, parameterization, and index-friendly predicates. Use when the user writes, formats, or reviews SQL or asks why a query is slow or unsafe.
license: MIT
---

# SQL Style Guide

Apply these conventions when writing new SQL or reviewing existing queries. When
reviewing, quote the original line and show the corrected version.

## Formatting

- Uppercase SQL keywords (`SELECT`, `FROM`, `WHERE`, `JOIN`); lowercase identifiers.
- One clause per line; indent continuation lines by four spaces.
- List selected columns explicitly. Avoid `SELECT *` outside ad-hoc exploration.
- Use explicit `JOIN ... ON ...` syntax; never comma joins.
- Alias tables with short, meaningful names (`orders o`, not `orders a`).

## Safety

- Never build SQL by concatenating user input. Use bound parameters.
- Every `UPDATE` and `DELETE` must have a `WHERE` clause; flag any that do not.
- Wrap multi-statement changes in a transaction.

## Performance

- Avoid functions on indexed columns in `WHERE` (`WHERE created_at >= :start`, not
  `WHERE DATE(created_at) = :day`).
- Prefer `EXISTS` over `IN (subquery)` for large correlated checks.
- Paginate with keyset pagination for large tables instead of large `OFFSET` values.

See [references/examples.md](references/examples.md) for before/after examples.
