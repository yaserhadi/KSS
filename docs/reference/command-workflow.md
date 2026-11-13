# KSS command workflow

**Audience:** Developers and operators using Cursor with KSS on a project — including people new to KSS.

**Purpose:** Explain **what** each KSS command does, **why** it helps, **when** to use it (and when not), and the **typical order**. This is the main human-facing command reference; other documents link here.

**This page is a human guide.** It does not replace the command files in [`commands/`](../../commands/) — those define agent behavior, output formats, and checks.

---

## Lifecycle

```text
/boot  →  /preplan  →  Plan Mode (paste Charter)  →  iterative review  →  Owner Build  →  /git-finalize (includes /gw-review)  →  /session-end
```

- `/preplan` produces a **Pre-Plan Charter**; Plan Mode turns it into the execution plan. See [Planning workflow](#planning-workflow-owner-controlled).
- Plan review is optional and happens in the same Plan conversation — no extra command.
- `/git-finalize` runs the `/gw-review` protocol before every merge. A standalone `/gw-review` is optional at any time (e.g. before Owner testing).
- This is a **reference workflow**, not a requirement to run every command for every task. Small or docs-only work skips steps (see [Common workflows](#common-workflows)).
- **Owner Build** is Cursor's native Build action on a plan. It is not an extra approval layer, and only the Owner presses it.

## Quick reference (cheat sheet)

| Phase | Command | When to use | Benefit | Side effects |
|-------|---------|-------------|---------|--------------|
| Session start | `/boot` | Start of a work session | Restores project context, state, last handoff | Analyzes only |
| Preparation | `/preplan` | A new plan is needed | Pre-Plan Charter: scope, destination, decisions, risk, backlog binding, verification expectations | Analyzes only (English Charter) |
| Plan | Cursor Plan Mode | Owner pastes the Charter | Researches and writes the execution plan for Owner review | Writes the plan file |
| Plan review (optional) | Same Plan conversation — no command | Owner wants an independent review | Questions, findings and changes are applied to the same plan | Updates the plan file |
| Execution | Owner Build | Plan accepted by the Owner | Executes the plan todos | Modifies files; may commit on a branch per plan |
| Review | `/gw-review` | Automatic inside `/git-finalize`; standalone optional | Finds defects, scope drift, security and rule violations | Analyzes only |
| Local merge | `/git-finalize` | Branch ready (tests green) | Reviews first; merges into local `main` only on Approve, deletes local branch, packages lifecycle artefacts | Commits, merges, modifies `.cursor/` artefacts — **no push** |
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

### `/preplan`

**In plain terms:** "What exactly are we doing, where does it belong, how risky is it, which backlog item does it serve, and what must the plan prove?" — **before** Plan Mode writes the plan.

- **What it does:** researches the request and the project's governance, then outputs a **Pre-Plan Charter** — one self-contained, copy-ready block. It covers: request and intended outcome; scope, exclusions and assumptions; project context and sources; backlog/capability binding; existing modules and proposed destination; applicable decisions and governance constraints; risks and mitigations (conditional risk block, CAB need, rollback); verification and acceptance expectations; open questions for the Owner; and instructions to Plan Mode.
- **What it does not do:** it does not write or edit a plan file, execute anything, change code, governance files or BACKLOG, mint or reserve backlog IDs, approve decisions, or authorize Build. The Charter is **not** an execution plan.
- **Why the Charter exists:** Plan Mode starts from a complete, governance-aware brief instead of a one-line request. Because the Charter is self-contained, it also works when pasted into a fresh Plan conversation.
- **Use when:** creating a new `.cursor/plans/*.plan.md` plan; the request is unclear; work may touch production, security, data, schema, access control, or several modules.
- **Proportional:** a docs-only change gets one line per item (`Risk: Low — <reason>`); sensitive work gets the full risk block.
- **Unnecessary when:** trivial edit with no plan file.
- **Full spec:** [`commands/preplan.md`](../../commands/preplan.md)

### Plan Mode and Owner Build

- **Plan Mode** receives the Charter, researches the implementation, writes the plan, and stops for Owner review.
- **Owner Build** executes the plan's todos within all project governance (branch rules, locks, decisions, tests).

### `/gw-review`

**In plain terms:** "Is this actual change set good to merge?"

- **Runs automatically:** inside `/git-finalize` before every merge (proportional — a trivial diff gets a short review with N/A where checks don't apply).
- **Standalone (optional):** an independent review at any time, e.g. before Owner testing.
- **Reuse:** an earlier review (standalone `/gw-review`, or a recorded Bugbot, security or human review) is reused only if it records the same `<base-sha>` and `<head-sha>` and its test evidence still applies; otherwise the review runs again.
- **Incomplete:** if the review cannot be completed (diff not computable, required evidence missing, scope unidentifiable), it is reported as incomplete; `/git-finalize` treats that as STOP.
- **Checks:** correctness, scope vs the approved plan, safety, security, boundaries, project conventions, test evidence, missing decisions, Doctrine_002 (N/A when irrelevant).
- **Verdicts:** **Approve** (no Critical findings; warnings recorded) · **Request Changes** (fixable issues must be resolved before merge) · **Block** (Critical: security/data-loss risk, Doctrine Fail, unapproved scope, missing required decision; re-review after fix).
- **Side effects:** none — analyzes only. A standalone verdict is **not** an instruction to merge or push; inside `/git-finalize`, Approve lets finalize continue.
- **Full spec:** [`commands/gw-review.md`](../../commands/gw-review.md)

### `/git-finalize`

**In plain terms:** "Close this branch onto local `main`."

- **Use when:** the feature branch is ready (tests and lint pass).
- **Does:** final commit on the branch, then the pre-merge review (the `/gw-review` protocol on `<base-sha>`...`<head-sha>`, or a valid reuse of an earlier review of the same SHAs), then `git merge --no-ff` into local `main`, deletes the local branch, runs merge-moment lifecycle packaging (HANDOFF, STATE, ACTIVE plan, BACKLOG row status, reports).
- **Review outcome:** **Approve** (advisory warnings allowed) continues automatically, with no Owner confirmation. **Request Changes**, **Block** or an incomplete review STOP before merge and branch deletion: local `main` stays unchanged and the feature branch is preserved. After fixes, run `/git-finalize` again.
- **Report:** includes the review verdict and the reviewed `<base-sha>`...`<head-sha>` range.
- **Does not:** push. Next required step is `/session-end`.
- **Full spec:** [`commands/git-finalize.md`](../../commands/git-finalize.md)

### `/session-end`

**In plain terms:** "Close the session properly."

- **Use when:** ending a session — always after `/git-finalize`, or alone when nothing was merged.
- **Does:** closeout audit (finalize integrity, packaging, plan location), repairs gaps via `/docpack`, writes `.cursor/memory/HANDOFF.md`, pushes `main` when product advanced (or records an Owner waiver).
- **Run in Agent mode** — Plan and Ask modes cannot write HANDOFF.
- **Full spec:** [`commands/session-end.md`](../../commands/session-end.md)

---

## Planning workflow (Owner-controlled)

The Owner decides when planning starts, when the plan is good enough, and when execution begins. Commands prepare and execute; they never decide on the Owner's behalf.

### Stage A — Preparation

1. The Owner invokes `/preplan` (with the request in the same message).
2. Cursor researches the project and produces the Pre-Plan Charter.
3. The Charter is displayed in a clean, copy-ready format.

### Stage B — Plan Creation

4. The Owner manually switches Cursor to Plan Mode.
5. The Owner pastes the Pre-Plan Charter and presses Enter.
6. Cursor researches the implementation and creates the actual plan.

### Stage C — Iterative Review

7. The Owner reviews the plan with ChatGPT or another external reviewer, if desired.
8. The Owner copies questions, findings and requested changes into the existing Cursor Plan conversation.
9. Cursor answers, investigates and updates the same plan.
10. This review/refinement loop continues until the plan is technically sound, complete and acceptable.

Each refinement happens in the same Plan conversation. Do **not** run `/preplan` again for a refinement, and do not create a duplicate plan. No extra command, approval layer or mandatory review report is involved.

### Stage D — Execution

11. ChatGPT or another reviewer may recommend that the plan is ready for Build.
12. The Owner makes the final decision and manually clicks **Build**.
13. Cursor executes the approved plan using applicable project governance.
14. The existing `/git-finalize` and `/session-end` workflow remains unchanged.

Review recommendations are advisory. **Only the Owner initiates Build.**

### External reviewers (optional)

An independent reviewer — ChatGPT, another AI assistant, or a human colleague — often catches gaps the planning agent missed. KSS has **no technical dependency** on any reviewer: Stage C is optional, and the loop works the same whoever the reviewer is. (The KSS Owner's own preference is ChatGPT as the independent reviewer.)

### Optional automatic transition (not yet verified)

**Canonical method:** the Owner switches to Plan Mode manually and pastes the Charter (Stage B).

**Optional capability — not yet verified; subject to runtime integration verification.** When the agent's native mode-switch to Plan Mode is available in the Cursor session, `/preplan` may *offer* to switch after showing the Charter. The switch happens only with the Owner's consent, the Charter stays in the same conversation, and Build is never triggered. This behavior has not yet been tested end to end; until it is, rely on the manual method. No script, browser automation or workaround is used to simulate a mode switch.

### When to press Build

Press Build when the plan:

- states its scope, exclusions and acceptance criteria clearly;
- has no open questions that change what will be built;
- includes the project's required sections (for example References, Governance Applicability, rollback or verification steps);
- has passed any review you chose to run, with each finding answered or deliberately deferred.

After Build, Cursor executes the plan's todos under the project's rules. When the branch is ready, run `/git-finalize` (review + local merge), then `/session-end` (closeout + push).

---

## Governance checks stay active

`/preplan` makes the **initial** module placement and risk assessment. It is a starting point, not a one-time gate:

- **Project rules and decisions bind independently.** During Plan Mode and Build, the project's rules and Active decisions apply whether or not any slash command runs.
- **Re-evaluate only what changed.** If scope shifts, re-check only the affected scope, destination, module-boundary or risk item. Do not run `/preplan` again or create a new plan for changes that stay inside the approved scope.
- **Material changes follow project law.** A material architectural or scope change follows the project's own conflict-resolution and plan-revision rules.
- **How Cursor activates project rules:**

| Kind | How it applies |
|------|----------------|
| Always-applied rule (`.mdc` with `alwaysApply: true`) | Loaded into every agent conversation automatically |
| Conditional rule (`.mdc` with `alwaysApply: false`, a `description`, or `globs`) | Applied when relevant files or requests match, or when the agent fetches it |
| Plain Markdown (for example a `README.md` or checklist under `.cursor/rules/`) | **Not** loaded automatically; it is documentation an agent reads on purpose |

- **User-level rule files** in `~/.cursor/rules/` are a different source; their loading is only partly documented. See [Global and user rules](how-to-make-global-rules.md).
- **Naming a command does not run it.** A rule that says "re-run the `/preplan` scope check" tells the agent what to evaluate; it does not execute `/preplan`.

---

## Worked example

A small feature: "Add an export button to the reports page."

1. **`/boot`** — the agent loads project context and reports the current branch and state.
2. **`/preplan` Add a CSV export button to the reports page** — the agent returns a Pre-Plan Charter, for example:

   ```text
   ## Pre-Plan Charter
   > Pre-Plan Charter — not an execution plan; no Build authorization.
   1. Request: CSV export on the reports page. Outcome: users download the current report as CSV.
   4. Binding: bound_existing_cap — <BACKLOG-ID>
   5. Destination: existing <reports-module> (per project boundary rule)
   7. Risk: Low — read-only export of data the user can already see
   8. Acceptance: export matches on-screen filters; permission checks unchanged
   10. Plan Mode: validate, write the plan, stop for Owner review
   ```

3. **Plan Mode** — the Owner switches to Plan Mode, pastes the Charter, presses Enter. Cursor writes the plan.
4. **Review** — the Owner asks an external reviewer to check the plan. The reviewer notes: "No test for large reports." The Owner pastes that finding into the same Plan conversation; Cursor adds a test todo and updates the plan.
5. **Build** — the Owner is satisfied and clicks Build. Cursor implements on a feature branch.
6. **`/git-finalize`** — pre-merge review returns Approve; the branch merges into local `main`.
7. **`/session-end`** — closeout passes; `main` is pushed.

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
| Plan review | Same Plan conversation (optional external reviewer) | Improves the plan before Build — advisory only |
| Review | `/git-finalize` step 3, or standalone `/gw-review` | Verdict on the change set — analysis only |
| Merge | `/git-finalize` | Local merge into `main` + lifecycle packaging |
| Closeout | `/session-end` | Verifies finalize and packaging; persists HANDOFF |
| Publication | `/session-end` | Pushes `main` (or records an Owner waiver) |

In-session summaries (files changed, tests, next steps, open questions) are normal agent output — no separate command is needed.

---

## Common workflows

| Workflow | Sequence |
|----------|----------|
| Feature work | `/boot` → `/preplan` → Plan Mode (paste Charter) → optional review loop → Owner Build → `/git-finalize` → `/session-end` |
| Small fix | Branch → fix → tests → `/git-finalize` → `/session-end` |
| Docs-only change | Branch → edit → `/git-finalize` (short review) → `/session-end` |
| Governance change | `/preplan` (Doctrine_002 check) → Plan Mode → optional review loop → Owner Build → `/git-finalize` → `/session-end` |
| Mid-session checkpoint | `/git-save` (and `/docpack` if governance artefacts need updating) |

---

## Findings to Backlog

**Principle:** Discussion is not authorization. A finding is not an approved backlog item. Backlog admission is not execution authorization.

1. **Why separate them** — keeping findings, product decisions, and implementation apart prevents silent scope growth and keeps every decision auditable.
2. **Record and evaluate** — during the pre-merge review in `/git-finalize` (or a standalone `/gw-review`), each finding gets one disposition: *blocks this merge*, *fix within the authorized scope*, *record or link for later evaluation*, or *Owner product decision*. Pre-existing or unrelated findings are recorded in the project's own register (or linked to an existing entry) for later evaluation.
3. **Link duplicates** — before recording, check for an existing finding, technical-debt item, or backlog item covering the same issue; link to it instead of creating a new one.
4. **Severity vs priority** — severity is the technical impact, assessed by the reviewer. Priority is the Owner's decision on when (or whether) to act.
5. **When the Owner decides** — a new requirement, a scope change, or a new backlog item needs an Owner admission decision. Fixes inside an already-authorized scope do not.
6. **Into the normal flow** — an admitted item follows the project's admission and ID-assignment rules, then enters `/preplan` → Plan → Owner Build like any other work. Admission alone does not start execution.

**Portability:** projects choose their own finding ID families, register location, numbering format, backlog ID namespace, and severity/status vocabulary. A central ID index is optional — useful only when several registers share one numbering namespace. Each project's source-of-truth locations are respected; in KSS projects `.cursor/` remains execution authority.

### Example

| Step | Content |
|------|---------|
| Observation | During the pre-merge review in `/git-finalize` (or a standalone `/gw-review`) of a feature branch, an audit-logging test fails |
| Evidence | The same test fails on `main` at the merge base — command output attached |
| Classification | Status: Confirmed · Origin: Baseline (not caused by this change) · Severity: Medium · Priority: Owner decides |
| Disposition | Record or link — linked to existing `<FINDING-ID>` if present, otherwise recorded in the project's register; does not block this merge |
| Optional admission | The Owner admits it as `<BACKLOG-ID>` in the project's registry; it then goes through `/preplan` → Plan → Owner Build |

---

## Removed and renamed commands

These commands were retired or renamed; their functions live on as shown. Do not invoke the old names.

| Old command | Function now in |
|-------------|-----------------|
| `/doplan` | Renamed to `/preplan` — output is now the Pre-Plan Charter |
| `/gw-triage` | `/preplan` Phase 3.5 — clarity, safest default, destination, decision check |
| `/gw-riskcheck` | `/preplan` Phase 3.5 — conditional risk block, CAB need, rollback, Doctrine_002 escalation |
| `/gw-handoff` | Normal agent reporting in-session; `/session-end` persists HANDOFF |
| `/global-workflow` | Already superseded; same mapping as above |

---

## Related resources

| Resource | Purpose |
|----------|---------|
| [`safe-coding-practices`](../../skills/safe-coding-practices/SKILL.md) skill | Evidence-based changes, rollback |
| Project `.cursor/rules/` (if present) | Project-specific boundary/placement rules — assessed first by `/preplan`; binding independently in Plan Mode and Build |
| [Decision digests guide](adr-command.md) | `/dec` for architectural decisions (`/adr` = deprecated alias) |
| [Boot commands paths](boot-commands-paths.md) | Canonical `.cursor/memory/` paths |
| [KSS Handbook](../handbook.md) | KSS overview |

---

## Ready-to-use prompts

| Scenario | Prompt |
|----------|--------|
| Start session | `/boot` then "Continue from handoff" |
| Prepare a new plan | `/preplan` then describe the intent; switch to Plan Mode and paste the Charter |
| Refine a plan after review | Paste the reviewer's questions and findings into the same Plan conversation |
| Independent review (optional) | `/gw-review` then name the branch/base or paste the diff |
| Review and merge locally | `/git-finalize` |
| Close and publish | `/session-end` |
| Record a decision | `/dec` then paste the discussion |
| Checkpoint | `/git-save` |
