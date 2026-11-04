---
name: git-commit-message
description: Writes clear, conventional Git commit messages from staged changes or a description of the work, following the Conventional Commits format with a concise subject and an explanatory body. Use when the user asks for a commit message, wants to split work into logical commits, or asks to improve an existing commit message.
license: MIT
---

# Git Commit Message

Write commit messages that let a future reader understand **what** changed and **why**
without opening the diff.

## Gather context

1. Look at the staged diff (`git diff --staged`). If nothing is staged, look at the
   unstaged diff and tell the user that nothing is staged yet.
2. Check recent history (`git log --oneline -15`) to match the repository's existing
   conventions: whether it uses Conventional Commits, scopes, ticket prefixes, or
   sentence case. **Existing repository conventions take precedence over this guide.**
3. If the diff mixes unrelated changes (for example a refactor plus a bug fix), recommend
   splitting it into separate commits and propose the groupings by file or hunk.

## Format

```
<type>(<optional scope>): <subject>

<body>

<footer>
```

### Type

| Type | Use for |
|---|---|
| `feat` | A new user-facing capability |
| `fix` | A bug fix |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `perf` | Performance improvement |
| `test` | Adding or correcting tests only |
| `docs` | Documentation only |
| `build` | Build system, dependencies |
| `ci` | CI configuration |
| `chore` | Maintenance that does not fit elsewhere |
| `style` | Formatting only, no code meaning change |

### Subject line

- Imperative mood: "add", "fix", "remove" — not "added" or "adds". Test: the subject
  should complete the sentence "If applied, this commit will ...".
- At most 72 characters including the type and scope; aim for 50.
- No trailing period.
- Describe the change, not the activity ("fix race in token refresh", not "fix bug" or
  "changes").

### Body

- Separate from the subject with a blank line. Wrap at 72 characters.
- Explain **why** the change was needed and what the previous behavior was. The diff
  already shows how.
- Mention notable side effects, trade-offs, or follow-up work.
- Omit the body for trivial changes where the subject says everything.

### Footer

- Reference issues using the repository's convention (for example `Refs: #123` or
  `Closes: PROJ-42`).
- For breaking changes, add `!` after the type or scope and a `BREAKING CHANGE:` footer
  describing what consumers must change.

## Examples

```
fix(auth): refresh access token before expiry instead of after 401

Requests made in the 30 seconds before expiry could fail with 401
and were not retried, logging users out of long-running sessions.
Refresh the token proactively when less than 60 seconds remain.

Closes: #482
```

```
refactor(api): extract pagination into a shared dependency
```

```
feat(export)!: return CSV exports as streamed responses

BREAKING CHANGE: /export now returns text/csv instead of a JSON
envelope. Clients must read the response body as a stream.
```

## Output

Provide the commit message in a fenced code block, ready to paste. If you recommended
splitting the change, provide one message per proposed commit with the files each one
should include. Do not run `git commit` unless the user explicitly asks you to.
