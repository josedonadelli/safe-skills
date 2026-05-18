---
name: setup-matt-pocock-skills-safe
description: Initialize .workspace/ in a company repo governed by NDAs. Writes agent config to .workspace/agents/ (git-ignored), adds .workspace/ to .gitignore, and updates CLAUDE.md/AGENTS.md. Run before first use of any engineering skill in a repo where external access (GitLab, GitHub) is prohibited and AI-generated artifacts must not appear in git history.
disable-model-invocation: true
---

# Setup Matt Pocock's Skills (Safe / NDA variant)

Initialize the `.workspace/` structure that all engineering skills use in NDA-governed repos. Nothing is written to git-tracked paths — all config lives in `.workspace/` (git-ignored).

Issue tracker is always **local markdown** in `.workspace/issues/` — no `gh`, `glab`, or external service calls. This is non-negotiable in this fork. See ADR-0002.

## What this skill writes

- `.workspace/agents/issue-tracker.md` — local workspace convention
- `.workspace/agents/triage-labels.md` — triage role → label string mapping
- `.workspace/agents/domain.md` — where skills find CONTEXT.md and ADRs
- `.gitignore` entry for `.workspace/`
- `## Agent skills` block in `CLAUDE.md` / `AGENTS.md`

## Process

### 1. Explore

Read the current repo state before asking anything:

- `CLAUDE.md` and `AGENTS.md` at the repo root — which exists? Is there already an `## Agent skills` section?
- `.workspace/` — does it already exist? Is `.workspace/agents/` already initialized?
- `.gitignore` — does it already ignore `.workspace/`?
- `CONTEXT-MAP.md` at the repo root — presence signals a multi-context (monorepo) layout

### 2. Ask — Triage label vocabulary

> When the `triage` skill moves an issue through its state machine it applies labels. The five canonical roles need to map to real strings. If your team hasn't defined label names yet, the defaults are fine.

| Role | Default string | Meaning |
|---|---|---|
| needs-triage | `needs-triage` | Maintainer needs to evaluate |
| needs-info | `needs-info` | Waiting on reporter |
| ready-for-agent | `ready-for-agent` | Fully specified, AFK-ready |
| ready-for-human | `ready-for-human` | Requires human implementation |
| wontfix | `wontfix` | Will not be actioned |

Ask whether to override any string. If no existing vocabulary, defaults are fine.

### 3. Ask — Domain docs layout

> Some skills read a domain glossary and past architectural decisions. They need to know whether this repo has one global context or one per sub-module.

- **Single-context** — one `CONTEXT.md` equivalent for the whole repo (most repos)
- **Multi-context** — separate `CONTEXT.md` per sub-module; signals presence of `CONTEXT-MAP.md`

If `CONTEXT-MAP.md` exists at the root, propose multi-context. Otherwise propose single-context.

### 4. Confirm and write

Show the user a draft of:
- The `## Agent skills` block for `CLAUDE.md` / `AGENTS.md`
- The contents of `.workspace/agents/issue-tracker.md`, `.workspace/agents/triage-labels.md`, `.workspace/agents/domain.md`

Let them edit before writing.

**Pick the file to edit:**
- If `CLAUDE.md` exists, edit it.
- Else if `AGENTS.md` exists, edit it.
- If neither exists, ask which to create — don't pick for them.

If an `## Agent skills` block already exists, update it in-place.

The block:

```markdown
## Agent skills

### Issue tracker

Issues live as local markdown files in `.workspace/issues/` (git-ignored). See `.workspace/agents/issue-tracker.md` — run `/setup-matt-pocock-skills-safe` to initialize.

### Triage labels

[one-line summary of label vocabulary]. See `.workspace/agents/triage-labels.md`.

### Domain docs

[single-context or multi-context]. See `.workspace/agents/domain.md`.
```

Then write in this order:

1. Create `.workspace/agents/`, `.workspace/context/`, `.workspace/adr/`, `.workspace/issues/` with `mkdir -p`
2. Write the three config files to `.workspace/agents/` using the seed templates in this skill folder:
   - [issue-tracker-workspace.md](./issue-tracker-workspace.md)
   - [triage-labels.md](./triage-labels.md)
   - [domain-workspace.md](./domain-workspace.md)
3. Add `.workspace/` to `.gitignore`: append if the file exists but doesn't already contain it; create if it doesn't exist
4. Edit `CLAUDE.md` / `AGENTS.md` with the `## Agent skills` block

### 5. Done

Tell the user setup is complete. Remind them:

- `.workspace/` is git-ignored — each teammate must run `/setup-matt-pocock-skills-safe` in their own clone before using the skills
- They can edit `.workspace/agents/*.md` directly to adjust config; re-running this skill is only needed to reinitialize from scratch
- All skill artifacts (issues, PRDs, glossary, ADRs) live under `.workspace/` and are shared through approved channels outside git
