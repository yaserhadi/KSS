/en

---
name: doplan
description: Governed planning gateway — CAP/backlog binding, gates, scaffold hints. English-only brief for Plan mode. Does not write plans or edit BACKLOG.
---

# /doplan — Governed Planning Gateway (KSS)

**Related Commands**: `/en`, `/gw-triage`, `/gw-riskcheck`, `/boot`, `/adr`

## Purpose

Produce the **DoPlan Governance Brief** required before plan generation.

`/doplan` is a **planning gateway only**. It does **not**:

- Decide whether a plan is needed (once invoked, produce the brief)
- Create or edit `.cursor/plans/*.plan.md` files
- Execute plan todos
- Edit `BACKLOG.md` unless the owner **explicitly** asks

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

Do **not** default to `docs/` as execution SSOT.

### Phase 2 — CAP / backlog (conditional)

**Jabal mode** — if `.cursor/memory/roadmap/BACKLOG.md` exists:

1. Read `.cursor/memory/roadmap/README.md` (pre-plan dedup rules)
2. Search BACKLOG for CAP-ID and capability name (semantic duplicates, not just exact title)
3. `rg` `.cursor/plans/`, `.cursor/reports/`, `.cursor/memory/decisions/` for `capability_id` / `capability_ids` when relevant
4. Set binding status to exactly one of:
   - `bound_existing_cap`
   - `proposed_new_cap`
   - `conflict_or_overlap`
5. Include suggested `capability_id` for Plan mode frontmatter when bound
6. Remind: first plan for a CAP → BACKLOG status `planned → partial` (edit BACKLOG only if owner explicitly asks)
7. **Never** mint CAP-ID in plan files. **Never** edit BACKLOG in `/doplan` unless owner explicitly asks.

**Portable mode** — if no BACKLOG.md:

- Produce a normal governed planning brief with scaffold hints
- CAP binding: `N/A` with reason "no backlog registry in project"

### Phase 3 — Scaffold hints (for Plan mode — do not write files)

Suggest (do not create):

- Proposed plan title
- Filename per project `.cursor/rules/plans-location.mdc` if present: `<name>_<YYYY-MM-DD-HHmm>_<hash>.plan.md`
- `capability_id` in frontmatter when CAP is bound
- `## References` template (Lock, Verification, Evidence paths per project rules)
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

**Backlog Action**
- None / propose row / owner approval required
- Note: /doplan does not edit BACKLOG unless owner explicitly asks.

**Next Step**
Use this brief in Plan mode to generate the plan file.
```

---

## Notes

- User may describe intent in Arabic or any language; brief is always English (`/en`).
- For architectural choices during planning, note if `/adr` → DEC digest may be needed later.
- Conflict with existing CAP or governance: cite Rule 7 / conflict-governance if project has it; do not auto-resolve.
