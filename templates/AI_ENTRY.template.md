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
6. `.cursor/memory/GOALS.md` — Strategic objectives (mandatory when present)

## Authority Model

```
DOC_POLICY > PROJECT_MANIFEST > INTEGRITY_RULES > DEC > STATE > HANDOFF > GOALS
```

- DOC_POLICY contains paths only (no governance logic)
- PROJECT_MANIFEST contains architectural locks
- INTEGRITY_RULES contains verification gates
- DEC digests in `.cursor/memory/decisions/` are agent-facing decision records
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
