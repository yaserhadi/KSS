# KSS_Doctrine_003 — Portable Structure vs Project Content

**Status:** Framework doctrine (generalization from Jabal CAB Fix Wave)  
**Date:** 2026-06-21  
**Cross-links:** [KSS_Doctrine_001](KSS_EVOLUTION.md#evol-005--extraction-direction-kss_doctrine_001), [KSS_Doctrine_002](KSS_Doctrine_002.md)

```yaml
KSS_Doctrine_003:
  name: Portable Structure vs Project Content
  classification: governance_rule
  severity: high
  rule: >
    Export governance structures and decision patterns only.
    Never export project-specific values, modules, names, workflows,
    or business assumptions.
  portable_structure: true
  project_content: replace_required
  violations_block:
    - copying project DEC paths verbatim into KSS templates
    - copying project module names or business examples as defaults
    - treating Jabal L3 content as portable without generalization
```

## Plain language

**Pattern ≠ project content**

| Export | Keep in project |
|--------|-----------------|
| Gate logic, decision tables, triage flow | DEC refs, module paths, conventions |
| Reading-chain structure | Project-specific lock IDs |
| Doctrine enforcement checks | Tenant/product examples |

## Template adoption

After copying from `templates/project-rules/`:

1. Replace `<LOCK_ID>`, DEC paths, and module/kernel naming with project equivalents
2. Do **not** assume Laravel modules, Jabal paths, or reference-project workflows
3. Run `/gw-triage` if destination is unclear

## Jabal lesson

Jabal's `module-creation-gate` taught that exporting **structure** to KSS while keeping **content** in the project prevents blind replication across repos.
