# Global and User Rules in Cursor

**Audience:** KSS adopters.

**Purpose:** Explain where Cursor rules can live, what Cursor documents about loading them, which KSS rule files rely on documented behavior, and what remains unverified. KSS does not claim a rule is enforced unless Cursor documents that it is loaded.

Sources (checked 2026-11-13): [Cursor Rules](https://cursor.com/docs/rules.md) and [Cursor Help — Rules](https://cursor.com/help/customization/rules.md). Re-check them after Cursor updates.

---

## Where rules can live

| Source | Location | Scope | Documented behavior |
|--------|----------|-------|---------------------|
| Project rules | `<project>/.cursor/rules/*.mdc` | That project; version-controlled | Must use `.mdc`. A plain `.md` file in `.cursor/rules` is ignored. |
| User Rules (Settings) | Customize → Rules | All your projects; stored on your Cursor account and synced | Plain text; used by Agent (Chat) |
| User rule files | `~/.cursor/rules` (Windows: `%USERPROFILE%\.cursor\rules`) | Machine-local; not synced | Location documented; see "Not verified" below |
| Team Rules | Cursor dashboard | Team members (Team / Enterprise plans) | Can be enforced by admins |
| `AGENTS.md` | Project root or subfolders | That project | Plain Markdown, picked up automatically |

Rules apply only to Agent (Chat). They do not affect Tab completion or Bugbot reviews.

## How `.mdc` rules activate

| `alwaysApply` | `description` | `globs` | Behavior |
|---------------|---------------|---------|----------|
| `true` | — | — | Always included |
| `false` | — | provided | Attached when a matching file is in context |
| `false` | provided | omitted | Agent pulls it in when the description is relevant |
| `false` | omitted | omitted | Only when you @-mention the rule |

## Precedence

When rules conflict, Cursor documents the order **Team Rules → Project Rules → User Rules**: all applicable rules are merged, and earlier sources take precedence when guidance conflicts.

Cursor identifies rules by their **full file path**. Two rules with the same filename in different folders both apply if their conditions match — a project file does **not** override a user file because it has the same name. To change a user-level rule for one project, write an explicit project `.mdc` rule that states the project's requirement; project rules take precedence over user rules on conflict.

---

## KSS rule files

| File | Format | Status |
|------|--------|--------|
| `plans-location.mdc` | `.mdc`, `alwaysApply: true` | Documented always-apply format |
| `git-main-merge-only.mdc` | `.mdc`, `alwaysApply: true` | Documented always-apply format |
| `conflict-governance.mdc` | `.mdc`, `alwaysApply: true` | Documented always-apply format |
| `reports-location-global.md` | Plain Markdown, no frontmatter | **Not verified** — may be ignored |

The `.mdc` format and `alwaysApply` behavior are documented for project rules. The documentation does not state that machine-local files follow every project-rule behavior; treat even the `.mdc` files as "verify on your machine."

## Not verified

Current Cursor documentation does not establish:

- Whether files in `%USERPROFILE%\.cursor\rules` load for a workspace on another directory or drive (for example `C:\xampp\htdocs\<project>` or `D:\work\<project>`).
- Whether plain `.md` files in the machine-local folder are loaded, or ignored as they are in project rule folders.
- Precedence between account User Rules and machine-local rule files.
- Whether Cursor must be reloaded after adding files, and how caching or symlinks behave.

Do not rely on any of these until you have confirmed them in your own environment.

## How to check on your machine

1. Open **Customize → Rules** in Cursor. It lists rules and their status.
2. Confirm that each KSS rule you copied appears there.
3. If a rule you need is missing, use a documented alternative:
   - add its text as a **User Rule** in Customize → Rules (all projects, synced), or
   - add an equivalent `.mdc` rule to the project's `.cursor/rules/` (that project, version-controlled).

---

## Creating a new user-level rule

- For a rule that must apply across all projects, prefer a **User Rule** in Customize → Rules — this is the documented cross-project mechanism.
- If you keep a machine-local file in `~/.cursor/rules/`, use the `.mdc` format with frontmatter, then confirm it in Customize → Rules:

```markdown
---
description: Short description of the rule
alwaysApply: true
---

# <Rule name>

1. First requirement
2. Second requirement
```

- For project-specific requirements, create the rule in the project's `.cursor/rules/` (Customize → Rules → Add Rule, or `/create-rule`).

## Managing machine-local rule files

- **List:** `ls ~/.cursor/rules/` (Windows: `dir %USERPROFILE%\.cursor\rules`)
- **Edit:** open the file in your editor, then confirm the change in Customize → Rules.
- **Disable temporarily:** move the file out of the folder (keep a copy). Whether other renaming schemes disable a rule is not documented.

## Best practices

- Keep rules short and focused (Cursor suggests under 500 lines).
- Name files descriptively (`plans-location.mdc`, not `rule1.mdc`).
- Document why each rule exists.
- Do not put project-specific rules in the user folder.
- Rules guide the agent; they are not a security control. Keep tests, branch protection and review in place.
