/en

---
name: doplan
description: Governed planning gateway — CAP/backlog binding, decision-registry candidates, scope/destination triage, pre-implementation risk assessment, gates, scaffold hints. English-only brief for Plan mode. Does not write plans or edit BACKLOG.
---

# /doplan — Governed Planning Gateway (KSS)

**Related Commands**: `/en`, `/boot`, `/dec`, `/gw-review`

## Purpose

Produce the **DoPlan Governance Brief** required before plan generation.

`/doplan` is a **planning gateway only**. It does **not**:

- Decide whether a plan is needed (once invoked, produce the brief)
- Create or edit `.cursor/plans/*.plan.md` files
- Execute plan todos
- Edit `BACKLOG.md` unless the owner **explicitly** asks
- Complete Governance Applicability (Plan mode completes it when the adopting project requires it)
- Resolve architecture/authority conflicts (surface uncertainty; cite project conflict governance if present)

**Correct chain:**

```text
User intent  →  /doplan  →  Governance Planning Brief  →  Plan mode  →  Plan file
```

`/boot` loads session context separately. `/doplan` prepares the planning brief only; execution follows Cursor's Plan → Build workflow.

### `.cursor/` read authority

Read any file under `.cursor/` with explicit paths via the Read tool. Do not skip canonical paths because of gitignore.

---

## Language (mandatory)

The first line of this command file is `/en`. The entire **DoPlan Governance Brief** and all `/doplan` narrative output MUST be **English only**, regardless of the user's input language. Code, file paths, and CAP-IDs stay as-is.

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
7. **Never** mint or reserve CAP-ID / BK-ID in plans, reports, or discussion. **Never** edit BACKLOG in `/doplan` unless owner explicitly asks.
8. **Finding vs admitted item** — state whether the intent is a **finding awaiting evaluation** (review observation, defect, deviation, technical debt, product idea) or an **admitted item** already in the registry. Check coverage by existing items and linked findings; link duplicates instead of proposing new ones.
9. Follow the project's own admission and ID-assignment rules (backlog README or project governance).
10. Backlog admission is not a Build instruction; an admitted item enters the normal `/doplan` → Plan → Build flow.

**Portable mode** — if no backlog registry:

- Produce a normal governed planning brief with scaffold hints
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
7. If no registry exists: state `Decision registry: none detected` and skip candidate listing (continue portable brief — do **not** fail merely because no registry exists).

Optional helper (when present in the adopting project): run the project’s Active-decision list script as a discovery aid only. Such scripts are **project-owned**, not mandatory KSS mechanisms.

Candidate lists are **helpers**. Plan mode must complete `## Governance Applicability` when the adopting project requires it. Project law decides whether GAB is mandatory and how conflicts are arbitrated. Preflight (when project law requires it) verifies evidence — scripts and `/doplan` must **not** claim semantic architecture correctness or full applicability proof.

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

### Phase 4 — Gates (advisory)

Report required gates; do not execute todos:

| Check | When |
|-------|------|
| Branch | Not `main`/`master` for implementation work — cite `git branch --show-current` if run |
| Scope / destination | Phase 3.5 items 1–2 — always, proportional to the task |
| CAB review | When Phase 3.5 risk triggers apply (per user/project CAB rule) |
| `plan-preflight-gate` | If project has `.cursor/rules/plan-preflight-gate.mdc` and plan touches sensitive domains |
| `MODULE-CREATION-GATE` | New tenant-facing capability — cite `.cursor/rules/module-creation-gate.mdc` if present |

### Phase 5 — Emit brief only

Output **only** the DoPlan Governance Brief block below. **Do not** write plan files. **Do not** execute plan todos.

---

## Output format (mandatory)

```markdown
## DoPlan Governance Brief

**CAP Binding**
| Field | Value |
|-------|-------|
| Binding status | bound_existing_cap / proposed_new_cap / conflict_or_overlap / N/A |
| CAP-ID | CAP-NNN / pending / N/A |
| Reason | ... |

**Planning Context**
- Proposed plan title:
- Suggested plan filename:
- Scope:
- Primary references:
- Required gates:

**Scope and Destination**
- Request clarity: Clear / Needs clarification (questions, if any)
- Proposed approach: [safest default]; Reversible: Yes / No
- Project boundary rule: [path under .cursor/rules/ or N/A]; Destination: [per rule]

**Risk Assessment (conditional)**
- Triggers: [list] / none → `Risk: Low — <reason>`
- Categories: Security / Compliance / Production / Data
- Risk level: Low / Medium / High / Critical — rationale (safeguards, blast radius, reversibility)
- Assumptions:
- CAB review required: Yes / No — reason; Production approval required: Yes / No
- Risks and mitigations:
- Rollback / recovery:
- Doctrine_002 checklist: Pass / Fail / N/A per check (Fail → at least High)

**Architectural Decision Check**
- Involves architectural choice: Yes / No; Decision type: DB / API / Security / Module / Integration / None
- Existing DEC covers this: DEC-NNNN / None found / N/A; DEC recommended (`/dec`): Yes / No

**Governance Applicability (candidates — complete in Plan mode)**
- Task / backlog item (if any):
- Declared domains / modules / planes (from intent):
- Decision registry: present path / none detected
- Candidate Active decisions: (ID — title — path) or none
- Uncertainty / possible conflicts: (or none noted)
- Classification hint: none noted / uncertain / project-defined (cite project conflict governance if present; do not auto-resolve; do not invent project law)
- Note: Candidates are not a complete applicability proof. Plan must complete ## Governance Applicability when the project requires it. Do not cite Draft decisions as law.

**Backlog Action**
- None / propose row / owner approval required
- Intent status: finding — awaiting Owner evaluation / admitted item <ID> / propose admission
- Note: /doplan does not edit BACKLOG unless owner explicitly asks.

**Next Step**
Use this brief in Plan mode to generate the plan file.
Plan mode presents the plan for Owner review and stops.
```

---

## Notes

- User may describe intent in Arabic or any language; brief is always English (`/en`).
- For architectural choices during planning, note if `/dec` → DEC digest may be needed later.
- Conflict with existing CAP or governance: cite project conflict-governance / Rule 7 if present; do not auto-resolve.
- Project `AI_ENTRY` may name the local decision-registry convention and must **point** to `/doplan` for discovery — it must not re-implement this workflow.
- Project law owns: whether GAB is mandatory, sensitive-work rules, conflict arbitration, and any local preflight scripts.
