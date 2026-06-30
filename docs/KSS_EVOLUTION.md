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
commit: fb712b9
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
  local_only: true
  push_deferred: true
  tag_type: annotated
evidence: Jabal/.cursor/reports/BK_029_DEC_EXECUTION_AUTHORITY_CLOSURE.md
```
