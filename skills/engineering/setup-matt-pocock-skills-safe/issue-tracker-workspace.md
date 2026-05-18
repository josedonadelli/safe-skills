# Issue tracker: Local Workspace

Issues and PRDs for this repo live as markdown files under `.workspace/issues/`. No external service is called — no `gh`, no `glab`, no network requests.

## Conventions

- **Create an issue**: create a new markdown file at `.workspace/issues/<feature>/<slug>.md` using the issue template below
- **Read an issue**: read the file at the path the user provides or search by feature/slug
- **List issues**: `find .workspace/issues -name "*.md" | sort`
- **Update an issue**: edit the file in place
- **Close an issue**: add a `## Status: closed` section to the file with a reason

The `.workspace/` folder is git-ignored. Issues are shared through approved channels outside git (email, internal systems, or teammates replicating the workspace setup).

## Issue file template

```markdown
# <title>

## Status

open | closed

## What to build

A concise description of this vertical slice.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2

## Blocked by

None — can start immediately.

## Notes

Any additional context.
```

## When a skill says "publish to the issue tracker"

Create a markdown file under `.workspace/issues/<feature>/`.

## When a skill says "fetch the relevant ticket"

Read the file the user points to, or `find .workspace/issues -name "*.md"` and filter by feature name or slug.
