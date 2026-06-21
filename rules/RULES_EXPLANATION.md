# Rules — Copy Instructions

**Destination:** `~/.cursor/rules/` (portable global rules only)

| File | Class | Purpose |
|------|-------|---------|
| plans-location.mdc | Portable global | Plans in project `.cursor/plans/` only |
| reports-location-global.md | Portable global | Reports in `.cursor/reports/` |
| git-main-merge-only.mdc | Portable global | Branch before edits on main |

## Rule taxonomy

| Class | Installed to | Examples |
|-------|--------------|----------|
| Portable global | `~/.cursor/rules/` | Files above |
| Project optional | `<project>/.cursor/rules/` | plan-preflight, plan-completion, module-creation-gate |
| Project-only | Never KSS install | Local git preferences, project checklists |

Project optional templates: `templates/project-rules/`

See [docs/GOVERNANCE_LAYERS.md](../docs/GOVERNANCE_LAYERS.md).

**Response language:** `/en`, `/ar`, `/aren` commands — see [COMMANDS_EXPLANATION.md](../commands/COMMANDS_EXPLANATION.md).
