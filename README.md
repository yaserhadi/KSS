# KSS Framework

**Knowledge Stewardship System** — a lightweight framework for managing knowledge, decisions and governance when you build software with Cursor AI agents.

---

## Why KSS exists

An AI agent starts every chat without memory of earlier chats. When a conversation runs out of context, or a new agent picks up the work, the project's goals, decisions, constraints and current execution position are gone unless someone re-explains them. Agents then guess: they repeat settled debates, contradict earlier decisions, edit the wrong branch, or lose track of what was already delivered.

KSS keeps that knowledge in a small set of project files that every session reads first, and adds commands and rules that help agents plan, execute, review and close work without stepping outside the project's boundaries.

**What KSS is not:**

- It does not replace Cursor or build your application. It makes work with Cursor agents more consistent, reviewable and continuous across sessions.
- It is not a technical enforcement engine. Rules and commands are instructions to the agent. Keep tests, branch protection and code review as your real controls.
- It does not own your project's content. KSS supplies structure; your project supplies its goals, constraints and decisions.

---

## How daily work changes

| Without KSS | With KSS |
|-------------|----------|
| Each chat starts cold; you re-explain the project | `/boot` loads goals, constraints, decisions, execution state and the last handoff |
| Plans are ad hoc and live in chat history | `/preplan` prepares a Pre-Plan Charter; Cursor Plan Mode writes a plan you review before you press Build |
| Merges happen whenever the agent finishes | `/git-finalize` reviews the actual change set, then merges into local `main` only on Approve |
| The next session does not know what happened | `/session-end` verifies the closeout, writes `HANDOFF.md` and publishes |
| Decisions are scattered across chats | `/dec` records decision digests (DEC) under `.cursor/memory/decisions/` |

Typical lifecycle (not every task needs every step):

```text
/boot → /preplan → Plan Mode (paste Charter) → review → Owner Build → /git-finalize → /session-end
```

See the [Command workflow guide](docs/reference/command-workflow.md) for the full workflow and a worked example.

---

## First 10 minutes

Try KSS on a throwaway project before adopting it.

1. **Get KSS.** Clone this repository.
2. **Install the commands.** Back up `~/.cursor/commands/` if it exists, then copy the files from [`commands/`](commands/) into it (skip `COMMANDS_EXPLANATION.md`). Restart Cursor if the new commands do not appear.
3. **Create a test project** with Git, and work on a branch (not `main`).
4. **Create the minimum startup files** from [`templates/`](templates/):

   | Template | Copy to |
   |----------|---------|
   | `DOC_POLICY.template.yaml` | `.cursor/DOC_POLICY.yaml` |
   | `AI_ENTRY.template.md` | `.cursor/memory/AI_ENTRY.md` |
   | `PROJECT_MANIFEST.template.md` | `.cursor/memory/PROJECT_MANIFEST.md` |
   | `STATE.template.yaml` | `.cursor/memory/STATE.yaml` |
   | `HANDOFF.template.md` | `.cursor/memory/HANDOFF.md` |
   | `GOALS.template.md` | `.cursor/memory/GOALS.md` |

5. **Fill the placeholders.** Give the project a name, write one goal in `GOALS.md`, set `status: active` in `STATE.yaml`, and write one next action in `HANDOFF.md`.
6. **Run `/boot`** in an Agent chat. The agent should report the project, the execution status and your next action. If it stops, a startup file is missing or unreadable.
7. **Run `/preplan`** with a small request (for example "add a README section"). You get a Pre-Plan Charter — analysis only; no files change.
8. **Run `/session-end`** in Agent mode. It writes `HANDOFF.md` for the next session.

Then follow [INSTALL.md](INSTALL.md) for the full setup.

---

## What to install

KSS separates three kinds of project artifacts. Only the first group is needed to start a session.

| Tier | Artifacts | Why |
|------|-----------|-----|
| **1. Minimum startup** | `.cursor/memory/AI_ENTRY.md`, `PROJECT_MANIFEST.md`, `STATE.yaml`, `HANDOFF.md`, `GOALS.md` · `.cursor/DOC_POLICY.yaml` | `/boot` loads the five memory files; it stops if `GOALS.md` cannot be read. `DOC_POLICY.yaml` maps paths — `/boot` falls back to default paths when it is missing, but the compatibility contract requires it. |
| **2. Standard installation scaffolds** | `.cursor/memory/INTEGRITY_RULES.md`, `VERSIONS.md`, `.cursor/memory/decisions/`, `.cursor/plans/`, `.cursor/reports/` | Created by the [installation guide](INSTALL.md). Read on demand or before execution; not `/boot` blockers. |
| **3. Workflow-specific** | `.cursor/plans/ACTIVE.plan.md`, `DEC-*.md` digests, reports, `LESSONS.md`, `conventions/`, project rules from `templates/project-rules/`, a pre-production policy | Needed only when you use the matching workflow. For example, `/boot` reads `ACTIVE.plan.md` only if it exists, while `/git-finalize`, `/docpack` and `/session-end` keep it current once you work with plans. |

Global runtime (installed per user, not per project): commands, agents, skills and the portable rule files. Whether Cursor actually loads each rule file is a separate question — see [Rules](#rules).

---

## Rules

KSS distributes four portable rule files in [`rules/`](rules/). Cursor documents activation only for `.mdc` rule files, so the four files are not equivalent:

| File | Format | Activation |
|------|--------|-----------|
| `plans-location.mdc`, `git-main-merge-only.mdc`, `conflict-governance.mdc` | `.mdc`, `alwaysApply: true` | Documented always-apply format |
| `reports-location-global.md` | Plain Markdown | **Not verified** — Cursor ignores plain `.md` in project rule folders |

Placing files in `~/.cursor/rules/` is not proof that Cursor loads them, especially for workspaces outside your home directory. Check **Customize → Rules** in Cursor, and see [Global and user rules](docs/reference/how-to-make-global-rules.md) for what is documented and what is not.

---

## Repository contents

| Folder | Purpose |
|--------|---------|
| [templates/](templates/) | Project memory scaffolds with placeholders |
| [commands/](commands/) | Cursor commands (copy to `~/.cursor/commands/`) |
| [rules/](rules/) | Portable rule files (copy to `~/.cursor/rules/`; see [Rules](#rules)) |
| [agents/](agents/) | Subagent definitions (copy to `~/.cursor/agents/`) |
| [skills/](skills/) | Reusable skills (copy to `~/.cursor/skills/`) |
| [docs/](docs/) | Handbook and reference documentation |
| [examples/](examples/) | Sample project files |

**Framework = structure. Project = content.** KSS stays small (about 65 tracked files); a mature project's `.cursor/` holds the real governance content.

---

## Documentation

| Read this | When |
|-----------|------|
| [KSS Handbook](docs/handbook.md) | Concepts, file inventory, authority model |
| [Installation Guide](INSTALL.md) | Full setup and upgrade notes |
| [Command workflow guide](docs/reference/command-workflow.md) | What each command does, the Pre-Plan and plan-review workflow, worked example |
| [Global and user rules](docs/reference/how-to-make-global-rules.md) | Where Cursor rules live and what activation is verified |
| [Decision digests (`/dec`)](docs/reference/adr-command.md) | Recording decisions |
| [Philosophy](philosophy.md) | Framework vs project, three-layer architecture |
| [Evolution log](docs/KSS_EVOLUTION.md) | Version history |

---

## KSS compatibility contract

A repository is "KSS-compatible" if it has:

**Mandatory:**
- `.cursor/DOC_POLICY.yaml`
- `.cursor/memory/AI_ENTRY.md`
- `.cursor/memory/PROJECT_MANIFEST.md`
- `.cursor/memory/INTEGRITY_RULES.md`
- `.cursor/memory/STATE.yaml`
- `.cursor/memory/HANDOFF.md`
- `.cursor/memory/GOALS.md`

**Workflow-specific / optional:**
- `.cursor/plans/` and `.cursor/plans/ACTIVE.plan.md` (required once you use plan workflows)
- `.cursor/reports/`
- `.cursor/memory/decisions/`
- `.cursor/memory/VERSIONS.md`
- `.cursor/memory/LESSONS.md`
- `.cursor/memory/conventions/`

---

## License

This repository does not currently include a license. Without one, default copyright applies: public visibility lets you read the files (and fork them on GitHub under GitHub's Terms of Service), but it does not grant general rights to reuse, modify or redistribute them. A license decision is pending.
