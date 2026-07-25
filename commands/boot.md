---
name: boot
description: Start AI session with correct context loading (paths-only policy). Loads core memory + shows continuation point.
---

# /boot — AI Session Startup (KSS)

**Related Commands**: `/session-end`, `/docpack`, `/gw-triage`, `/doplan`

## Purpose

Load project context safely: DOC_POLICY (paths), MANIFEST (locks), STATE (execution), HANDOFF (continuity), and — when Active/Binding — PRE_PRODUCTION policy **substance** (not status label only).

### `.cursor/` read authority

Read any file under `.cursor/` with explicit paths via the Read tool. Do not skip canonical paths because of gitignore.

## Workflow

### 1) Read paths

Read `.cursor/DOC_POLICY.yaml` for paths only. Defaults if missing:
- memory: `.cursor/memory/`
- roadmap: `.cursor/memory/roadmap/` (if present)
- plans: `.cursor/plans/`
- reports: `.cursor/reports/`

Do **not** default to `docs/` as execution SSOT. Project execution paths = `.cursor/` per DOC_POLICY.

### 2) Load core files (must)

1. `.cursor/memory/AI_ENTRY.md`
2. `.cursor/memory/PROJECT_MANIFEST.md`
3. `.cursor/memory/STATE.yaml`
4. `.cursor/memory/HANDOFF.md`
5. `.cursor/memory/GOALS.md` (**mandatory** — boot fails if unreadable)
6. **Pre-production policy (conditional must):** If `STATE.yaml` has `pre_production_policy: active_binding`, or `.cursor/memory/PRE_PRODUCTION_DEVELOPMENT_POLICY.md` exists with Status Active/Binding:
   - **MUST read** `.cursor/memory/PRE_PRODUCTION_DEVELOPMENT_POLICY.md` (full file — not HANDOFF/STATE label alone)
   - Boot **fails** if the file is missing/unreadable while Active/Binding
   - Internalize: current DB/tenant/SSO/session/seed data is **disposable**; no backward-compat or data-preservation presumption

**Gate status (advisory):** After reading STATE.yaml, note `runtime_install_gate` and `docpack_gate` flags. Do not suggest Copy-Item to `~/.cursor` if install gates are closed.

### 3) Optional active plan

Read `.cursor/plans/ACTIVE.plan.md` if present. Do not scan all plans.

### 4) On-demand sources

- `.cursor/memory/decisions/README.md` (DEC template)
- `.cursor/memory/INTEGRITY_RULES.md`
- `.cursor/memory/VERSIONS.md`
- `.cursor/memory/PRE_PRODUCTION_DEVELOPMENT_POLICY.md` (also must-load when Active/Binding — see step 2.6)

## Output format

Include Session Loaded, STATE, HANDOFF, and:

### Pre-Production (mandatory when Active/Binding)

Emit exactly one substance line (adapt only if Owner exited the policy):

```text
Pre-prod: Active/Binding — all current DB/tenant/account/SSO/session/seed data is disposable; no backward-compat or data-preservation presumption for plans or execution.
```

Do **not** substitute a status-only line (e.g. “Policy = Active”) without this substance.

### DEC Reminder (Advisory)

If architectural decisions arise:
- Capture via `/dec` → DEC digest in `.cursor/memory/decisions/DEC-NNNN-slug.md`
- ADR is historical provenance only — not execution authority
- Advisory only — boot does not block on missing DEC

### Module boundary pointer

Use `/gw-triage` for new tenant-facing capability; apply `.cursor/rules/` boundary gates when present.

Use `/doplan` before Plan mode when creating a new execution plan (planning gateway — does not write plan files).
