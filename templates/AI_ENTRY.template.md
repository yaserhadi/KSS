# AI Entry Point

<!-- Copy to: .cursor/memory/AI_ENTRY.md -->
<!-- This is the first file agents read. It defines reading order and authority. -->

## Mandatory Reading Order

When starting a session, read these files in order:

1. `.cursor/DOC_POLICY.yaml` — Canonical paths only
2. `.cursor/memory/AI_ENTRY.md` — This file (entry gate)
3. `.cursor/memory/PROJECT_MANIFEST.md` — Architectural locks
4. `.cursor/memory/STATE.yaml` — Current execution status
5. `.cursor/memory/HANDOFF.md` — Session continuity
6. `.cursor/memory/GOALS.md` — Strategic objectives (mandatory; `/boot` stops if unreadable)

`.cursor/plans/ACTIVE.plan.md` is read only if present. INTEGRITY_RULES, VERSIONS and DEC digests are read on demand and before execution.

## Authority Model

```
DOC_POLICY → PROJECT_MANIFEST → DEC → INTEGRITY_RULES → STATE → HANDOFF → GOALS
```

This is a reading order only; it does not establish precedence when sources conflict. If MANIFEST, DEC and INTEGRITY_RULES disagree, stop and report the conflict for an Owner decision.

- DOC_POLICY contains paths only (no governance logic)
- PROJECT_MANIFEST contains architectural locks
- DEC digests in `.cursor/memory/decisions/` are canonical agent-facing decision records (when the project keeps a decision registry)
- INTEGRITY_RULES contains verifiable guardrails and verification commands
- Decision-registry discovery for planning is via `/preplan` (Active-only candidates). `AI_ENTRY` may name the registry path/convention; it must not re-implement discovery
- Whether Governance Applicability is mandatory is **project law**, not implied by KSS alone
- Legacy project `docs/` is not execution authority
- STATE controls execution status
- HANDOFF provides session continuity
- GOALS provide strategic direction

## Execution Gate

Before executing any task:
1. Check STATE.yaml status
2. If `manual-oversight` → require explicit owner approval
3. If `blocked` → do NOT execute
4. If `active` → proceed with caution

## On-Demand Sources (project fills)

<!-- Add project-specific reading hints here, e.g.:
- "When working on <feature>, read the <LOCK_ID> section in PROJECT_MANIFEST"
-->

## Rules

1. Do NOT create new memory locations or shadow folders (except those declared in DOC_POLICY)
2. Do NOT modify enforcement layers without explicit approval
3. Do NOT derive behavior from deleted project `docs/` or legacy ADR trees unless explicitly requested
4. ALWAYS check STATE before execution
5. ALWAYS update HANDOFF at session end
