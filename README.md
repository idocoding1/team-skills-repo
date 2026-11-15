# Team Skills Hub — approved skills repository

The marketplace's source of truth for published skills. Only the Team Skills Hub publisher
commits here: every commit is one approved version (`Publish <name> v<version>`).
Do not edit files by hand; changes go through Submit → Team Lead review.

```
skills/<name>/              the skill exactly as installed (SKILL.md, scripts/, references/, assets/)
metadata/<name>.yaml        marketplace metadata of the latest published version
approvals/<name>/<v>.json   one approval record per published version
```

`scope` in `metadata/<name>.yaml` is `global` (shown to every team) or `project`
(with `project: <name>`). See the marketplace repo's `docs/metadata-format.md`.

## Team

`team.json` lists the team: `leads` review and approve submissions, `members` submit them.
Team Skills Hub reads it to decide who is a Team Lead. `.github/CODEOWNERS` requests a lead's
review on every pull request; keep both in sync. Nobody can approve their own submission, so a
lead's own submissions need a second lead.

