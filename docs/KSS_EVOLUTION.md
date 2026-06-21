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
