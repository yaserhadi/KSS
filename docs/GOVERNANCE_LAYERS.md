# Governance Layers

**Audience:** KSS adopters, AI agents, architects  
**See also:** [philosophy.md](../philosophy.md), [handbook.md](handbook.md)

---

## Reading order (when sources align)

| Priority | Layer | Location |
|----------|-------|----------|
| 1 | Enforceable locks | `PROJECT_MANIFEST.md` |
| 2 | Canonical decisions | `memory/decisions/DEC-*.md` |
| 3 | Verification | `INTEGRITY_RULES.md` |
| 4 | Runtime position | `STATE.yaml`, `HANDOFF.md` |
| 5 | Agent rules | `.cursor/rules/*.mdc` |
| 6 | Historical evidence | `.cursor/reports/` |
| 7 | Temporary intent | `.cursor/plans/` (active only) |

This is a **reading order**, not a silent override stack. On conflict → Evidence Conflict Report + owner arbitration (INTEGRITY Rule 7).

---

## Truth types

| Type | Sources |
|------|---------|
| Behavioral | code, tests |
| Architectural | MANIFEST, DEC, INTEGRITY |
| Operational | STATE, HANDOFF |
| Historical | reports, archived plans |

---

## Artifact taxonomy

| Class | Examples | Agent derives behavior? |
|-------|----------|-------------------------|
| **Authoritative** | MANIFEST, DEC-*, INTEGRITY_RULES | Yes |
| **Operational** | STATE, HANDOFF, ACTIVE.plan | Yes (session) |
| **Temporary** | active `plans/*.plan.md` | Yes — scoped to todos |
| **Evidentiary** | `reports/*` | No — context only |
| **Informational** | GOALS, ROADMAP, LESSONS, README | No |

### Agent rules

- Do **not** treat a **report** as authoritative over **DEC**
- Do **not** treat an **archived/completed plan** as current over **STATE/HANDOFF**
- Do **not** treat **informational** files as execution gates

---

## Rule taxonomy

| Class | Installed to | Examples |
|-------|--------------|----------|
| Portable global | `~/.cursor/rules/` | plans-location, reports-location-global, git-main-merge-only |
| Project optional | `<project>/.cursor/rules/` | plan-preflight, plan-completion, module-creation-gate |
| Project-only | Never in KSS install | Project-specific checklists, local git preferences |

Templates for project optional rules: `templates/project-rules/`

---

## Command taxonomy

| Category | Examples | Location |
|----------|----------|----------|
| Runtime | `/boot`, `/session-end`, `/en`, `/ar`, `/aren` | `~/.cursor/commands/` |
| Governance | `/gw-triage`, `/gw-riskcheck`, `/gw-review`, `/gw-handoff` | `~/.cursor/commands/` |
| Operational | `/git-prepare`, `/git-save`, `/git-finalize` | `~/.cursor/commands/` |
| Documentation | `/docpack`, `/adr` (→ DEC digest) | `~/.cursor/commands/` |

---

## Post-docs SSOT (Rule 1)

During development, `.cursor/` is the only execution authority. Do not derive behavior from deleted or legacy `docs/` trees.

Phase C may add human-facing documentation — generated artefacts only, never SSOT.
