# Integrity Rules (Non-negotiable)

<!-- Copy to: .cursor/memory/INTEGRITY_RULES.md -->
<!-- These are guardrails that cannot be bypassed. -->

1. No `ADR-*.md` under `.cursor/`. Use `memory/decisions/DEC-*.md` only.
2. No [TBD] in active memory files.
3. DOC_POLICY contains paths only.
4. Legacy human ADRs were in `docs/architecture/ADR/` (deleted when project migrates). DEC + MANIFEST are execution authority.
5. MANIFEST contains only constraints and locks, not discussions.
6. HANDOFF is replace-only and must be backed up before overwrite.

---

## DEC Governance (Agent-Facing)

1. `DEC-*.md` allowed only in `.cursor/memory/decisions/`.
2. No `ADR-*.md` under `.cursor/` (any path).
3. DEC and MANIFEST are authoritative for execution — not legacy project `docs/`.
4. DEC changes affecting locks require owner approval before MANIFEST/INTEGRITY sync.
5. Agents do not derive behavior from deleted project `docs/` trees.

---

## Periodic Drift Check (run manually)

```
rg -n '[TBD]' .cursor
rg -n 'ADR-' .cursor/memory/decisions
find .cursor -name 'ADR-*.md'
```

Any unexpected result requires review.

---

## Domain Locks Verification (project fills)

<!-- For each lock in PROJECT_MANIFEST, add verification commands:

### Lock: <LOCK_ID>

**Verification commands:**

```
rg "<forbidden_pattern>" <path>
```
Expected: empty (pattern must not exist)

```
rg "<required_pattern>" <path>
```
Expected: matches found (pattern must exist)

**Stop conditions:**
- If <condition> → STOP

-->

---

## Conventions Governance

Engineering conventions live in `.cursor/memory/conventions/`.

**Authority Rules:**

| Aspect | Rule |
|--------|------|
| Location | ONLY in `.cursor/memory/conventions/` |
| Status | Advisory by default |
| Override | Enforcement layers (MANIFEST, INTEGRITY_RULES, VERSIONS, STATE) always win |

**Promotion to Enforcement:**

If a convention contains enforceable constraints (MUST/forbidden/required), they must be:
1. Extracted as a Lock in PROJECT_MANIFEST, OR
2. Added as a gate in INTEGRITY_RULES, OR
3. Pinned in VERSIONS

Conventions alone do NOT grant enforcement authority.

---

## Plan Governance Validation

Before executing any plan in `.cursor/plans/`:

```
rg -l "## References" .cursor/plans/*.plan.md
rg "status:" .cursor/memory/STATE.yaml
```

**Validation Rules:**

| Check | Requirement |
|-------|-------------|
| References section | MUST exist in active plan |
| Lock reference | MUST point to valid MANIFEST lock |
| STATE.yaml status | If `manual-oversight` → owner approval required |
| STATE.yaml status | If `blocked` → do NOT execute |

If any check fails → STOP and request owner approval.

---

## ACTIVE.plan.md Consistency

ACTIVE.plan.md MUST always reflect current execution state.

**On Plan Completion (mandatory):**

When ALL todos in a plan are marked `completed`:
1. Update ACTIVE.plan.md "Current active plan" to next plan or "None"
2. Move completed plan to "Last completed plan" with status and date
3. Include evidence report link when applicable
