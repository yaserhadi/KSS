/en

---
name: preplan
description: Pre-planning gateway — researches the request and project governance and produces a copy-ready Pre-Plan Charter for Cursor Plan Mode. Does not write plans, execute work, edit BACKLOG, or authorize Build.
---

# /preplan — Pre-Plan Charter (KSS)

**Related Commands**: `/en`, `/boot`, `/dec`, `/gw-review`

## Purpose

Produce the **Pre-Plan Charter**: a self-contained, copy-ready document that prepares Cursor Plan Mode to research and write the actual execution plan.

The Charter is **not an execution plan** and carries **no Build authorization**.

`/preplan` does **not**:

- Decide whether a plan is needed (once invoked, produce the Charter)
- Create or edit `.cursor/plans/*.plan.md` files
- Execute plan todos or any implementation task
- Modify project code, governance files, or `BACKLOG.md` (unless the owner **explicitly** asks for a backlog edit)
- Mint or reserve CAP-IDs / BK-IDs
- Approve architectural decisions
- Complete Governance Applicability (Plan mode completes it when the adopting project requires it)
- Resolve architecture/authority conflicts (surface uncertainty; cite project conflict governance if present)
- Authorize Build

**Correct chain:**

```text
User intent  →  /preplan  →  Pre-Plan Charter  →  Owner switches to Plan Mode and pastes it
             →  Plan file  →  iterative review in the same Plan conversation  →  Owner Build
```

`/boot` loads session context separately. `/preplan` prepares the Charter only; execution follows Cursor's Plan → Build workflow, and only the Owner presses Build.

### `.cursor/` read authority

Read any file under `.cursor/` with explicit paths via the Read tool. Do not skip canonical paths because of gitignore.

---

## Language (mandatory)

The first line of this command file is `/en`. The entire **Pre-Plan Charter** and all `/preplan` narrative output MUST be **English only**, regardless of the user's input language. Code, file paths, and CAP-IDs stay as-is.

---

## Workflow

### Phase 0 — Language

`/en` applies. All output in English only.

### Phase 1 — Context (read-only)

1. Read `.cursor/DOC_POLICY.yaml` for paths. Defaults if missing:
   - memory: `.cursor/memory/`
   - plans: `.cursor/plans/`
   - reports: `.cursor/reports/`
   - roadmap: `.cursor/memory/roadmap/` (if present)
2. Read `.cursor/memory/AI_ENTRY.md`
3. Read `.cursor/memory/STATE.yaml` and `.cursor/memory/HANDOFF.md` (advisory)
4. Optionally read `.cursor/plans/ACTIVE.plan.md` — do not scan all plans

Do **not** default to `docs/` as execution SSOT. Project execution paths = `.cursor/` per DOC_POLICY.

### Phase 2 — CAP / backlog (conditional)

**Backlog-registry mode** — if `.cursor/memory/roadmap/BACKLOG.md` exists (or the backlog path named by project DOC_POLICY / AI_ENTRY):

1. Read the backlog README when present (pre-plan dedup rules)
2. Search BACKLOG for CAP-ID / BK-ID and capability name (semantic duplicates, not just exact title)
3. `rg` `.cursor/plans/`, `.cursor/reports/`, `.cursor/memory/decisions/` for `capability_id` / `capability_ids` / `backlog_item_id` when relevant
4. Set binding status to exactly one of:
   - `bound_existing_cap`
   - `proposed_new_cap`
   - `conflict_or_overlap`
5. Include suggested `capability_id` / `backlog_item_id` for Plan mode frontmatter when bound
6. Remind: first plan for a CAP/BK → backlog status `planned → partial` (edit BACKLOG only if owner explicitly asks)
7. **Never** mint or reserve CAP-ID / BK-ID in plans, reports, or discussion. **Never** edit BACKLOG in `/preplan` unless owner explicitly asks.
8. **Finding vs admitted item** — state whether the intent is a **finding awaiting evaluation** (review observation, defect, deviation, technical debt, product idea) or an **admitted item** already in the registry. Check coverage by existing items and linked findings; link duplicates instead of proposing new ones.
9. Follow the project's own admission and ID-assignment rules (backlog README or project governance).
10. Backlog admission is not a Build instruction; an admitted item enters the normal `/preplan` → Plan → Build flow.

**Portable mode** — if no backlog registry:

- Produce a normal Pre-Plan Charter with scaffold hints
- CAP binding: `N/A` with reason "no backlog registry in project"

### Phase 2.5 — Decision registry / Active decision candidates (generic)

**Portable.** Do **not** hard-code project-specific decision IDs, backlog IDs, product domains, or project governance law into this command.

When the project exposes a decision registry (common convention: `.cursor/memory/decisions/README.md`, or the path named by project `AI_ENTRY`):

1. Read the registry index when present.
2. Enumerate decision files under the project decisions directory (e.g. `DEC-*.md` or the project’s declared naming).
3. Keep only decisions with **Active** status; **exclude Draft** and **Superseded**.
4. Against the **task scope** (user intent + backlog binding + declared domains/surfaces):
   - match registry titles/rows
   - search Active decision **body** text (Context, Decision, Forbidden, Allowed, Impacted files/modules, Verification, locks, related refs — as the project structures them)
5. Emit **candidate** applicable authorities (IDs + titles + paths) — not a proven complete set.
6. Surface **uncertainty** and possible conflicts; **do not** silently resolve architecture/authority disagreements.
7. If no registry exists: state `Decision registry: none detected` and skip candidate listing (continue the portable Charter — do **not** fail merely because no registry exists).

Optional helper (when present in the adopting project): run the project’s Active-decision list script as a discovery aid only. Such scripts are **project-owned**, not mandatory KSS mechanisms.

Candidate lists are **helpers**. Plan mode must complete `## Governance Applicability` when the adopting project requires it. Project law decides whether GAB is mandatory and how conflicts are arbitrated. Preflight (when project law requires it) verifies evidence — scripts and `/preplan` must **not** claim semantic architecture correctness or full applicability proof.

### Phase 3 — Scaffold hints (for Plan mode — do not write files)

Suggest (do not create):

- Proposed plan title
- Filename per project `.cursor/rules/plans-location.mdc` if present: `<name>_<YYYY-MM-DD-HHmm>_<hash>.plan.md`
- `capability_id` / `backlog_item_id` in frontmatter when CAP/BK is bound
- `## References` template (Lock, Verification, Evidence paths per project rules)
- `## Governance Applicability` skeleton **when the adopting project requires it** (domains, modules/planes, matched locks/Active decisions/verification-or-integrity/rules, N/A, conflict classification per project law, verification)
- `last_updated` (optional, informational) in frontmatter

### Phase 3.5 — Scope, destination and risk (pre-implementation)

Proportional to the task: a docs-only or trivial change gets one line per item; sensitive work gets the full blocks.

1. **Clarity** — if the request is ambiguous or missing critical context, ask 1–2 critical questions only (scope, environment, constraints, expected outcome). Otherwise propose the safest default: reversible over irreversible, additive over destructive.
2. **Destination** — for feature or structural work, apply the project's placement/boundary rule under `.cursor/rules/` if one exists (existing module / kernel / new unit) and cite it. If none exists, state destination at a high level. Re-check when scope grows into a full feature surface.
3. **Architectural decision check** — apply the Decision Indicators below. If a decision needs recording and no Active DEC covers it, recommend `/dec`.
4. **Risk block** — only when a trigger applies: production-impacting, security-sensitive, data-affecting (schema, migrations, bulk updates), access-control/permission, high-risk or irreversible, cross-module or system-wide.
   - Categories: Security (auth, permissions, secrets, injection, XSS) · Compliance (audit trails, retention, PII — cite applicable requirements, do not assume specific frameworks) · Production (live impact, downtime, data loss) · Data (schema, migrations, bulk updates).
   - Level, based on safeguards (backup, rollback, staging, monitoring) and blast radius — not on sensitivity alone:
     - **Critical** — rollback untested, or backup/recovery cannot be confirmed, or no staging, or safeguards absent/uncertain
     - **High** — rollback tested, backup available, batching and monitoring in place, but significant production impact
     - **Medium** — rollback exists, limited production impact, staging available, safeguards confirmed
     - **Low** — minimal production impact, well-tested rollback, staging available, all safeguards in place
   - Assumptions (inferred vs stated), CAB review required Yes/No with reason, production approval required Yes/No, risks with mitigations, rollback and recovery.
   - No trigger applies → one line: `Risk: Low — <reason>`.
5. **Doctrine_002 / Layer Responsibility Check** — when the work touches governance layers (L1/L2/L3, runtime install, authority files): run [`LAYER_VIOLATION_CHECKLIST.md`](../docs/LAYER_VIOLATION_CHECKLIST.md) per [`KSS_Doctrine_002.md`](../docs/KSS_Doctrine_002.md); adopting project `.cursor/CURSOR_RUNTIME.md` wins when present. Otherwise N/A. Any **Fail** → risk level at least **High**.

This assessment informs the plan; it is not an approval step. CAB approval, where genuinely required, follows the user/project CAB rule.

**Decision Indicators** (trigger a `/dec` recommendation):
- Trade-off language: "chose X over Y", "decided to", "alternative considered"
- New patterns: database schema, API endpoint design, authentication flow
- Breaking changes: contract modifications, interface changes
- Security boundaries: permission model, data access rules
- Module boundaries: new module, cross-module dependency

### Phase 3.6 — Continuing applicability (Plan Mode and Build)

`/preplan` makes the **initial** placement and risk assessment. It does not replace the project's own controls:

- Project rules and Active decisions stay independently binding during Plan Mode and Build, whether or not any slash command runs.
- When scope changes, re-evaluate only the affected checks — scope, destination, module boundary, or risk. Do **not** re-run `/preplan` or create a new plan for changes that stay inside the approved scope.
- A material architectural or scope change follows the adopting project's conflict-resolution and plan-revision rules.
- Mentioning a slash command inside a rule or document does not execute that command.

### Phase 4 — Gates (advisory)

Report required gates in the Charter; do not execute todos:

| Check | When |
|-------|------|
| Branch | Not `main`/`master` for implementation work — cite `git branch --show-current` if run |
| Scope / destination | Phase 3.5 items 1–2 — always, proportional to the task |
| CAB review | When Phase 3.5 risk triggers apply (per user/project CAB rule) |
| `plan-preflight-gate` | If project has `.cursor/rules/plan-preflight-gate.mdc` and plan touches sensitive domains |
| `MODULE-CREATION-GATE` | New tenant-facing capability — cite `.cursor/rules/module-creation-gate.mdc` if present |

### Phase 5 — Emit the Charter only

1. Output the **Pre-Plan Charter** as **one fenced Markdown block** (use a four-backtick fence so inner code blocks survive), ready to copy and paste into a fresh Plan Mode conversation. It must be complete on its own: do not rely on earlier chat context.
2. After the block, add one hand-off line: "Switch to Plan Mode and paste the Charter to create the execution plan."
3. **Optional automatic transition (not yet verified; subject to runtime integration verification):** when the agent's native mode-switch to Plan Mode is available in the current Cursor session, the agent may *offer* the switch. The switch happens only with the Owner's consent; the Charter stays in the conversation; Build is never triggered. If the mode-switch is not available, say nothing about it. The manual switch-and-paste above is the canonical method.

**Do not** write plan files. **Do not** execute plan todos.

---

## Output format (mandatory)

````markdown
## Pre-Plan Charter

> Pre-Plan Charter — not an execution plan; no Build authorization.

### 1. Request and intended outcome
- Request:
- Intended outcome:

### 2. Scope, exclusions, assumptions
- In scope:
- Out of scope:
- Assumptions (stated / inferred):

### 3. Project context and source references
- Repository / branch / tip:
- Sources read: (paths)
- Relevant existing behavior:

### 4. Backlog / capability binding
| Field | Value |
|-------|-------|
| Binding status | bound_existing_cap / proposed_new_cap / conflict_or_overlap / N/A |
| CAP-ID / BK-ID | existing ID / pending / N/A (never minted here) |
| Intent status | finding — awaiting Owner evaluation / admitted item <ID> / propose admission |
| Reason | ... |

### 5. Existing modules and proposed destination
- Request clarity: Clear / Needs clarification (questions, if any)
- Proposed approach: [safest default]; Reversible: Yes / No
- Project boundary rule: [path under .cursor/rules/ or N/A]; Destination: [per rule]

### 6. Applicable decisions and governance constraints
- Decision registry: present path / none detected
- Candidate Active decisions: (ID — title — path) or none
- Architectural decision check: involves architectural choice Yes / No; type; existing DEC covers it / none found; `/dec` recommended Yes / No
- Governance Applicability candidates: domains / modules / planes; matched rules and gates; uncertainty or possible conflicts; classification hint (project-defined; do not auto-resolve)
- Note: candidates are not a complete applicability proof; Plan Mode completes `## Governance Applicability` when the project requires it. Do not cite Draft decisions as law.

### 7. Risks and mitigations
- Triggers: [list] / none → `Risk: Low — <reason>`
- Categories: Security / Compliance / Production / Data
- Risk level: Low / Medium / High / Critical — rationale (safeguards, blast radius, reversibility)
- CAB review required: Yes / No — reason; Production approval required: Yes / No
- Risks and mitigations:
- Rollback / recovery:
- Doctrine_002 checklist: Pass / Fail / N/A per check (Fail → at least High)

### 8. Verification and acceptance expectations
- Required gates: (Phase 4)
- Tests / checks expected:
- Acceptance criteria:

### 9. Open questions and Owner decisions
- (or none)

### 10. Instructions to Cursor Plan Mode
- Research and validate this Charter against the actual codebase and project governance; correct anything that does not hold.
- Confirm or correct the destination and the risk assessment.
- Produce the execution plan: todos, the project's required sections (for example References, Governance Applicability, storage-mode checks), and scaffold hints from this Charter.
- Do not start implementation. Present the plan for Owner review and stop. Only the Owner presses Build.
````

---

## Notes

- User may describe intent in Arabic or any language; the Charter is always English (`/en`).
- For architectural choices during planning, note if `/dec` → DEC digest may be needed later.
- Conflict with existing CAP or governance: cite project conflict-governance / Rule 7 if present; do not auto-resolve.
- Project `AI_ENTRY` may name the local decision-registry convention and must **point** to `/preplan` for discovery — it must not re-implement this workflow.
- Project law owns: whether GAB is mandatory, sensitive-work rules, conflict arbitration, and any local preflight scripts.
- Iterative plan review (external reviewer findings pasted into the same Plan conversation) does not need `/preplan` again. See the [command workflow guide](../docs/reference/command-workflow.md#planning-workflow-owner-controlled).
