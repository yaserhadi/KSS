# KSS User Handbook

## Executive Summary

**Knowledge Stewardship System (KSS)** is a lightweight framework for managing knowledge, decisions and governance in AI-assisted development. It gives AI agents persistent project memory across sessions and gives humans governance without heavy process. For the problem it solves and a first walkthrough, start with the [README](../README.md).

### KSS_Doctrine_001 — Extraction Direction

```yaml
KSS_Doctrine_001:
  name: Extraction Direction
  rule: Jabal → Review → Generalize → KSS → Optional Runtime Install
  forbids: KSS → ~/.cursor without owner-approved runtime_install_gate
```

When adopting or updating KSS from a reference project (e.g. Jabal): review and classify first, generalize patterns into KSS, then install to `~/.cursor` only with explicit owner approval. See [GOVERNANCE_LAYERS.md](GOVERNANCE_LAYERS.md) and [KSS_EVOLUTION.md](KSS_EVOLUTION.md) (EVOL-005).

### What KSS Provides

- **Session continuity** — HANDOFF captures what happened and what to do next
- **Clear authority** — DOC_POLICY, MANIFEST, DEC digests, INTEGRITY_RULES, STATE and HANDOFF in a defined reading order
- **Execution gate** — work runs only when the `status` in `STATE.yaml` is `active`
- **Governance without ceremony** — commands and rules guide the agent through planning, review, merge and closeout

### Who This Handbook Is For

Colleagues adopting KSS: developers, architects, and anyone using Cursor with AI agents on KSS-enabled projects.

---

## Quick Start

### 1. Install

Follow the [Installation Guide](../INSTALL.md): commands, agents, skills and portable rules into `~/.cursor/`, and the Tier 1 startup files into your project. Optional organizational policies can be added as User Rules in Cursor (see [Rules](#rules)).

### 2. Start Every Session

```
/boot
```

This loads project context, execution state, and last session handoff.

### 3. End Every Session

```
/session-end
```

This writes `.cursor/memory/HANDOFF.md` before you close the chat. **Run this in Agent mode** (Plan and Ask cannot write HANDOFF).

---

## Benefits

| Benefit | Description |
|---------|-------------|
| **Session continuity** | HANDOFF tells the next agent (or you) what to do next and what to avoid |
| **Governance without ceremony** | Commands and rules guide branching, review and closeout; tests, branch protection and code review remain your technical controls |
| **Clear authority** | MANIFEST, DEC, INTEGRITY and STATE have distinct roles and a defined reading order; conflicts are reported, not silently resolved |
| **Safe execution** | Execution gate blocks work when STATE `status` is not `active` |
| **Auditability** | Reports in one place, DEC digests as decision records, evidence-based conclusions |
| **Team consistency** | Same commands and rules for all team members |

---

## File Inventory

### Project .cursor Structure

| File | Purpose | Path | Tier |
|------|---------|------|------|
| DOC_POLICY.yaml | Canonical paths only | `.cursor/DOC_POLICY.yaml` | 1 — startup (`/boot` falls back to defaults if missing) |
| AI_ENTRY.md | Entry gate, reading order, execution rules | `.cursor/memory/AI_ENTRY.md` | 1 — startup |
| PROJECT_MANIFEST.md | Vision, constraints, architectural locks | `.cursor/memory/PROJECT_MANIFEST.md` | 1 — startup |
| STATE.yaml | Phase, stage, execution status | `.cursor/memory/STATE.yaml` | 1 — startup |
| HANDOFF.md | Session continuity, next actions | `.cursor/memory/HANDOFF.md` | 1 — startup |
| GOALS.md | Strategic goals only | `.cursor/memory/GOALS.md` | 1 — startup (`/boot` stops if unreadable) |
| INTEGRITY_RULES.md | Non-negotiable guardrails, verification commands | `.cursor/memory/INTEGRITY_RULES.md` | 2 — scaffold |
| VERSIONS.md | Version facts | `.cursor/memory/VERSIONS.md` | 2 — scaffold |
| decisions/ | DEC digests | `.cursor/memory/decisions/` | 2 — scaffold |
| ACTIVE.plan.md | Current / last completed plan pointer | `.cursor/plans/ACTIVE.plan.md` | 3 — plan workflows (read only if present at startup) |
| LESSONS.md | Mistakes to avoid | `.cursor/memory/LESSONS.md` | 3 — optional |
| conventions/ | Engineering standards (advisory) | `.cursor/memory/conventions/` | 3 — optional |

Tier 1 = minimum startup; Tier 2 = standard installation scaffolds; Tier 3 = workflow-specific. See [What to install](../README.md#what-to-install).

### Global Commands (User-Level)

Copy to `~/.cursor/commands/`.

| Command | Purpose |
|---------|---------|
| boot | Load project context at session start |
| preplan | Pre-Plan Charter for Plan Mode: scope, destination, risk, decisions, backlog binding, verification expectations |
| session-end | Closeout: verify finalize/packaging, persist HANDOFF, push |
| docpack | Package `.cursor/` governance (Detect → Propose → Apply) |
| gw-review | Review the actual change set before merge |
| git-prepare | Create branch from main |
| git-save | Commit progress (no push) |
| git-finalize | Pre-merge review, local merge into main, delete local branch, lifecycle packaging |
| dec | Create DEC digest (`adr` = deprecated alias) |
| en | Respond in English only |
| ar | Respond in Arabic only |
| aren | Respond in both Arabic and English |

When to use each command and in which order: [Command workflow guide](reference/command-workflow.md).

### Subagents

Copy to `~/.cursor/agents/`.

| Agent | Purpose |
|-------|---------|
| dec-steward | Extract DEC digests from pasted text (via `/dec`) |
| adr-steward | Deprecated alias → dec-steward (via `/adr`) |
| ai-knowledge-steward | Maintain .cursor/memory/ (via /docpack) |
| user-doc-steward | Human docs at DOC_POLICY `human_docs` only (via /docpack; skip if undefined) |

> Subagents are execution tools. They never own authority. All decisions remain with human governance.

### Skills (Optional)

| Skill | Path | Purpose |
|-------|------|---------|
| safe-coding-practices | `~/.cursor/skills/safe-coding-practices/SKILL.md` | Evidence-based dev, rollback |
| create-rule | `~/.cursor/skills/create-rule/SKILL.md` | Create Cursor rules |
| create-skill | `~/.cursor/skills/create-skill/SKILL.md` | Create Agent Skills |
| update-cursor-settings | `~/.cursor/skills/update-cursor-settings/SKILL.md` | Modify settings.json |

---

## Rules

Cursor has several rule sources. KSS uses two of them and leaves a third to the adopter:

| Source | Where | KSS role |
|--------|-------|----------|
| Portable rule files | `~/.cursor/rules/` (machine-local) | KSS distributes four files |
| Project rules | `<project>/.cursor/rules/*.mdc` | KSS offers optional templates in `templates/project-rules/` |
| User Rules | Customize → Rules (stored on your Cursor account) | Adopter-owned organizational policies; KSS ships no text |

### Portable rule files (distributed by KSS)

Copy to `~/.cursor/rules/`:

| Rule | Purpose | Activation |
|------|---------|-----------|
| plans-location.mdc | Plans stored in project `.cursor/plans/` only | `.mdc`, `alwaysApply: true` |
| git-main-merge-only.mdc | No edits on `main`/`master`; branch first | `.mdc`, `alwaysApply: true` |
| conflict-governance.mdc | Stop and report when governance sources disagree | `.mdc`, `alwaysApply: true` |
| reports-location-global.md | Reports stored in `.cursor/reports/` | Plain Markdown — **not verified** |

Cursor documents `alwaysApply: true` as "always included" for `.mdc` rules, and ignores plain `.md` files in project rule folders. Whether machine-local files load for every workspace (for example, a workspace outside your home directory) is not documented. Confirm in **Customize → Rules**. See [Global and user rules](reference/how-to-make-global-rules.md).

### Optional organizational User Rules

Teams often add organization-wide policies as User Rules, for example:

1. Change advisory review for production, security and data changes
2. Git investigation and safety before claiming files are missing
3. Agent safety and integrity (no bypassing auth, no secrets)
4. Audit, risk and compliance intent
5. Data protection and PII
6. Production environment safety
7. Git change management and branching

These are **adopter-owned** policies. KSS does not ship their text and does not require them; write them to match your organization.

**Response language:** `/en`, `/ar`, `/aren` commands — not a rule file.

---

## Commands

The **[Command workflow guide](reference/command-workflow.md)** is the main reference: what each command does, when to use it, side effects, common workflows, and Findings to Backlog.

Reference lifecycle (not every task needs every step; Owner Build is Cursor's native Build action, not an approval layer):

```text
/boot → /preplan → Plan Mode (paste Charter) → iterative review → Owner Build → /git-finalize (includes /gw-review) → /session-end
```

### Session and Workflow Commands

| Command | When to Use |
|---------|-------------|
| `/boot` | Start of every session; loads project context |
| `/preplan` | Before Plan Mode; produces a copy-ready Pre-Plan Charter (scope, destination, risk, decisions, backlog binding). Paste it into Plan Mode; refine the plan in the same Plan conversation; only the Owner presses Build |
| `/gw-review` | Optional standalone; runs automatically inside `/git-finalize` |
| `/session-end` | Before closing chat; verifies finalize/packaging, writes HANDOFF, pushes (use Agent mode) |
| `/docpack` | Mid-session packaging of `.cursor/` governance; embedded by finalize / session-end |

### Git Commands

| Command | When to Use |
|---------|-------------|
| `/git-prepare` | Start new work; create branch from main |
| `/git-save` | Checkpoint progress; commit without push |
| `/git-finalize` | Pre-merge review, then local merge into main after tests; then `/session-end` verifies and pushes |

### Language Commands

| Command | When to Use |
|---------|-------------|
| `/en` | Reply in English only |
| `/ar` | Reply in Arabic only |
| `/aren` | Reply in both Arabic and English |

### Decision Command

| Command | When to Use |
|---------|-------------|
| `/dec` | Record a decision digest (DEC) from pasted text (discussion, meeting notes). `/adr` is a deprecated alias that forwards to `/dec` |

---

## Subagents

| Agent | Invoked By | Purpose |
|-------|------------|---------|
| **dec-steward** | `/dec` (and the deprecated `/adr` alias) | Extract context, options, decision; write DEC digest to `.cursor/memory/decisions/` |
| **ai-knowledge-steward** | `/docpack` | Update `.cursor/memory/` and `.cursor/reports/` (HANDOFF, STATE, LESSONS, closure) |
| **user-doc-steward** | `/docpack` | Update human docs at DOC_POLICY `human_docs` only — skip if path undefined |

---

## KSS Flow

### Execution Flow

```
Session Start → /boot → Read DOC_POLICY, AI_ENTRY, MANIFEST, STATE, HANDOFF, GOALS
                              ↓
                    STATE status == active?
                     /              \
                   No                Yes
                    |                 |
            Do NOT execute    Read HANDOFF next actions
            Clarify only      Work within MANIFEST
                    |                 |
                    └────────┬────────┘
                             ↓
                    /session-end before close
```

### Authority Order

Read sources in this order when they align:

1. DOC_POLICY (paths only)
2. PROJECT_MANIFEST (architectural locks and constraints)
3. DEC digests in `.cursor/memory/decisions/` (canonical decisions)
4. INTEGRITY_RULES (verifiable guardrails and verification commands)
5. STATE (execution position)
6. HANDOFF (continuity)
7. GOALS (strategic intent)

This is a reading order only; it does not establish precedence when sources conflict. When MANIFEST, DEC and INTEGRITY disagree, the agent stops and reports the conflict for an Owner decision. DEC changes that affect locks are synced into MANIFEST or INTEGRITY only with Owner approval.

Legacy project `docs/` and historical ADRs are not execution authority. KSS `docs/` is framework reference only.

---

## Ready-to-Use Prompts

| Scenario | Prompt |
|----------|--------|
| Start session | `/boot` then "Continue from handoff" |
| End session and publish | `/session-end` (after `/git-finalize` when work was merged) |
| Record a decision from discussion | `/dec` then paste the discussion or meeting text |
| Prepare a new plan (scope, risk, backlog) | `/preplan` then describe the intent; paste the Charter into Plan Mode |
| Independent review (optional) | `/gw-review` then name the branch/base or paste the diff |
| Package `.cursor/` governance after work | `/docpack` |
| Start work on branch | `/git-prepare` |
| Save progress | `/git-save` |
| Close branch and merge locally | `/git-finalize` |
| English reply | `/en` then your question |
| Arabic reply | `/ar` then your question |
| Dual language | `/aren` then your question |

---

## Troubleshooting

| Issue | Fix |
|-------|-----|
| HANDOFF missing or empty | Run `/session-end` in Agent mode before closing; or provide next actions manually |
| `/boot` stops on GOALS | Create `.cursor/memory/GOALS.md` (older installs used `.cursor/goals/`; see INSTALL upgrade note) |
| Agent won't execute | Check `STATE.yaml` — execution allowed only when `status` is `active` |
| Plans saved to wrong folder | Check Customize → Rules for `plans-location`; plans go to project `.cursor/plans/` |
| Reports in wrong place | `reports-location-global.md` activation is not verified; add the rule as a User Rule or project `.mdc` rule if needed |
| /session-end doesn't write | Use **Agent mode**; Plan and Ask modes cannot write HANDOFF |
| Language override ignored | Use `/en`, `/ar`, or `/aren` at start of message |
| DEC conflicts with MANIFEST or INTEGRITY | Agent stops and reports; the Owner decides; sync enforcement layers explicitly with approval |

---

## Where to Get Files

See the framework `commands/`, `agents/`, `rules/`, `skills/` and `templates/` folders for copyable files.
