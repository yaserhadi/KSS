/en

---
name: doplan
description: Governed planning gateway — CAP/backlog binding, decision-registry candidates, gates, scaffold hints. English-only brief for Plan mode. Does not write plans or edit BACKLOG.
---

# /doplan — Governed Planning Gateway (KSS)

**Related Commands**: `/en`, `/gw-triage`, `/gw-riskcheck`, `/boot`, `/dec`

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

`/boot` loads session context separately. It does **not** start execution from a plan. Agent mode may execute plan todos later, after gates pass.

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
7. **Never** mint CAP-ID / BK-ID in plan files. **Never** edit BACKLOG in `/doplan` unless owner explicitly asks.

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
- `last_updated` ISO 8601 in frontmatter

### Phase 4 — Gates (advisory)

Report required gates; do not execute todos:

| Check | When |
|-------|------|
| Branch | Not `main`/`master` for implementation work — cite `git branch --show-current` if run |
| `/gw-triage` | Scope or module destination unclear |
| `/gw-riskcheck` | Production, security, auth, tenancy, schema, RBAC, bulk data |
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
- Note: /doplan does not edit BACKLOG unless owner explicitly asks.

**Next Step**
Use this brief in Plan mode to generate the plan file.
```

---

## Notes

- User may describe intent in Arabic or any language; brief is always English (`/en`).
- For architectural choices during planning, note if `/dec` → DEC digest may be needed later.
- Conflict with existing CAP or governance: cite project conflict-governance / Rule 7 if present; do not auto-resolve.
- Project `AI_ENTRY` may name the local decision-registry convention and must **point** to `/doplan` for discovery — it must not re-implement this workflow.
- Project law owns: whether GAB is mandatory, sensitive-work rules, conflict arbitration, and any local preflight scripts.
