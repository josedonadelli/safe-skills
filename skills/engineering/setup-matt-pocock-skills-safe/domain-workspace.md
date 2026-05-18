# Domain Docs

How the engineering skills consume this repo's domain documentation.

## Before exploring, read these

- **`.workspace/context/CONTEXT.md`** — domain glossary for this repo (git-ignored). Initialize with `/setup-matt-pocock-skills-safe` if the file doesn't exist yet.
- **`.workspace/adr/`** — architectural decisions (git-ignored). Read any ADRs relevant to the area you're about to work in.

If either doesn't exist yet, **proceed silently**. The `/grill-with-docs` skill creates them lazily as terms and decisions are resolved during sessions.

## File structure

Single-context repo (most repos):

```
.workspace/
├── context/
│   └── CONTEXT.md
└── adr/
    ├── 0001-first-decision.md
    └── 0002-second-decision.md
```

Multi-context repo (presence of `CONTEXT-MAP.md` at the repo root):

```
.workspace/
├── context/
│   └── CONTEXT-MAP.md      ← points to per-module context files
└── adr/                    ← system-wide decisions
src/
├── ordering/
│   └── .workspace/
│       ├── context/
│       │   └── CONTEXT.md  ← context-specific glossary
│       └── adr/            ← context-specific decisions
└── billing/
    └── .workspace/
        ├── context/
        │   └── CONTEXT.md
        └── adr/
```

## Use the glossary's vocabulary

When your output names a domain concept (in an issue title, a refactor proposal, a hypothesis, a test name), use the term as defined in `.workspace/context/CONTEXT.md`. Don't drift to synonyms the glossary explicitly avoids.

If the concept you need isn't in the glossary yet, that's a signal — either you're inventing language the project doesn't use (reconsider) or there's a real gap (note it for `/grill-with-docs`).

## Flag ADR conflicts

If your output contradicts an existing ADR in `.workspace/adr/`, surface it explicitly rather than silently overriding:

> _Contradicts ADR-0003 (workspace-first policy) — but worth reopening because…_
