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

`team.json` lists the team: `leads` review and approve submissions, `members` submit them, and
`projects` are the customer projects a skill can belong to (`name`, optional `description`).
Team Skills Hub reads it to decide who is a Team Lead. `.github/CODEOWNERS` requests a lead's
review on every pull request; keep both in sync. Team Leads can publish their own submissions.

## Branch protection

`.github/rulesets/protect-main.json` protects `main`: changes only through pull requests, one
approval from a code owner (a Team Lead), squash merges only, no force pushes or deletion. The
repository admin may bypass the approval **for pull requests only**, so a lead (the owner) can publish
their own submission: GitHub never lets a PR's author approve it. Apply it once: **Settings → Rules → Rulesets → New ruleset →
Import a ruleset**, and pick the file. `require_last_push_approval` stays off on purpose: Team Skills
Hub pushes the approval record to the PR branch and then approves as the same Team Lead.

