---
name: docpack
description: Package session outcomes into .cursor/ governance artefacts. Detect → Classify → DEC check → Propose → Apply → Evidence.
---

# Docpack — Governance Packaging

Package session outcomes into **`.cursor/` governance artefacts** — memory, conventions, reports, and closure evidence.

**Not** project human documentation. **Not** legacy `docs/` as execution SSOT. **Not** KSS framework reference docs (`KSS/docs/`, `~/.cursor/docs/`).

Project execution SSOT = `.cursor/` per adopting project's `DOC_POLICY.yaml`.

## Related commands

| Command | Role |
|---------|------|
| `/session-end` | HANDOFF.md only (lighter close) |
| `/docpack` | Superset: HANDOFF + STATE + conventions + reports + optional DEC |
| `/gw-handoff` | In-chat summary only — does not persist |
| `/adr` | DEC digest → `.cursor/memory/decisions/` |

## Workflow

```text
Detect → Classify Artifacts → DEC Completeness Check → Proposal → Apply → Evidence
```

### 1. Read paths

Read `.cursor/DOC_POLICY.yaml` for paths only (no governance logic in that file).

**Standard keys** (use DOC_POLICY value or default):

| Key | Default |
|-----|---------|
| `memory` | `.cursor/memory` |
| `reports` | `.cursor/reports` |
| `conventions` | `.cursor/memory/conventions` |
| `decisions` | `.cursor/memory/decisions` |
| `roadmap` | `.cursor/memory/roadmap` |
| `plans` | `.cursor/plans` |

**Human-facing docs (optional):** only if `DOC_POLICY.yaml` defines an active `human_docs` path (uncommented, non-empty). If absent → **do not list an open write track**; report `N/A — skipped (no human_docs in DOC_POLICY)`.

Do **not** assume project `docs/` exists. Do **not** create or resurrect project `docs/` as SSOT.

### 2. Detect

- Files modified, decisions made, lessons learned, phase/CAP closure.

**Gap check** (when relevant):

| Change type | Check |
|-------------|-------|
| DB / migrations | `{conventions}/database-conventions.md` |
| API / auth | `{conventions}/api-conventions.md` |
| RBAC | `{conventions}/rbac-conventions.md` |
| Tests | `tests/README.md` |

### 2.5 DEC Completeness Check (Advisory)

Triggers: module boundaries, API contracts, tenancy/schema, security/RBAC, decision language in commits.

If triggers and no DEC in session → **DEC Missing Warning** (advisory, non-blocking). Recommend `/adr` (DEC digest output).

### DEC_POLICY

| Trigger class | Policy |
|---------------|--------|
| Module boundary, tenancy/schema, security/RBAC, API contract | **Mandatory recommend** `/adr` (DEC digest) |
| Minor ops, typo, test-only | **Advisory skip** with documented reason |
| Ambiguous | **Advisory warning** in proposal |

### 3. Propose

```
DOCPACK PROPOSAL

**DEC Completeness Check**: [OK / Warning]

Governance track (.cursor/memory/):
- [ ] HANDOFF.md
- [ ] STATE.yaml
- [ ] LESSONS.md
- [ ] VERSIONS.md (if breaking)

Conventions (.cursor/memory/conventions/):
- [ ] [convention files as needed]

Architecture:
- [ ] DEC via `/adr` → `.cursor/memory/decisions/DEC-NNNN-slug.md`

Evidentiary (.cursor/reports/):
- [ ] Closure / evidence report if phase or CAP complete

Human-facing docs:
- [ ] N/A — skipped (no human_docs in DOC_POLICY)
  OR (only when human_docs path is defined and owner-enabled):
- [ ] User Documentation Steward → {human_docs path}

Confirm? [Y/n]
```

When `human_docs` is **not** in DOC_POLICY, show **only** the `N/A — skipped` line under Human-facing docs. Do not imply Phase C or project `docs/` is available.

### 4. Apply

After confirmation:

- **AI Knowledge Steward** → `.cursor/memory/`, `.cursor/reports/` (evidence), scoped plan updates
- **User Documentation Steward** → only when `human_docs` path exists in DOC_POLICY and owner confirmed in proposal
- Summary of updates + evidence paths

## Layered sources

| Class | Examples |
|-------|----------|
| Authoritative | PROJECT_MANIFEST, DEC-*, INTEGRITY_RULES |
| Operational | STATE.yaml, HANDOFF.md, ACTIVE.plan.md |
| Evidentiary | `.cursor/reports/*` |
| Informational | README, `memory/GOALS.md`, `memory/roadmap/BACKLOG.md` |
| Human-facing (optional) | DOC_POLICY `human_docs` only — never execution SSOT |

## Enforcement defaults

| Target | When |
|--------|------|
| HANDOFF | Required |
| STATE | When meaningful |
| LESSONS | Encouraged |
| DEC via `/adr` | Major architecture decisions |
| Human-facing docs | Only when `human_docs` path defined in DOC_POLICY |

## Safe defaults (auto-apply)

- HANDOFF.md update
- STATE.yaml `last_updated` (or project-equivalent field)

## Require confirmation

- LESSONS.md, conventions, VERSIONS.md
- DEC file creation
- Human-facing documentation (only when `human_docs` active in DOC_POLICY)

## Integration

- **AI Knowledge Steward** — `.cursor/memory/`, `.cursor/reports/`
- **User Documentation Steward** — `human_docs` path only when defined in DOC_POLICY

## Prohibitions

- Do **not** write to project `docs/` or `docs/architecture/ADR/` as execution authority
- Do **not** treat project `docs/` as agent SSOT
- Do **not** conflate KSS framework docs (`KSS/docs/`, `~/.cursor/docs/`) with project execution paths
