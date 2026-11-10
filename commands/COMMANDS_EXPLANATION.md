# Commands — Copy Instructions

**Destination:** `~/.cursor/commands/`  
**Source of truth:** `KSS/commands/` (this repository)  
**When to use which command:** [Command workflow guide](../docs/reference/command-workflow.md)

| File | Purpose | When to Use |
|------|---------|-------------|
| boot.md | Load project context | Start of every session |
| doplan.md | Governed planning gateway — scope/destination triage, pre-implementation risk assessment, CAP/BK binding, Active decision-registry candidates, gates; English brief for Plan mode | Before creating a new plan (does not write plan files; does not complete GAB) |
| session-end.md | Persist HANDOFF + closeout audit (finalize + docpack + **push**) | Before closing chat |
| docpack.md | Package `.cursor/` governance (tool used by finalize / session-end) | Mid-session or embedded |
| gw-review.md | Review the actual change set; finding dispositions; catch unrecorded decisions | Before merge — standalone, or embedded in `/git-finalize` |
| git-prepare.md | Create branch from main | Start new work |
| git-save.md | Commit progress | Safe checkpoint |
| git-finalize.md | Pre-merge review (gw-review protocol) → local merge→main → merge packaging; then **require `/session-end`** | Close feature onto local main |
| en.md | English-only reply | Override response language for this session |
| ar.md | Arabic-only reply | Override response language for this session |
| aren.md | Dual language (Arabic + English) | Override response language for this session |
| dec.md | Create DEC digest from text (primary) | Architecture decisions → `.cursor/memory/decisions/` |
| adr.md | Deprecated alias → `/dec` | Backward compatibility only |

## Command taxonomy

| Category | Commands |
|----------|----------|
| Runtime | boot, session-end, en, ar, aren |
| Planning | doplan (first line `/en` — English brief → Plan mode; includes scope/destination triage and risk assessment) |
| Governance | gw-review |
| Operational | git-prepare, git-save, git-finalize |
| Governance packaging | docpack, dec (`/adr` deprecated alias) |

See [docs/GOVERNANCE_LAYERS.md](../docs/GOVERNANCE_LAYERS.md).

## Language overrides

Use `/en`, `/ar`, or `/aren` when you need a explicit response language for the rest of the message. These commands replace the retired `agent-response-language-global.md` rule.

| Command | Effect |
|---------|--------|
| `/en` | Agent responds in English only (code and paths unchanged) |
| `/ar` | Agent responds in Arabic only |
| `/aren` | Agent responds in both Arabic and English |

User may ask in any language; the command sets the **response** language only.

## Repo-only (not copied to `~/.cursor/commands/`)

| File | Purpose |
|------|---------|
| COMMANDS_EXPLANATION.md | This index |
