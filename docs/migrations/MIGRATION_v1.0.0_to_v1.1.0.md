# Migration: KSS v1.0.0 → v1.1.0

**Breaking changes:** post-docs SSOT, DEC paths, steward write targets.

See also: [KSS_EVOLUTION.md](../KSS_EVOLUTION.md) EVOL-001, EVOL-004.

---

## 1. Update DOC_POLICY in each project

Replace legacy paths:

```yaml
# REMOVE (v1.0.0)
user_docs: docs
architecture: docs/architecture
adr: docs/architecture/ADR

# ADD (v1.1.0)
plans: .cursor/plans
goals: .cursor/goals
decisions: .cursor/memory/decisions
architecture: .cursor/memory/decisions
adr: .cursor/memory/decisions
```

Copy from `templates/DOC_POLICY.template.yaml`.

---

## 2. Re-install Layer 2 runtime from KSS

After pulling KSS @ v1.1.0, sync to `~/.cursor/`:

1. `commands/` → `~/.cursor/commands/`
2. `rules/` → `~/.cursor/rules/`
3. `skills/` → `~/.cursor/skills/`
4. `agents/` → `~/.cursor/agents/`

Do not copy project gate templates to `~/.cursor/rules/`.

---

## 3. Stewards and commands

| Item | v1.0.0 | v1.1.0 |
|------|--------|--------|
| adr-steward output | `docs/architecture/ADR/` | `.cursor/memory/decisions/DEC-*.md` |
| ai-knowledge-steward write | included ADR folder | memory/, scoped plans/, closure reports |
| `/adr` command | human ADR | DEC digest (filename unchanged) |
| docpack | ADR advisory | DEC advisory |

---

## 4. Project rules (optional)

Copy from `templates/project-rules/` to `<project>/.cursor/rules/` if needed:
- plan-preflight-gate
- plan-completion-gate
- module-creation-gate

Read `templates/project-rules/README.template.md` for portable vs project-only classes.

---

## 5. Verify

```bash
rg "docs/architecture/ADR" ~/.cursor/agents ~/.cursor/commands
# expect: 0 hits after sync

find <project>/.cursor -name 'ADR-*.md'
# expect: 0
```

---

## 6. Framework vs project content

KSS ~47 files = structure. Project `.cursor/` = your SSOT content. Do not copy Jabal-specific memory/plans/reports into KSS.
