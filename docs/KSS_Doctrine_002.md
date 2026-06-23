# KSS_Doctrine_002 — Layer Responsibility Principle

**Status:** Framework doctrine (generalization from Jabal)  
**Date:** 2026-06-21  
**Cross-link:** KSS_Doctrine_001 (Extraction Direction)

```yaml
KSS_Doctrine_002:
  name: Layer Responsibility Principle
  classification: governance_rule
  severity: high
  rules:
    - Layer1_governs
    - Layer3_teaches
    - Layer2_distributes
    - Layer2_never_defines_authority
    - Layer3_never_overrides_Layer1
  violations_block:
    - blind KSS → ~/.cursor sync
    - L2 writes authority
    - L3 overrides L1
    - reports override DEC
    - docs treated as execution SSOT
```

**Authority model:** This document is the **governing rule**. [`LAYER_VIOLATION_CHECKLIST.md`](LAYER_VIOLATION_CHECKLIST.md) is the implementation aid for gw-* commands — not a substitute for this doctrine.

## Layers

| Layer | Location | Role |
|-------|----------|------|
| **L1 Authority** | `.cursor/memory/` (MANIFEST, DEC, INTEGRITY) | Governs — highest agent authority |
| **L2 Runtime** | `~/.cursor/` from KSS | Distributes portable patterns |
| **L3 Project SSOT** | `Project/.cursor/` | Teaches reference implementation |

## Plain language

**Layer 1 governs · Layer 3 teaches · Layer 2 distributes**

Layer 2 must not define authoritative locks. Layer 3 must not override Layer 1.

## Export targets (when KSS repo available)

- `KSS/philosophy.md`
- `KSS/docs/GOVERNANCE_LAYERS.md`
- `KSS/docs/handbook.md`
- `KSS/docs/CURSOR_ARCHITECTURE.md`

## Jabal attestation

- [`.cursor/CURSOR_RUNTIME.md`](../../CURSOR_RUNTIME.md)
- [`.cursor/reports/JABAL_GOVERNANCE_LESSONS.md`](../JABAL_GOVERNANCE_LESSONS.md)
