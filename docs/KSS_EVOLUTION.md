# KSS Evolution Log

Index of lessons generalized from adopter projects into KSS. For **upgrade steps**, see [migrations/](migrations/).

---

## EVOL-001 — Post-docs SSOT

```yaml
id: EVOL-001
lesson: ".cursor is sole execution authority; docs/ not agent SSOT"
origin: Jabal docs_audit_migration Phase B
generalized_to:
  - philosophy.md
  - docs/GOVERNANCE_LAYERS.md
  - templates/DOC_POLICY.template.yaml
  - templates/INTEGRITY_RULES.template.md (Rules 1-7)
kss_files_changed: [philosophy.md, GOVERNANCE_LAYERS.md, DOC_POLICY.template.yaml, agents/*, commands/docpack.md, commands/adr.md]
breaking_change: true
migration_required: migrations/MIGRATION_v1.0.0_to_v1.1.0.md
kss_version: v1.1.0
```

---

## EVOL-002 — Three-layer Cursor architecture

```yaml
id: EVOL-002
lesson: "KSS framework / ~/.cursor runtime / project .cursor SSOT"
origin: Jabal KSS governance extraction
generalized_to:
  - philosophy.md
  - docs/handbook.md
  - INSTALL.md
kss_files_changed: [philosophy.md, handbook.md, INSTALL.md]
breaking_change: false
migration_required: Re-read INSTALL; no folder moves
kss_version: v1.1.0
```

---

## EVOL-003 — Project Cursor OS + artifact taxonomy

```yaml
id: EVOL-003
lesson: "memory/plans/reports/rules/goals + Layer 2 runtime; artifact classes"
origin: Jabal .cursor inventory (~235 files)
generalized_to:
  - docs/GOVERNANCE_LAYERS.md
  - templates/project-rules/README.template.md
kss_files_changed: [GOVERNANCE_LAYERS.md, COMMANDS_EXPLANATION.md, RULES_EXPLANATION.md]
breaking_change: false
kss_version: v1.1.0
```

---

## EVOL-004 — DEC replaces live ADR folder for agents

```yaml
id: EVOL-004
lesson: "Stewards and /adr write DEC digests to memory/decisions/"
origin: Jabal docs migration + KSS v1.0.0 stale paths
generalized_to:
  - agents/adr-steward.md
  - agents/ai-knowledge-steward.md
  - commands/adr.md
breaking_change: true
migration_required: migrations/MIGRATION_v1.0.0_to_v1.1.0.md
kss_version: v1.1.0
```

---

## EVOL-005 — Extraction Direction (KSS_Doctrine_001)

```yaml
id: EVOL-005
doctrine_id: KSS_Doctrine_001
name: Extraction Direction
lesson: "Jabal → Review → Generalize → KSS → Optional Runtime Install; never KSS → ~/.cursor before review"
origin: Jabal KSS extraction correction 2026-06-21
rule: Jabal .cursor (reference) → classify → generalize → KSS draft → owner-approved runtime install last
forbids: Copy-Item KSS → ~/.cursor without runtime_install_gate all true
generalized_to:
  - philosophy.md
  - docs/GOVERNANCE_LAYERS.md
  - docs/handbook.md
  - commands/docpack.md
layer_dependency: "Layer 1 governs · Layer 3 teaches · Layer 2 distributes"
breaking_change: false
kss_version: v1.1.0-draft
evidence: Jabal/.cursor/reports/KSS_EXTRACTION_CORRECTION_AUDIT.md
```

---

## EVOL-006 — Governance Lab sync (patch)

```yaml
id: EVOL-006
version: v1.1.2
commit: efdf993
source: Governance Lab (~/.cursor) after Jabal dist-kss-sync
lesson: "Validated live runtime lessons sync back to KSS as patch releases; conflict-governance always applies"
changes:
  - rules/conflict-governance.mdc (new)
  - INSTALL.md, rules/RULES_EXPLANATION.md
  - commands/docpack.md, gw-review.md, gw-riskcheck.md, gw-triage.md
  - docs/KSS_Doctrine_002.md
  - templates/project-rules/*.template.mdc (5)
breaking_change: false
semver: patch
prior_release: v1.1.1@9d65027
publication:
  local_only: true
  push_deferred: true
  tag_type: annotated
evidence: Jabal/.cursor/reports/KSS_SYNC_FROM_JABAL.md
```

---

## EVOL-007 — DEC execution authority + docpack/doplan sync (patch)

```yaml
id: EVOL-007
version: v1.1.3
commit: 4d38f50
source: Jabal BK-029 + docpack SSOT alignment + /doplan gateway
lesson: "/dec is primary decision command; ADR retired from execution model (deprecated alias only); docpack and doplan aligned to .cursor SSOT"
changes:
  - commands/dec.md (new)
  - agents/dec-steward.md (new)
  - commands/adr.md, agents/adr-steward.md (deprecated aliases)
  - commands/docpack.md, doplan.md (SSOT alignment)
  - commands/boot.md, session-end.md, gw-* (→ /dec references)
  - docs/GOVERNANCE_LAYERS.md, handbook.md, reference docs
breaking_change: false
semver: patch
prior_release: v1.1.2@efdf993
publication:
  local_only: false
  push_deferred: false
  tag_type: annotated
evidence: Jabal/.cursor/reports/BK_029_DEC_EXECUTION_AUTHORITY_CLOSURE.md
```

---

## EVOL-008 — session-end embeds conditional docpack check (patch)

```yaml
id: EVOL-008
version: v1.1.4
commit: bd1b9a8
source: Jabal BK-042 session workflow improvement
lesson: "session-end should embed a conditional docpack trigger so agents never forget governance packaging when conventions change; docpack remains standalone for mid-session use"
changes:
  - commands/session-end.md (step 4 → conditional docpack check replaces optional steward mention)
  - commands/docpack.md (related commands table updated)
breaking_change: false
semver: patch
prior_release: v1.1.3@3b06515
publication:
  local_only: false
  push_deferred: false
  tag_type: annotated
evidence: N/A (workflow improvement; no project closure)
```

---

## Release v1.1.5 (2026-07-15)

Annotated tag on `main` covering **EVOL-009 … EVOL-012** (tenant storage gate templates + session closeout command hierarchy). Prior release: `v1.1.4`. Owner opened remote publication gate (Jabal Phase 15C deferred push).

---

## EVOL-010 — session-end/docpack lifecycle packaging (patch)

```yaml
id: EVOL-010
version: v1.1.5
source: Jabal Governance Lab — BK-064 post-merge session-end gap
lesson: >
  HANDOFF alone is not session closure. /session-end must run docpack lifecycle packaging when
  a BK/defect/delivery merges or Decisions complete; do not skip for "product-only" or
  "governance-only" sessions. Docpack must auto-apply STATE/ACTIVE/BACKLOG/closure-report
  consistency (strip false pending-merge wording) in session-end embedded mode.
changes:
  - commands/session-end.md (required lifecycle matrix; anti-skip rules; embedded docpack)
  - commands/docpack.md (Detect git tip vs SSOT; lifecycle safe defaults; embedded mode)
  - synced to ~/.cursor/commands (Governance Lab runtime)
breaking_change: false
semver: patch
prior_lesson: EVOL-008
prior_release: v1.1.4
publication:
  local_only: false
  push_deferred: false
  tag_type: annotated
evidence: Jabal session after BK-064 Decision A merge — closure SSOT/BACKLOG/ACTIVE left pending-merge
```

---

## EVOL-011 — git-finalize requires post-merge lifecycle packaging (patch)

```yaml
id: EVOL-011
version: v1.1.5
source: Jabal Governance Lab — same BK-064 finalize gap
lesson: >
  /git-finalize must not stop at merge+branch-delete. Step 5 requires /docpack lifecycle
  packaging so HANDOFF/STATE/ACTIVE/BACKLOG/closure SSOT match main HEAD. Soft Skip for
  "conventions only" caused pending-merge drift after Decision A.
changes:
  - commands/git-finalize.md (step 5 post-merge packaging required; report artefacts)
  - synced to ~/.cursor/commands
breaking_change: false
semver: patch
prior_lesson: EVOL-010
prior_release: v1.1.4
publication:
  local_only: false
  push_deferred: false
  tag_type: annotated
evidence: Jabal retrospectively packed BK-064 after merge b15443d
```

---

## EVOL-012 — session-end is authoritative closeout (finalize + docpack + push) (patch)

```yaml
id: EVOL-012
version: v1.1.5
source: Jabal Governance Lab — owner clarification on command hierarchy
lesson: >
  /git-finalize is local merge + merge packaging only; it does not close the session.
  /session-end is the authoritative closeout: verify finalize done, verify/repair docpack,
  and push (or record owner waiver). Standalone /docpack after finalize is usually redundant;
  prefer session-end.
changes:
  - commands/session-end.md (closeout audit: git/finalize, docpack, push)
  - commands/git-finalize.md (explicit handoff to /session-end; no session-closed claim)
  - commands/docpack.md (finalize vs session-end embedded modes; prefer session-end after finalize)
  - synced to ~/.cursor/commands
breaking_change: false
semver: patch
prior_lesson: EVOL-011
prior_release: v1.1.4
publication:
  local_only: false
  push_deferred: false
  tag_type: annotated
evidence: owner feedback — still need docpack/session-end after finalize including push
```

---

## EVOL-009 — Tenant storage mode compatibility gate (patch)

```yaml
id: EVOL-009
version: v1.1.5
source: Jabal BK-052 governance extraction (tenant-storage-mode-gate)
lesson: "Multi-tenant projects need trigger-based PRV for shared_db / database_per_tenant / schema_per_tenant; two-tier auth vs domain model; INTEGRITY verification + preflight cross-ref"
changes:
  - templates/project-rules/tenant-storage-mode-gate.template.mdc (new)
  - templates/project-rules/plan-preflight-gate.template.mdc (storage gate cross-ref)
  - templates/project-rules/README.template.md
  - templates/INTEGRITY_RULES.template.md (TENANT-STORAGE-MODE-COMPATIBILITY section)
breaking_change: false
semver: patch
prior_release: v1.1.4
publication:
  local_only: false
  push_deferred: false
  tag_type: annotated
evidence: Jabal/.cursor/reports/BK_052_TENANT_STORAGE_COMPATIBILITY.md
```

---

## EVOL-013 — GW command family reduced to /gw-review

```yaml
id: EVOL-013
version: unreleased
source: Owner decision 2026-11-10 — remove overlapping command responsibilities
lesson: >
  Four GW commands overlapped with /doplan and /session-end. Pre-implementation triage and
  risk assessment belong in /doplan (proportional, not an approval step); /gw-review stays as
  the single review of the actual change set; in-session summaries are normal agent output and
  /session-end persists HANDOFF. Findings, product decisions and implementation stay separate:
  a finding is not an approved backlog item, and backlog admission is not execution authorization.
  /git-finalize runs the /gw-review protocol before merge; Approve continues, other outcomes stop.
changes:
  - commands/gw-triage.md, gw-riskcheck.md, gw-handoff.md, global-workflow.md (removed)
  - commands/git-finalize.md (pre-merge review step before merge; SHA-based reuse; Approve continues, Request Changes / Block / incomplete stop)
  - commands/doplan.md (Phase 3.5 scope, destination and risk; finding vs admitted item)
  - commands/gw-review.md (actual-diff scope, verdict definitions, finding dispositions, portable conventions)
  - docs/reference/command-workflow.md (renamed from gw-workflow-commands.md; lifecycle, Findings to Backlog)
  - README, INSTALL, handbook, COMMANDS_EXPLANATION, GOVERNANCE_LAYERS, LAYER_VIOLATION_CHECKLIST, Doctrine_002/003, related-command lines, two project-rule templates (reference alignment)
breaking_change: true
semver: none (unreleased)
prior_lesson: EVOL-012
publication:
  tag: none
  runtime_install: separate Owner decision
evidence: KSS branch chore/simplify-gw-commands
```
