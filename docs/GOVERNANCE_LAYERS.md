# Governance Layers

**Audience:** KSS adopters, AI agents, architects  
**See also:** [philosophy.md](../philosophy.md), [handbook.md](handbook.md), [KSS_EVOLUTION.md](KSS_EVOLUTION.md) (EVOL-005)

---

## KSS Governance Core (executive reference)

The three doctrines form the **KSS Governance Framework** nucleus. Read together before extraction, export, or runtime install.

```yaml
KSS_Governance_Core:
  Doctrine_001:
    name: Extraction Direction
    governs: extraction_flow
    doc: GOVERNANCE_LAYERS.md#kss_doctrine_001--extraction-direction
    rule: Jabal → Review → Generalize → KSS → Optional ~/.cursor install (LAST)

  Doctrine_002:
    name: Layer Responsibility Principle
    governs: authority_and_layer_responsibilities
    doc: KSS_Doctrine_002.md
    rule: Layer 1 governs · Layer 3 teaches · Layer 2 distributes
    enforcement: LAYER_VIOLATION_CHECKLIST.md (gw-* commands)

  Doctrine_003:
    name: Portable Structure vs Project Content
    governs: export_content_boundaries
    doc: KSS_Doctrine_003.md
    rule: Export structure and decision patterns only; replace project content after copy
```

**Apply order:** 001 (flow) → 003 (what to export) → 002 (layer authority during review and install).

---

## KSS_Doctrine_001 — Extraction Direction

```yaml
KSS_Doctrine_001:
  name: Extraction Direction
  rule: Jabal → Review → Generalize → KSS → Optional Runtime Install
  forbids: KSS → ~/.cursor without owner-approved runtime_install_gate
```

**Wrong:** bulk `Copy-Item KSS → ~/.cursor` before review.  
**Right:** reference project `.cursor/` → classify → generalize → KSS draft → owner-approved install last.

---

## KSS_Doctrine_002 — Layer Responsibility

See [KSS_Doctrine_002.md](KSS_Doctrine_002.md). Summary:

- **L1** governs (MANIFEST, DEC, INTEGRITY)
- **L2** distributes portable patterns (KSS → `~/.cursor/`)
- **L3** teaches reference implementation (project `.cursor/`)

Enforcement aid: [LAYER_VIOLATION_CHECKLIST.md](LAYER_VIOLATION_CHECKLIST.md) — used by `/gw-*` commands.

---

## KSS_Doctrine_003 — Portable Structure vs Project Content

See [KSS_Doctrine_003.md](KSS_Doctrine_003.md). Summary:

- Export **structure** (gates, decision tables, reading chains)
- Replace **content** (DEC paths, module names, project workflows) per adopting repo
- `templates/project-rules/` are patterns, not copy-paste project rules

---

## Layer dependency (Jabal lesson)

```text
Layer 3 (Project SSOT)  — teaches patterns
      ↓ derives
Layer 2 (Runtime/KSS)   — distributes portable patterns
      ↓ consumes
Layer 1 (Authority)     — governs both
```

Plain language: **Layer 1 governs · Layer 3 teaches · Layer 2 distributes**

| Layer | Location | Role |
|-------|----------|------|
| L1 | `.cursor/memory/` | MANIFEST, DEC, INTEGRITY |
| L2 | KSS → `~/.cursor/` | Portable runtime |
| L3 | `<project>/.cursor/` | Project gates, plans, reports |

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
| Documentation | `/docpack`, `/dec` (`/adr` deprecated alias) | `~/.cursor/commands/` |

---

## Post-docs SSOT (Rule 1)

During development, `.cursor/` is the only execution authority. Do not derive behavior from deleted or legacy `docs/` trees.

Phase C may add human-facing documentation — generated artefacts only, never SSOT.
