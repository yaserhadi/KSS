# Session End

**Purpose**: End session; persist handoff to `.cursor/memory/HANDOFF.md`.

**Related Commands**: `/boot`, `/gw-handoff`, `/docpack`

## Workflow

### 1. Read paths

`.cursor/DOC_POLICY.yaml` or defaults: `.cursor/memory/`, `.cursor/reports/`.

### 2. Generate handoff summary

Session summary, test status, files changed, decisions, open issues, next steps.

### 3. Update HANDOFF.md

Backup prior handoff to `.cursor/reports/HANDOFF_BACKUP_YYYY-MM.md`, then replace HANDOFF.md.

### 4. Optional stewards

- `/docpack` for full **governance packaging** (HANDOFF + STATE + conventions + reports — superset of this command)
- Architecture decisions → `/adr` (DEC digest in `.cursor/memory/decisions/`)

### 5. Confirm completion

Display session ended summary; next session run `/boot`.

## DEC closing check (advisory)

- Decisions this session: [list / none]
- DEC captured: [DEC-NNNN / None / Not yet]
- If decisions exist without DEC: recommend `/adr` or document "DEC Not Needed: reason"

Non-blocking.

## Gate awareness (advisory)

Read `.cursor/memory/STATE.yaml` when present:

- `runtime_install_gate`: if any flag false → do **not** recommend Copy-Item to `~/.cursor`
- `docpack_gate.extraction_close_allowed`: if false → governance closure still pending
- `kss_to_global_sync.performed`: if false → live `~/.cursor` may be stale vs remediation draft

Non-blocking.
