# Project Rules (optional templates)

Copy selected files from this folder to **`<project>/.cursor/rules/`**.

These are **project-layer** rules — not installed to `~/.cursor/rules/`.

## Rule classes

| Class | Installed to | Purpose |
|-------|--------------|---------|
| **Portable global** | `~/.cursor/rules/` | Cross-project path discipline (from KSS `rules/`) |
| **Project optional** | `<project>/.cursor/rules/` | Gates in this folder (preflight, completion, module-creation) |
| **Project-only** | `<project>/.cursor/rules/` | Never exported to KSS — local preferences, checklists |

## Files in this folder

| Template | Purpose |
|----------|---------|
| `plan-preflight-gate.template.mdc` | Gate before sensitive plan todos |
| `plan-completion-gate.template.mdc` | ACTIVE.plan update on completion |
| `module-creation-gate.template.mdc` | Module vs kernel vs new module |

Customize lock IDs, DEC references, and project names after copy.

See [GOVERNANCE_LAYERS.md](../../docs/GOVERNANCE_LAYERS.md) for reading order.
