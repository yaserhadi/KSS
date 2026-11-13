# Project Manifest

<!-- Copy to: .cursor/memory/PROJECT_MANIFEST.md -->
<!-- This is the single authoritative source for architectural constraints. -->

## Project Identity

| Aspect | Value |
|--------|-------|
| Project | <project_name> |
| Vision | <vision_statement> |
| Phase | <current_phase> |

## Audience

<!-- Define who reads this document -->
- AI agents (primary)
- Human developers (reference)
- Project stakeholders (overview)

## Document Role

This document is the single authoritative source for architectural constraints.
No architectural baseline exists outside PROJECT_MANIFEST.

## Authority Model

DOC_POLICY → PROJECT_MANIFEST → DEC → INTEGRITY_RULES → STATE → HANDOFF → GOALS

Reading order only; it does not establish precedence when sources conflict. Conflicts between MANIFEST, DEC and INTEGRITY_RULES are reported to the Owner. DEC digests live in `.cursor/memory/decisions/`; legacy ADRs are historical provenance only.

## Architectural Locks (project fills)

<!-- Template for a lock:

### Lock: <LOCK_ID> (Applies: <phase>+)

**Non-negotiables:**
- <constraint_1>
- <constraint_2>

**Allowed patterns:**
- <pattern>

**Forbidden patterns:**
- <pattern>

**Stop conditions:**
- <condition> → STOP and ask owner

-->

<!-- Add your locks below -->
