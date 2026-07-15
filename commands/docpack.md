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
| `/session-end` | **Authoritative session close** — verify finalize + verify/repair docpack + **push** |
| `/git-finalize` | Local merge + merge-moment packaging; still requires `/session-end` afterward |
| `/docpack` | Packaging tool (standalone mid-session, or embedded by finalize / session-end) |
| `/gw-handoff` | In-chat summary only — does not persist |
| `/dec` | DEC digest → `.cursor/memory/decisions/` |
| `/adr` | Deprecated alias → `/dec` |

## Modes

| Mode | When | Confirmation |
|------|------|--------------|
| **Standalone** | Owner runs `/docpack` mid-session | Propose checklist; apply after confirm for gated items; safe defaults may auto-apply |
| **Finalize embedded** | `/git-finalize` step 5 | Auto-apply merge-tip lifecycle packaging |
| **Session-end embedded** | `/session-end` closeout | **Verify** finalize packaging; **repair** if drifted; do not leave “run docpack later” |

After `/git-finalize`, prefer **`/session-end`** over a redundant standalone `/docpack` — session-end verifies packaging and publishes.

Do **not** exit `/session-end` with HANDOFF updated while leaving closure SSOT / BACKLOG / ACTIVE / STATE contradicting the session outcome, or while `main` is ahead of origin without push or owner waiver.

## Workflow

```text
Detect → Classify Artifacts → DEC Completeness Check → Proposal (if needed) → Apply → Evidence
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

- Files modified, decisions made, lessons learned, phase/BK/CAP closure.
- **Git tip vs governance SSOT** (required when delivery/merge/finalize happened this session):
  - Closure / UAT / evidence reports under `{reports}/`
  - `{roadmap}/BACKLOG.md` lifecycle row for the active BK
  - `{plans}/ACTIVE.plan.md` last-completed / notes
  - `{memory}/STATE.yaml` focus (`branch`, `head`, `next_action`, BK status fields)
- Stale phrase scan (must repair when false after merge/finalize):
  - `pending merge`, `await merge`, `awaiting merge`
  - `one functional defect`, `Decision A` / Decision still **open** when it completed
  - feature-tip-only attribution when work is already on `main`

**Gap check** (when relevant):

| Change type | Check |
|-------------|-------|
| DB / migrations | `{conventions}/database-conventions.md` |
| API / auth | `{conventions}/api-conventions.md` |
| RBAC | `{conventions}/rbac-conventions.md` |
| Tests | `tests/README.md` |
| Delivery / merge / UAT close | Closure report + runbook Result Record + BACKLOG + ACTIVE.plan + STATE |

### 2.5 DEC Completeness Check (Advisory)

Triggers: module boundaries, API contracts, tenancy/schema, security/RBAC, decision language in commits.

If triggers and no DEC in session → **DEC Missing Warning** (advisory, non-blocking). Recommend `/dec`.

### DEC_POLICY

| Trigger class | Policy |
|---------------|--------|
| Module boundary, tenancy/schema, security/RBAC, API contract | **Mandatory recommend** `/dec` |
| Minor ops, typo, test-only | **Advisory skip** with documented reason |
| Defect fix under existing Active DEC / Lock | **DEC Not Needed** (document reason) |
| Ambiguous | **Advisory warning** in proposal |

### 3. Propose

```
DOCPACK PROPOSAL

**DEC Completeness Check**: [OK / Warning]
**Mode**: standalone | session-end embedded

Governance track (.cursor/memory/):
- [ ] HANDOFF.md
- [ ] STATE.yaml
- [ ] LESSONS.md
- [ ] VERSIONS.md (if breaking)

Operational lifecycle:
- [ ] ACTIVE.plan.md (status / last completed / notes match tip)
- [ ] BACKLOG.md (BK lifecycle row matches delivery state)
- [ ] Closure / UAT / evidence report(s) (no stale pending-merge wording)
- [ ] Runbook Result Record (when a runbook exists for the delivery)

Conventions (.cursor/memory/conventions/):
- [ ] [convention files as needed]

Architecture:
- [ ] DEC via `/dec` → `.cursor/memory/decisions/DEC-NNNN-slug.md`

Evidentiary (.cursor/reports/):
- [ ] Closure / evidence report if phase or BK/CAP complete

Human-facing docs:
- [ ] N/A — skipped (no human_docs in DOC_POLICY)
  OR (only when human_docs path is defined and owner-enabled):
- [ ] User Documentation Steward → {human_docs path}

Confirm? [Y/n]   # omit for session-end embedded safe+lifecycle auto-apply
```

When `human_docs` is **not** in DOC_POLICY, show **only** the `N/A — skipped` line under Human-facing docs. Do not imply Phase C or project `docs/` is available.

In **session-end embedded** mode: do not block the session close on Confirm for safe defaults + lifecycle packaging. Still ask Confirm before LESSONS / conventions / VERSIONS / new DEC / human_docs.

### 4. Apply

- **AI Knowledge Steward** → `.cursor/memory/`, `.cursor/reports/` (evidence), scoped plan updates, roadmap lifecycle rows when delivery closed
- **User Documentation Steward** → only when `human_docs` path exists in DOC_POLICY and owner confirmed in proposal
- Summary of updates + evidence paths

Preserve intentional deletions (e.g. consolidated detail reports marked deleted) — do **not** recreate them.

## Layered sources

| Class | Examples |
|-------|----------|
| Authoritative | PROJECT_MANIFEST, DEC-*, INTEGRITY_RULES |
| Operational | STATE.yaml, HANDOFF.md, ACTIVE.plan.md, BACKLOG.md |
| Evidentiary | `.cursor/reports/*` |
| Informational | README, `memory/GOALS.md` |
| Human-facing (optional) | DOC_POLICY `human_docs` only — never execution SSOT |

## Enforcement defaults

| Target | When |
|--------|------|
| HANDOFF | Required (also via `/session-end`) |
| STATE | When meaningful tip/status/next_action changed |
| ACTIVE.plan | When plan completed, merge landed, or notes contradict tip |
| BACKLOG | When BK/CAP lifecycle status changed (delivered / partial / blocked note) |
| Closure / evidence reports | When delivery/merge/UAT/Decision closed this session |
| Runbook Result Record | When a runbook was used and final verdict changed |
| LESSONS | Encouraged |
| DEC via `/dec` | Major architecture decisions |
| Human-facing docs | Only when `human_docs` path defined in DOC_POLICY |

## Safe defaults (auto-apply)

- HANDOFF.md update
- STATE.yaml focus / `last_updated` (or project-equivalent fields)
- When delivery/merge/finalize/Decision-complete triggers match:
  - ACTIVE.plan.md notes / last-completed alignment
  - BACKLOG.md lifecycle row for the affected BK (status text; do **not** mint BK-IDs)
  - Closure / UAT SSOT + runbook Result Record consistency with **merge commit / main HEAD**
  - Strip false `pending merge` / open-Decision wording after merge already happened

## Require confirmation

- LESSONS.md, conventions, VERSIONS.md
- DEC file creation
- Human-facing documentation (only when `human_docs` active in DOC_POLICY)
- BACKLOG **mint** of new BK-IDs (owner-only) — packaging may update an existing row’s status text only

## Integration

- **AI Knowledge Steward** — `.cursor/memory/`, `.cursor/reports/`, plans, roadmap status text
- **User Documentation Steward** — `human_docs` path only when defined in DOC_POLICY

## Prohibitions

- Do **not** write to project `docs/` or `docs/architecture/ADR/` as execution authority
- Do **not** treat project `docs/` as agent SSOT
- Do **not** conflate KSS framework docs (`KSS/docs/`, `~/.cursor/docs/`) with project execution paths
- Do **not** skip lifecycle packaging because “HANDOFF already mentions it”
- Do **not** start unrelated next-BK planning from docpack (lifecycle record only)
