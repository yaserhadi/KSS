# KSS Installation Guide

This guide sets up KSS in two places: your **user runtime** (`~/.cursor/`, once per machine) and each **project** (`<project>/.cursor/`). For a quick trial, see [First 10 minutes](README.md#first-10-minutes).

Back up any existing `~/.cursor/commands/`, `agents/`, `skills/` and `rules/` folders before copying.

---

## Installation Order

### 1. Commands

Copy command files to your user Cursor commands folder:

```
~/.cursor/commands/
├── boot.md
├── preplan.md
├── session-end.md
├── docpack.md
├── dec.md
├── adr.md
├── en.md
├── ar.md
├── aren.md
├── gw-review.md
├── git-prepare.md
├── git-save.md
└── git-finalize.md
```

See the [Command workflow guide](docs/reference/command-workflow.md) for what each command does and when to use it. Use `/preplan` before Plan Mode when creating a new execution plan: it produces a Pre-Plan Charter that you paste into Plan Mode. `adr.md` is a deprecated alias of `dec.md`.

**Upgrading an existing install:** `doplan.md` was renamed to `preplan.md`. Back up `~/.cursor/commands/` first, copy the new `preplan.md`, then remove the retired `~/.cursor/commands/doplan.md` so `/doplan` no longer appears in Cursor.

**Language overrides:** `/en` (English only), `/ar` (Arabic only), `/aren` (dual language). See [COMMANDS_EXPLANATION.md](commands/COMMANDS_EXPLANATION.md).

### 2. Agents

Copy agent files to your user Cursor agents folder:

```
~/.cursor/agents/
├── dec-steward.md
├── adr-steward.md          ← deprecated alias of dec-steward
├── ai-knowledge-steward.md
└── user-doc-steward.md
```

### 3. Skills

Copy skill folders to your user Cursor skills folder:

```
~/.cursor/skills/
├── safe-coding-practices/SKILL.md
├── create-rule/SKILL.md
├── create-skill/SKILL.md
└── update-cursor-settings/SKILL.md
```

### 4. Portable rules

KSS distributes four portable rule files in [`rules/`](rules/). Copy them to your machine-local user rules folder (`~/.cursor/rules/`; on Windows `%USERPROFILE%\.cursor\rules`):

```
~/.cursor/rules/
├── plans-location.mdc
├── git-main-merge-only.mdc
├── conflict-governance.mdc
└── reports-location-global.md
```

**Copying a file is not the same as Cursor loading it.** Cursor documents activation for `.mdc` rule files; `reports-location-global.md` is plain Markdown and its activation is not verified. After copying, open **Customize → Rules** in Cursor and confirm which rules are listed. If a rule must apply and you cannot confirm it there, add its text as a User Rule in Customize → Rules, or add an equivalent `.mdc` rule to the project's `.cursor/rules/`. Details and open questions: [Global and user rules](docs/reference/how-to-make-global-rules.md).

**Project-specific rules** (module boundaries, plan gates) belong in the **adopting project's** `.cursor/rules/`, not in the user folder. Copy optional templates from `templates/project-rules/` and rename `*.template.mdc` to `*.mdc` — Cursor only recognizes the `.mdc` extension. See [docs/GOVERNANCE_LAYERS.md](docs/GOVERNANCE_LAYERS.md).

### 5. Optional organizational User Rules

Some adopters also add organization-wide policies (for example change advisory review, production safety, data protection, Git branching) as User Rules in **Customize → Rules**. These are adopter-owned policies: KSS does not ship their text and does not require them. User Rules are stored on your Cursor account and apply across your projects.

**Response language:** use `/en`, `/ar`, or `/aren` commands — not a rule file.

### 6. Project Setup

For each project adopting KSS, create the `.cursor/` structure from [`templates/`](templates/). Artifacts fall into three tiers; only Tier 1 is needed to start a session.

**Tier 1 — Minimum startup** (`/boot` loads these; it stops if `GOALS.md` cannot be read):

| Template | Copy to |
|----------|---------|
| `DOC_POLICY.template.yaml` | `.cursor/DOC_POLICY.yaml` |
| `AI_ENTRY.template.md` | `.cursor/memory/AI_ENTRY.md` |
| `PROJECT_MANIFEST.template.md` | `.cursor/memory/PROJECT_MANIFEST.md` |
| `STATE.template.yaml` | `.cursor/memory/STATE.yaml` |
| `HANDOFF.template.md` | `.cursor/memory/HANDOFF.md` |
| `GOALS.template.md` | `.cursor/memory/GOALS.md` |

`/boot` falls back to default paths when `DOC_POLICY.yaml` is missing, but the [compatibility contract](README.md#kss-compatibility-contract) requires it.

**Tier 2 — Standard installation scaffolds** (read on demand or before execution; not `/boot` blockers):

| Template | Copy to |
|----------|---------|
| `INTEGRITY_RULES.template.md` | `.cursor/memory/INTEGRITY_RULES.md` (required by the compatibility contract) |
| `VERSIONS.template.md` | `.cursor/memory/VERSIONS.md` |
| — | `.cursor/memory/decisions/` (empty folder for DEC digests) |
| `plans/README.template.md` | `.cursor/plans/README.md` |
| `reports/README.template.md` | `.cursor/reports/README.md` |

**Tier 3 — Workflow-specific** (add when you use the workflow):

| Artifact | Needed when |
|----------|-------------|
| `.cursor/plans/ACTIVE.plan.md` (from `ACTIVE.plan.template.md`) | You use plan workflows. `/boot` reads it only if present; `/git-finalize`, `/docpack` and `/session-end` keep it current. |
| `.cursor/memory/decisions/README.md` | Before the first `/dec`: the dec-steward agent writes DEC digests using this project-owned template. KSS does not ship one. |
| `.cursor/memory/decisions/DEC-NNNN-*.md` | Created by `/dec` when a decision is recorded |
| Reports in `.cursor/reports/` | Evidence or closure is recorded |
| `.cursor/memory/LESSONS.md` (from `LESSONS.template.md`) | Optional |
| `.cursor/memory/conventions/` | Optional, advisory |
| Project rules from `templates/project-rules/` | Optional gates |
| `.cursor/memory/PRE_PRODUCTION_DEVELOPMENT_POLICY.md` | Only if `STATE.yaml` marks a binding pre-production policy; `/boot` then requires it |

Resulting minimum + standard layout:

```
.cursor/
├── DOC_POLICY.yaml
├── memory/
│   ├── AI_ENTRY.md
│   ├── PROJECT_MANIFEST.md
│   ├── STATE.yaml
│   ├── HANDOFF.md
│   ├── GOALS.md
│   ├── INTEGRITY_RULES.md
│   ├── VERSIONS.md
│   └── decisions/
├── plans/
│   └── README.md
└── reports/
    └── README.md
```

Then fill the templates with your project's content.

---

## What Gets Installed Where

| Source | Destination | Filled by |
|--------|-------------|-----------|
| commands/, agents/, skills/, rules/ | `~/.cursor/...` (user, machine-local) | Framework (copy as-is) |
| templates/ | `<project>/.cursor/...` | Adopter (fill with project content) |
| templates/project-rules/ | `<project>/.cursor/rules/` (renamed to `.mdc`) | Adopter (customize) |

Framework provides structure. Project provides content.
Templates are scaffolds, NOT policies.

---

## Upgrading from `.cursor/goals/GOALS.md`

Earlier KSS scaffolds placed goals in `.cursor/goals/`. `/boot` reads `.cursor/memory/GOALS.md` and stops if it cannot be read. If your project still uses the old path:

1. Move `GOALS.md` to `.cursor/memory/GOALS.md`.
2. Update the `goals` entry in `.cursor/DOC_POLICY.yaml` to `.cursor/memory/GOALS.md`.
3. Remove the empty `.cursor/goals/` folder and run `/boot` to confirm.

---

## Verification

After installation, run `/boot` in an Agent chat. The agent should:
1. Read `.cursor/DOC_POLICY.yaml` (or use default paths)
2. Load the Tier 1 memory files
3. Report the current STATE and the next action from HANDOFF

If `/boot` stops, check that every Tier 1 file exists at the path above and that paths in `DOC_POLICY.yaml` point to existing files.

Then check **Customize → Rules** to see which rules Cursor has loaded.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `/boot` command not found | Copy commands to `~/.cursor/commands/`; reload the Cursor window |
| `/boot` stops on GOALS | Create `.cursor/memory/GOALS.md` (see Upgrading above) |
| Memory files not loading | Check DOC_POLICY.yaml paths |
| Agent not following a rule | Check Customize → Rules: is the rule listed? Plain `.md` rule files may be ignored; use `.mdc` or a User Rule |
| Project rule template has no effect | Rename `*.template.mdc` to `*.mdc` |
| `/dec` cannot find a template | Create `.cursor/memory/decisions/README.md` |
| Skills not available | Copy to `~/.cursor/skills/` |
