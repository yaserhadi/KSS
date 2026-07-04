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

### 4. Docpack conditional check

Evaluate whether `/docpack` is needed based on session changes:

| Session changed... | Action |
|--------------------|--------|
| Conventions, patterns, or architecture rules | Run `/docpack` |
| Architectural decisions (DEC-worthy) | Run `/docpack` (includes DEC check) |
| Only product code following existing patterns | Skip — note "No convention changes" |
| Only governance files (BACKLOG, ACTIVE.plan) | Skip — already covered by HANDOFF |

**If docpack triggers:** run the full `/docpack` workflow (Detect → Classify → DEC check → Propose → Apply → Evidence). This replaces the separate post-session `/docpack` call.

**If docpack skips:** report "Docpack: skipped — no convention/architecture changes this session."

### 5. Optional stewards

- Architecture decisions → `/dec` (DEC digest in `.cursor/memory/decisions/`)

### 6. Confirm completion

Display session ended summary; next session run `/boot`.

## DEC closing check (advisory)

- Decisions this session: [list / none]
- DEC captured: [DEC-NNNN / None / Not yet]
- If decisions exist without DEC: recommend `/dec` or document "DEC Not Needed: reason"

Non-blocking.

## Gate awareness (advisory)

Read `.cursor/memory/STATE.yaml` when present:

- `runtime_install_gate`: if any flag false → do **not** recommend Copy-Item to `~/.cursor`
- `docpack_gate.extraction_close_allowed`: if false → governance closure still pending
- `kss_to_global_sync.performed`: if false → compare live `~/.cursor` with `KSS/commands/` before assuming sync

Non-blocking.
