# Layer Violation Checklist

**Status:** Implementation aid — **not** standalone authority.  
**Governing rule:** [`KSS_Doctrine_002.md`](KSS_Doctrine_002.md) (`classification: governance_rule`)

Use this checklist in `/gw-triage`, `/gw-riskcheck`, and `/gw-review` to satisfy Doctrine_002. Violations map to `violations_block` in Doctrine_002.

| Check | Fail signal | Doctrine violation |
|-------|-------------|-------------------|
| L2 defines authority | `~/.cursor` command/rule writes locks, MANIFEST, or gate authority | L2 writes authority |
| L3 overrides L1 | Project rule/plan contradicts MANIFEST/DEC/INTEGRITY | L3 overrides L1 |
| `docs/` as execution SSOT | Propose/Apply targets `docs/architecture/ADR/`, `docs/reference/`, or treats `docs/` as authoritative | docs treated as execution SSOT |
| Reports over DEC | Evidentiary report used where DEC required | reports override DEC |
| Ungated runtime install | Suggests `Copy-Item` KSS → `~/.cursor` without `STATE.runtime_install_gate` all true | blind KSS → ~/.cursor sync |

## Output (gw-* commands)

For each check: **Pass / Fail / N/A** with evidence path. Any **Fail** on governance-impacting work → minimum **High** severity in riskcheck; **Critical** in review (block merge).
