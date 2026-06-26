# Boot Commands — Canonical Paths (Single Memory)

**Purpose:** All `/boot`, `/docpack`, and `/session-end` commands must use these paths so only one memory location (`.cursor/memory/`) is used. Do not reference root `memory/`.

**Canonical policy file:** `.cursor/DOC_POLICY.yaml`
**Canonical memory directory:** `.cursor/memory/`

## Path substitutions

Apply these in your Cursor command files (copy to `~/.cursor/commands/`):

| Old | New |
|-----|-----|
| `memory/DOC_POLICY.yaml` | `.cursor/DOC_POLICY.yaml` |
| `memory/AI_ENTRY.md` | `.cursor/memory/AI_ENTRY.md` |
| `memory/STATE.yaml` | `.cursor/memory/STATE.yaml` |
| `memory/HANDOFF.md` | `.cursor/memory/HANDOFF.md` |
| `memory/LESSONS.md` | `.cursor/memory/LESSONS.md` |
| `memory/` (as directory) | `.cursor/memory/` |
| `.cursor/goals/GOALS.md` | `.cursor/memory/GOALS.md` |

## Files to update

- **boot** (e.g. `boot.md`): Check policy at `.cursor/DOC_POLICY.yaml`; read `.cursor/memory/AI_ENTRY.md` (mandatory order: DOC_POLICY, then PROJECT_MANIFEST, STATE, HANDOFF, GOALS), then `.cursor/memory/PROJECT_MANIFEST.md`, `.cursor/memory/STATE.yaml`, `.cursor/memory/HANDOFF.md`, and **`.cursor/memory/GOALS.md` (mandatory — Read with explicit path; if read fails, STOP boot)**. On-demand: `.cursor/memory/decisions/`, `.cursor/memory/INTEGRITY_RULES.md`. Do **not** use project `docs/` as execution SSOT.
- **docpack** (e.g. `docpack.md`): Governance packaging — read/write `.cursor/memory/`, `.cursor/reports/`, conventions per DOC_POLICY. Not project `docs/`.
- **session-end** (e.g. `session-end.md`): Write handoff to `.cursor/memory/HANDOFF.md` only.

## Verification

After updating commands, run `/session-end` and confirm that only `.cursor/memory/HANDOFF.md` is created or updated (not any file under root `memory/`).

**Single-memory-path fix:** Commands updated; content migrated to `.cursor/memory/`; root `memory/` deprecated (README only).
