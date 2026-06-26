---
name: boot
description: Start AI session with correct context loading (paths-only policy). Loads core memory + shows continuation point.
---

# /boot — AI Session Startup (KSS)

**Related Commands**: `/session-end`, `/docpack`, `/gw-triage`, `/doplan`

## Purpose

Load project context safely: DOC_POLICY (paths), MANIFEST (locks), STATE (execution), HANDOFF (continuity).

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

**Gate status (advisory):** After reading STATE.yaml, note `runtime_install_gate` and `docpack_gate` flags. Do not suggest Copy-Item to `~/.cursor` if install gates are closed.

### 3) Optional active plan

Read `.cursor/plans/ACTIVE.plan.md` if present. Do not scan all plans.

### 4) On-demand sources

- `.cursor/memory/decisions/README.md` (DEC template)
- `.cursor/memory/INTEGRITY_RULES.md`
- `.cursor/memory/VERSIONS.md`

## Output format

Include Session Loaded, STATE, HANDOFF, and:

### DEC Reminder (Advisory)

If architectural decisions arise:
- Capture via `/adr` → DEC digest in `.cursor/memory/decisions/DEC-NNNN-slug.md`
- Legacy `docs/architecture/ADR/` is not execution authority
- Advisory only — boot does not block on missing DEC

### Module boundary pointer

Use `/gw-triage` for new tenant-facing capability; apply `.cursor/rules/` boundary gates when present.

Use `/doplan` before Plan mode when creating a new execution plan (planning gateway — does not write plan files).
