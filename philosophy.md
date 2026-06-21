# KSS Philosophy

Owner: <KSS_Steward_Name>
Example: KSS Steward - John

---

## Core Principle

**Framework defines structure. Project defines content.**

The KSS Framework provides structure, templates, tooling, and install paths.
The project provides locks, DEC digests, STATE, evidence, and product constraints.

---

## SSOT Architecture (three layers)

| Layer | Path | Role |
|-------|------|------|
| **1. KSS Framework** | `<kss_repo>/` | Distributable product (~47 files) |
| **2. User install** | `~/.cursor/` | Runtime: commands, rules, skills, agents |
| **3. Project SSOT** | `<project>/.cursor/` | Content SSOT (~200+ files at maturity) |

```text
Jabal (reference) → generalize patterns → KSS → ~/.cursor → adopters
```

**Framework vs project size:** KSS stays small and portable; project `.cursor/` holds the real governance content.

There is **no `KSS/.cursor/`**. Templates copy into the adopter project.

---

## Project Cursor OS (PCOS)

| Component | Typical path | Role |
|-----------|--------------|------|
| Memory | `.cursor/memory/` | Governance brain |
| Plans | `.cursor/plans/` | Temporary authority |
| Reports | `.cursor/reports/` | Evidence ledger |
| Rules | `.cursor/rules/` | Policy engine (project gates) |
| Goals | `.cursor/goals/` | Strategic intent |
| Commands | `~/.cursor/commands/` | User interface (Layer 2) |
| Skills | `~/.cursor/skills/` | Capability library (Layer 2) |
| Agents | `~/.cursor/agents/` | Specialized executors (Layer 2) |

See [docs/GOVERNANCE_LAYERS.md](docs/GOVERNANCE_LAYERS.md) for reading order and artifact taxonomy.

---

## Authority Model

```
DOC_POLICY > PROJECT_MANIFEST > DEC-* > INTEGRITY_RULES > STATE > HANDOFF > GOALS
```

| Layer | Authority | Content |
|-------|-----------|---------|
| DOC_POLICY | Paths only | Canonical path map |
| PROJECT_MANIFEST | Enforceable | Locks and constraints |
| DEC-* | Canonical | Agent decision digests (`.cursor/memory/decisions/`) |
| INTEGRITY_RULES | Verifiable | Rules 1–7, verification commands |
| STATE | Operational | Current execution position |
| HANDOFF | Session | Next actions |
| GOALS | Strategic | Long-term intent |

**DEC replaces live ADR folders** for agent execution. Human ADR trees under `docs/` are legacy; post-migration projects use DEC digests only.

Reports and archived plans are **historical** — not default execution authority.

---

## Templates Are Scaffolds, Not Policies

Templates contain placeholders. They do NOT enforce project-specific locks or product facts.

When you adopt KSS:
1. Copy templates to your project `.cursor/`
2. Replace placeholders
3. Add your locks, DEC digests, and verification commands

---

## Enforcement Lives in Project Memory

The framework does NOT enforce specific lock IDs, verification commands, or phase values.

Enforcement happens in the project's:
- `.cursor/memory/PROJECT_MANIFEST.md`
- `.cursor/memory/INTEGRITY_RULES.md`
- `.cursor/memory/decisions/DEC-*.md`
- `.cursor/memory/VERSIONS.md`
- `.cursor/memory/STATE.yaml`

---

## Separation of Concerns

| Layer | Contains | Must NOT Contain |
|-------|----------|------------------|
| Framework | Structure, templates, tooling | Real project data, locks, STATE |
| Project | Locks, DEC, STATE, reports | Framework templates verbatim |

Phase C human documentation (when enabled) is **never** execution SSOT.

---

## Why This Matters

With clear separation:
- Framework evolves independently (see `docs/KSS_EVOLUTION.md`)
- Projects maintain their own governance
- Any project can adopt KSS by copying and filling templates
- Framework updates use migration guides under `docs/migrations/`
