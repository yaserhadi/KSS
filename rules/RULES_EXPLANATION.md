# Rules — Copy Instructions

**Destination:** `~/.cursor/rules/` (machine-local user rules; Windows `%USERPROFILE%\.cursor\rules`)

| File | Class | Purpose | Activation |
|------|-------|---------|-----------|
| plans-location.mdc | Portable global | Plans in project `.cursor/plans/` only | `.mdc`, `alwaysApply: true` |
| git-main-merge-only.mdc | Portable global | Branch before edits on main | `.mdc`, `alwaysApply: true` |
| conflict-governance.mdc | Portable global | Silent conflict prohibition — stop + impact report | `.mdc`, `alwaysApply: true` |
| reports-location-global.md | Portable global | Reports in `.cursor/reports/` | Plain Markdown — **not verified** |

**Distributed is not the same as loaded.** Cursor documents activation for `.mdc` rule files and ignores plain `.md` in project rule folders. Whether machine-local files load in every workspace (including workspaces outside the user home directory) is not documented. After copying, confirm in **Customize → Rules**. See [Global and user rules](../docs/reference/how-to-make-global-rules.md).

## Rule taxonomy

| Class | Installed to | Examples |
|-------|--------------|----------|
| Portable global | `~/.cursor/rules/` | Files above |
| Project optional | `<project>/.cursor/rules/` (rename `*.template.mdc` → `*.mdc`) | plan-preflight, plan-completion, module-creation-gate |
| Project-only | Never KSS install | Local git preferences, project checklists |

Project optional templates: `templates/project-rules/`

See [docs/GOVERNANCE_LAYERS.md](../docs/GOVERNANCE_LAYERS.md).

**Response language:** `/en`, `/ar`, `/aren` commands — see [COMMANDS_EXPLANATION.md](../commands/COMMANDS_EXPLANATION.md).
