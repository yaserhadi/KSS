# KSS command workflow

**Audience:** Developers and operators using Cursor with KSS on a project.

**Purpose:** Explain **what** each KSS command does, **why** it helps, **when** to use it (and when not), and the **typical order**. This is the main human-facing command reference; other documents link here.

**This page is a human guide.** It does not replace the command files in [`commands/`](../../commands/) — those define agent behavior, output formats, and checks.

---

## Lifecycle

```text
/boot  →  /doplan  →  Plan  →  Owner Build  →  /gw-review  →  /git-finalize  →  /session-end
```

- This is a **reference workflow**, not a requirement to run every command for every task. Small or docs-only work skips steps (see [Common workflows](#common-workflows)).
- **Owner Build** is Cursor's native Build action on a plan. It is not an extra approval layer.

## Quick reference (cheat sheet)

| Phase | Command | When to use | Benefit | Side effects |
|-------|---------|-------------|---------|--------------|
| Session start | `/boot` | Start of a work session | Restores project context, state, last handoff | Analyzes only |
| Planning | `/doplan` | A new plan is needed | Scope, destination, decisions, risk and backlog binding before Plan mode | Analyzes only (English brief) |
| Plan | Cursor Plan mode | After `/doplan` | Writes the plan file for Owner review | Writes the plan file |
| Execution | Owner Build | Plan accepted by the Owner | Executes the plan todos | Modifies files; may commit on a branch per plan |
| Review | `/gw-review` | After implementation, before merge | Finds defects, scope drift, security and rule violations | Analyzes only |
| Local merge | `/git-finalize` | Branch ready (tests green, review done when applicable) | Merges into local `main`, deletes local branch, packages lifecycle artefacts | Commits, merges, modifies `.cursor/` artefacts — **no push** |
| Closeout | `/session-end` | End of session | Verifies finalize and packaging, persists HANDOFF, publishes | Modifies HANDOFF/STATE, **pushes** (or records waiver) |

**Order rule:** `/git-finalize` (local merge) comes **before** `/session-end` (verify + push). Finalize never pushes; session-end owns publication.

---

## Retained commands in detail

### `/boot`

**In plain terms:** "Where are we?" — loads DOC_POLICY, AI_ENTRY, MANIFEST, STATE, HANDOFF.

- **Use when:** starting a session, or after a long break.
- **Unnecessary when:** the session already has fresh context.
- **Side effects:** none — read-only. It does not start execution from a plan.
- **Full spec:** [`commands/boot.md`](../../commands/boot.md)

### `/doplan`

**In plain terms:** "What exactly are we doing, where does it belong, how risky is it, and which backlog item does it serve?" — **before** Plan mode writes the plan.

- **Use when:** creating a new `.cursor/plans/*.plan.md` plan; the request is unclear; work may touch production, security, data, schema, access control, or several modules.
- **Covers:** clarity questions (1–2 only), safest default approach, module/kernel destination per project rules, architectural decision check (`/dec` recommendation), conditional risk assessment (safeguard-based level, CAB need, rollback, mitigations), Doctrine_002 check when governance layers are touched, CAP/backlog binding, Active decision candidates, scaffold hints.
- **Proportional:** a docs-only change gets one line per item (`Risk: Low — <reason>`); sensitive work gets the full risk block.
- **Unnecessary when:** trivial edit with no plan file.
- **Side effects:** none — produces an English brief only. It does not write the plan, edit BACKLOG (unless the Owner explicitly asks), or approve anything. CAB approval, where genuinely required, follows the CAB rule.
- **Full spec:** [`commands/doplan.md`](../../commands/doplan.md)

### Plan mode and Owner Build

- **Plan mode** writes the plan from the `/doplan` brief and stops for Owner review.
- **Owner Build** executes the plan's todos within all project governance (branch rules, locks, decisions, tests).

### `/gw-review`

**In plain terms:** "Is this actual change set good to merge?"

- **Use when:** before merge of code, security, data, schema, or governance-affecting changes.
- **Optional:** trivial, reversible edits.
- **Unnecessary when:** an equivalent review is already documented for the same diff (recorded Bugbot, security review, human review) — repeat only for a material reason.
- **Checks:** correctness, scope vs the approved plan, safety, security, boundaries, project conventions, test evidence, missing decisions, Doctrine_002 (N/A when irrelevant).
- **Verdicts:** **Approve** (no Critical findings; warnings recorded) · **Request Changes** (fixable issues must be resolved before merge) · **Block** (Critical: security/data-loss risk, Doctrine Fail, unapproved scope, missing required decision; re-review after fix).
- **Side effects:** none — analyzes only. A verdict is **not** an instruction to merge or push.
- **Full spec:** [`commands/gw-review.md`](../../commands/gw-review.md)

### `/git-finalize`

**In plain terms:** "Close this branch onto local `main`."

- **Use when:** the feature branch is ready (tests and lint pass; `/gw-review` done when applicable).
- **Does:** final commit on the branch, `git merge --no-ff` into local `main`, deletes the local branch, runs merge-moment lifecycle packaging (HANDOFF, STATE, ACTIVE plan, BACKLOG row status, reports).
- **Does not:** push. Next required step is `/session-end`.
- **Full spec:** [`commands/git-finalize.md`](../../commands/git-finalize.md)

### `/session-end`

**In plain terms:** "Close the session properly."

- **Use when:** ending a session — always after `/git-finalize`, or alone when nothing was merged.
- **Does:** closeout audit (finalize integrity, packaging, plan location), repairs gaps via `/docpack`, writes `.cursor/memory/HANDOFF.md`, pushes `main` when product advanced (or records an Owner waiver).
- **Run in Agent mode** — Plan and Ask modes cannot write HANDOFF.
- **Full spec:** [`commands/session-end.md`](../../commands/session-end.md)

---

## Supporting commands (use as needed — not lifecycle steps)

| Command | Role | Typical use | Side effects |
|---------|------|-------------|--------------|
| `/git-prepare` | Create a branch from updated `main` | Starting work outside a plan's own branch step | Stashes if needed, pulls `main`, creates branch |
| `/git-save` | Checkpoint commit on the branch | Long work, safe save point | Commits — no merge, no push |
| `/docpack` | Package `.cursor/` governance artefacts | Mid-session packaging; embedded by finalize / session-end | Modifies `.cursor/` memory/reports |
| `/dec` (`/adr` alias) | Write a DEC digest from discussion text | An architectural decision must be recorded | Writes `.cursor/memory/decisions/DEC-NNNN-*.md` |
| `/en`, `/ar`, `/aren` | Set response language | Any time | None |

## Review vs merge vs closeout vs publication

| Step | Command | What it decides or does |
|------|---------|-------------------------|
| Review | `/gw-review` | Verdict on the change set — analysis only |
| Merge | `/git-finalize` | Local merge into `main` + lifecycle packaging |
| Closeout | `/session-end` | Verifies finalize and packaging; persists HANDOFF |
| Publication | `/session-end` | Pushes `main` (or records an Owner waiver) |

In-session summaries (files changed, tests, next steps, open questions) are normal agent output — no separate command is needed.

---

## Common workflows

| Workflow | Sequence |
|----------|----------|
| Feature work | `/boot` → `/doplan` → Plan → Owner Build → `/gw-review` → `/git-finalize` → `/session-end` |
| Small fix | Branch → fix → tests → `/gw-review` if auth/tenancy/DB touched → `/git-finalize` → `/session-end` |
| Docs-only change | Branch → edit → `/git-finalize` → `/session-end` (review optional) |
| Governance change | `/doplan` (Doctrine_002 check) → Plan → Owner Build → `/gw-review` → `/git-finalize` → `/session-end` |
| Mid-session checkpoint | `/git-save` (and `/docpack` if governance artefacts need updating) |

---

## Findings to Backlog

**Principle:** Discussion is not authorization. A finding is not an approved backlog item. Backlog admission is not execution authorization.

1. **Why separate them** — keeping findings, product decisions, and implementation apart prevents silent scope growth and keeps every decision auditable.
2. **Record and evaluate** — `/gw-review` gives each finding one disposition: *blocks this merge*, *fix within the authorized scope*, *record or link for later evaluation*, or *Owner product decision*. Pre-existing or unrelated findings are recorded in the project's own register (or linked to an existing entry) for later evaluation.
3. **Link duplicates** — before recording, check for an existing finding, technical-debt item, or backlog item covering the same issue; link to it instead of creating a new one.
4. **Severity vs priority** — severity is the technical impact, assessed by the reviewer. Priority is the Owner's decision on when (or whether) to act.
5. **When the Owner decides** — a new requirement, a scope change, or a new backlog item needs an Owner admission decision. Fixes inside an already-authorized scope do not.
6. **Into the normal flow** — an admitted item follows the project's admission and ID-assignment rules, then enters `/doplan` → Plan → Owner Build like any other work. Admission alone does not start execution.

**Portability:** projects choose their own finding ID families, register location, numbering format, backlog ID namespace, and severity/status vocabulary. A central ID index is optional — useful only when several registers share one numbering namespace. Each project's source-of-truth locations are respected; in KSS projects `.cursor/` remains execution authority.

### Example

| Step | Content |
|------|---------|
| Observation | During `/gw-review` of a feature branch, an audit-logging test fails |
| Evidence | The same test fails on `main` at the merge base — command output attached |
| Classification | Status: Confirmed · Origin: Baseline (not caused by this change) · Severity: Medium · Priority: Owner decides |
| Disposition | Record or link — linked to existing `<FINDING-ID>` if present, otherwise recorded in the project's register; does not block this merge |
| Optional admission | The Owner admits it as `<BACKLOG-ID>` in the project's registry; it then goes through `/doplan` → Plan → Owner Build |

---

## Removed commands

These commands were retired; their functions live on as shown. Do not invoke them.

| Removed | Function now in |
|---------|-----------------|
| `/gw-triage` | `/doplan` Phase 3.5 — clarity, safest default, destination, decision check |
| `/gw-riskcheck` | `/doplan` Phase 3.5 — conditional risk block, CAB need, rollback, Doctrine_002 escalation |
| `/gw-handoff` | Normal agent reporting in-session; `/session-end` persists HANDOFF |
| `/global-workflow` | Already superseded; same mapping as above |

---

## Related resources

| Resource | Purpose |
|----------|---------|
| [`safe-coding-practices`](../../skills/safe-coding-practices/SKILL.md) skill | Evidence-based changes, rollback |
| Project `.cursor/rules/` (if present) | Project-specific boundary/placement rules (applied by `/doplan`) |
| [ADR command guide](adr-command.md) | `/dec` / `/adr` for architectural decisions |
| [Boot commands paths](boot-commands-paths.md) | Canonical `.cursor/memory/` paths |
| [KSS Handbook](../handbook.md) | KSS overview |

---

## Ready-to-use prompts

| Scenario | Prompt |
|----------|--------|
| Start session | `/boot` then "Continue from handoff" |
| New plan (scope, risk, backlog) | `/doplan` then describe intent; use the brief in Plan mode |
| Review before merge | `/gw-review` then name the branch/base or paste the diff |
| Merge locally | `/git-finalize` |
| Close and publish | `/session-end` |
| Record a decision | `/dec` then paste the discussion |
| Checkpoint | `/git-save` |
