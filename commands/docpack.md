---
name: docpack
description: Update documentation after work is done. Detect → Classify → DEC check → Propose → Apply → Evidence.
---

# Docpack — Documentation Packaging

Capture session changes in `.cursor/memory/`, `.cursor/reports/`, and project conventions — **not** legacy `docs/` as execution SSOT.

## Workflow

```text
Detect → Classify Artifacts → DEC Completeness Check → Proposal → Apply → Evidence
```

### 1. Read paths

Read `.cursor/DOC_POLICY.yaml` for paths only.
Defaults: `.cursor/memory/`, `.cursor/reports/`, `.cursor/memory/decisions/`, `.cursor/plans/`.

### 2. Detect

- Files modified, decisions made, user impact, lessons learned.

**Gap check** (when relevant):

| Change type | Check |
|-------------|-------|
| DB / migrations | `.cursor/memory/conventions/database-conventions.md` |
| API / auth | `.cursor/memory/conventions/api-conventions.md` |
| RBAC | `.cursor/memory/conventions/rbac-conventions.md` |
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

Authoritative track (.cursor/memory/):
- [ ] HANDOFF.md
- [ ] STATE.yaml
- [ ] LESSONS.md
- [ ] VERSIONS.md (if breaking)

Conventions (.cursor/memory/conventions/):
- [ ] [convention files as needed]

Architecture:
- [ ] DEC via `/adr` → `.cursor/memory/decisions/DEC-NNNN-slug.md`

Evidentiary (.cursor/reports/):
- [ ] Closure / evidence report if phase complete

Phase C (human docs — optional, never execution SSOT):
- [ ] User Documentation Steward only if Phase C gate active per DOC_POLICY

Confirm? [Y/n]
```

### 4. Apply

After confirmation:
- **AI Knowledge Steward** → `.cursor/memory/` updates
- **User Documentation Steward** → Phase C human docs only (if gate enabled)
- Summary of updates + evidence paths

## Layered sources

| Class | Examples |
|-------|----------|
| Authoritative | PROJECT_MANIFEST, DEC-*, INTEGRITY_RULES |
| Operational | STATE.yaml, HANDOFF.md, ACTIVE.plan.md |
| Evidentiary | `.cursor/reports/*` |
| Informational | README, GOALS, ROADMAP |
| Phase C | Human docs — optional |

## Enforcement defaults

| Target | When |
|--------|------|
| HANDOFF | Required |
| STATE | When meaningful |
| LESSONS | Encouraged |
| DEC via `/adr` | Major architecture decisions |
| Phase C docs | User impact + gate enabled |

## Safe defaults (auto-apply)

- HANDOFF.md update
- STATE.yaml `last_updated`

## Require confirmation

- LESSONS.md, conventions, VERSIONS.md
- DEC file creation
- Phase C user documentation

## Integration

- **AI Knowledge Steward** — `.cursor/memory/`
- **User Documentation Steward** — Phase C only

Do **not** write to `docs/architecture/ADR/` or treat `docs/` as execution authority.
