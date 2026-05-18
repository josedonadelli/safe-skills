---
name: setup-matt-pocock-skills-safe
description: Initialize .workspace/ in a company repo governed by NDAs. Creates .workspace/ (git-ignored), installs git guardrails, and writes agent config to .workspace/agents/. Run before first use of any engineering skill in repos where external access (GitLab, GitHub) is prohibited and AI-generated artifacts must not appear in git history.
disable-model-invocation: true
---

# Setup Matt Pocock's Skills (Safe / NDA variant)

Initialize the `.workspace/` structure and guardrails that all engineering skills assume in NDA-governed repos. Nothing is written to git-tracked paths except `.gitignore`.

Issue tracker is always **local markdown** in `.workspace/issues/` — no `gh`, `glab`, or external service calls. See ADR-0002.

## What this skill does

**Writes to `.gitignore`** (only git-tracked file this skill touches):

```
.workspace/
.claude/
.agents/
skills-lock.json
```

**Creates locally (all git-ignored):**

- `.workspace/agents/issue-tracker.md` — local workspace tracker convention
- `.workspace/agents/triage-labels.md` — triage role → label string mapping
- `.workspace/agents/domain.md` — where skills find the glossary and ADRs
- `.workspace/context/`, `.workspace/adr/`, `.workspace/issues/` — artifact folders
- `.claude/hooks/block-dangerous-git.sh` — guardrails hook script
- `.claude/settings.json` — PreToolUse hook registration

**Also updates** `CLAUDE.md` / `AGENTS.md` with an `## Agent skills` block.

## Process

### 1. Explore

Read the current repo state before asking anything:

- `CLAUDE.md` and `AGENTS.md` at the repo root — which exists? Is there already an `## Agent skills` section?
- `.workspace/` — does it already exist? Is `.workspace/agents/` already initialized?
- `.gitignore` — which of the four entries are already present?
- `.claude/settings.json` — are guardrails already installed?
- `CONTEXT-MAP.md` at the repo root — presence signals a multi-context layout

### 2. Ask — Triage label vocabulary

> When the `triage` skill moves an issue through its state machine it applies labels. The five canonical roles need to map to real strings. If your team hasn't defined label names yet, the defaults are fine.

| Role | Default string | Meaning |
|---|---|---|
| needs-triage | `needs-triage` | Maintainer needs to evaluate |
| needs-info | `needs-info` | Waiting on reporter |
| ready-for-agent | `ready-for-agent` | Fully specified, AFK-ready |
| ready-for-human | `ready-for-human` | Requires human implementation |
| wontfix | `wontfix` | Will not be actioned |

Ask whether to override any. Defaults are fine if no existing vocabulary.

### 3. Ask — Domain docs layout

> Skills read a domain glossary and past architectural decisions. They need to know whether this repo has one global context or one per sub-module.

- **Single-context** — one glossary for the whole repo (most repos)
- **Multi-context** — separate glossary per sub-module (signals `CONTEXT-MAP.md`)

If `CONTEXT-MAP.md` exists at the root, propose multi-context. Otherwise propose single-context.

### 4. Ask — Guardrails scope

> The guardrails hook blocks `git add`, `git commit`, `git push`, and other dangerous git operations from within Claude Code. You choose where to install it.

- **This repo only** (recommended) — installs to `.claude/settings.json` in this repo. Other repos on your machine are unaffected.
- **Global** — installs to `~/.claude/settings.json`. Applies to every repo on this machine.

### 5. Confirm and write

Show the user a draft of:
- The `## Agent skills` block for `CLAUDE.md` / `AGENTS.md`
- The contents of `.workspace/agents/issue-tracker.md`, `.workspace/agents/triage-labels.md`, `.workspace/agents/domain.md`

Let them edit before writing.

**Pick the file to edit for the `## Agent skills` block:**
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

Then execute in this order:

1. Add the four entries to `.gitignore` (append only those not already present; create the file if it doesn't exist):
   ```
   .workspace/
   .claude/
   .agents/
   skills-lock.json
   ```
2. Create `.workspace/agents/`, `.workspace/context/`, `.workspace/adr/`, `.workspace/issues/` with `mkdir -p`
3. Write the three config files to `.workspace/agents/` using the seed templates:
   - [issue-tracker-workspace.md](./issue-tracker-workspace.md)
   - [triage-labels.md](./triage-labels.md)
   - [domain-workspace.md](./domain-workspace.md)
4. Edit `CLAUDE.md` / `AGENTS.md` with the `## Agent skills` block
5. Install the guardrails hook:
   - Copy [block-dangerous-git.sh](./block-dangerous-git.sh) to the target hooks directory (`.claude/hooks/` for project scope, `~/.claude/hooks/` for global)
   - `chmod +x` the copied script
   - Add the PreToolUse hook to the target `settings.json` (merge into existing; don't overwrite other settings):
     ```json
     {
       "hooks": {
         "PreToolUse": [
           {
             "matcher": "Bash",
             "hooks": [
               {
                 "type": "command",
                 "command": "<path-to-script>"
               }
             ]
           }
         ]
       }
     }
     ```
   - For project scope: `"$CLAUDE_PROJECT_DIR"/.claude/hooks/block-dangerous-git.sh`
   - For global scope: `~/.claude/hooks/block-dangerous-git.sh`

### 6. Done

Tell the user setup is complete. Remind them:

- Every teammate must run `npx skills@latest add josedonadelli/safe-skills` then `/setup-matt-pocock-skills-safe` in their own clone — `.claude/` is git-ignored, nothing is shared via git
- To verify guardrails: `echo '{"tool_input":{"command":"git commit -m test"}}' | .claude/hooks/block-dangerous-git.sh` should exit 2 and print BLOCKED
- All artifacts (issues, PRDs, glossary, ADRs) live in `.workspace/` and are shared through approved channels outside git
