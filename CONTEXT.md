# Matt Pocock Skills

A collection of agent skills (slash commands and behaviors) loaded by Claude Code. Skills are organized into buckets and consumed by per-repo configuration emitted by `/setup-matt-pocock-skills-safe`.

## Language

**Issue tracker**:
In this fork, always `.workspace/issues/` — a local markdown convention inside the git-ignored **Workspace**. No external services (GitHub Issues, GitLab Issues, Linear, or similar) are used. Skills like `to-issues`, `to-prd`, `triage`, and `review` read from and write to it.
_Avoid_: GitHub Issues, GitLab Issues, `.scratch/`, backlog manager, backlog backend, issue host

**Issue**:
A single tracked unit of work inside the **Issue tracker** — a bug, task, PRD, or slice produced by `to-issues`. Stored as a markdown file under `.workspace/issues/<feature>/`.
_Avoid_: ticket (use only when quoting external systems that call them tickets)

**Triage role**:
A canonical state-machine label applied to an **Issue** during triage (e.g. `needs-triage`, `ready-for-agent`). Each role maps to a real label string in the **Issue tracker** via `.workspace/agents/triage-labels.md`.

**Workspace**:
The `.workspace/` folder at the root of each company repo where this fork is installed. Git-ignored by default. Centralizes all AI-assisted artifacts — issues, PRDs, glossaries, ADRs, triage outputs, and agent config — that must not appear in the official git history.
_Avoid_: `.scratch/`, scratch folder, Claude workspace

**Workspace artifact**:
Any file generated or edited by a skill inside the **Workspace**. The human decides what to promote manually to the official repository.
_Avoid_: Claude output, draft

**Workspace layout**:
Standard internal structure of the **Workspace**:
- `.workspace/agents/` — skill config (issue tracker, triage labels, domain docs)
- `.workspace/context/CONTEXT.md` — project domain glossary (singleton per repo)
- `.workspace/adr/` — architectural decisions (singleton per repo)
- `.workspace/issues/<feature>/` — issues and PRDs grouped by feature
_Avoid_: flat structure, layout by artifact type

## Relationships

- An **Issue tracker** holds many **Issues**
- An **Issue** carries one **Triage role** at a time
- A **Workspace** contains the **Issue tracker**, the domain glossary, and all **Workspace artifacts**

## Flagged ambiguities

- "backlog" was previously used to mean both the *tool* hosting issues and the *body of work* inside it — resolved: the tool is the **Issue tracker**; "backlog" is no longer used as a domain term.
- "backlog backend" / "backlog manager" — resolved: collapsed into **Issue tracker**.
