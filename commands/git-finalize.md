# Git Finalize

**Purpose:** Close a feature branch using **only local Git operations**: commit on the feature branch, merge into local `main`, delete the local feature branch, then run **merge-moment lifecycle packaging** so SSOT artefacts match the merge tip.

## Assumption: local repository only

This command describes **no network push as its core job**: no `fetch`/`pull`/`push` required to finish the local merge. Publishing to `origin` is **`/session-end`’s responsibility** (closeout).

**Related commands:** `/git-prepare`, `/git-save`, `/session-end`, `/docpack`, `/gw-handoff`

## Relationship to `/session-end` and `/docpack`

```text
/git-finalize  →  local merge + merge packaging
/session-end   →  authoritative session close (verify finalize + verify/repair docpack + PUSH)
```

| After `/git-finalize` succeeds… | Next |
|---------------------------------|------|
| Session still open | **Required:** run `/session-end` to close the session properly |
| Need mid-session packaging only | `/docpack` standalone (unusual right after finalize — prefer session-end) |
| Want remote updated | **`/session-end`** pushes (or records owner waiver) — not this command |

Do **not** tell the owner the delivery is “fully done” after finalize alone. Finalize ends with: merge hash + local branch deleted + packaging touched + **reminder to run `/session-end`**.

## Workflow

`<branch>` = current feature branch name. **Refuse if you are on `main`.**

### 1. Quality checks

- Tests pass (e.g. `php artisan test`).
- Lint passes (e.g. `./vendor/bin/pint --test`).
- While still on `<branch>`, if this session materially changed the code being merged: update `.cursor/memory/HANDOFF.md` so it describes the **pre-merge** work (feature tip + gates). Post-merge packaging is step 5; **session closeout** is `/session-end`.

### 2. Commit on `<branch>`

```
git add -A
git commit -m "<message>"
```

- Adjust staging if you must not include some paths; **never** commit `.env`, keys, or tokens; respect `.gitignore`.
- Skip this step if there is nothing to commit.
- Prefer **not** staging gitignored `.cursor/` governance files into the product commit unless the project explicitly tracks them.

### 3. Merge into local `main`

```
git checkout main
git merge <branch> --no-ff -m "Merge <branch> into main"
```

Resolve conflicts before continuing.

### 4. Delete the local feature branch

```
git branch -d <branch>
```

Use `-D` only if Git reports the branch is not fully merged after an intentional merge.

### 5. Post-merge lifecycle packaging (**required** at merge moment)

After a successful merge onto local `main`, run **`/docpack` in embedded / lifecycle mode** so artefacts match the merge tip **now** (do not leave pending-merge wording until “later”).

**Must align with merge commit / `main` HEAD:**

| Artefact | Update when relevant |
|----------|----------------------|
| HANDOFF.md | Tip = merge commit; Decision/delivery status COMPLETE if closed |
| STATE.yaml | `focus.branch` / `head` / BK status / `next_action` |
| ACTIVE.plan.md | Last-completed notes — no stale PARTIAL / pending merge |
| BACKLOG.md | BK lifecycle row status text (do **not** mint BK-IDs) |
| Closure / UAT / evidence report(s) | Strip false `pending merge` / open-Decision wording |
| Runbook Result Record | Record merge commit when a runbook was used |

**Do not skip** packaging because “only product code changed” or “HANDOFF already mentions the merge.”

**If nothing governance-related exists** for this delivery (no BK, no closure report, no ACTIVE notes): report explicitly why packaging was a no-op, still refresh HANDOFF + STATE tip.

This step does **not** replace `/session-end`. Session-end will **re-verify** packaging and perform **push**.

### 6. Report (local + handoff to session-end)

Report:

1. Commits made on `<branch>` (if any)
2. Merge commit hash on `main`
3. Confirmation that local `<branch>` was deleted
4. Docpack / lifecycle artefacts touched (or explicit no-op reason)
5. **Next required:** `/session-end` (verify + push / waiver)

**Do not** run `git push` inside `/git-finalize` unless the owner explicitly ordered push **in the same message as finalize** (prefer session-end for publish).

---

## Guards

- Refuse if on `main`.
- Refuse if tests or lint fail.
- Prefer refusing if post-merge lifecycle packaging (step 5) was skipped while a tracked BK/delivery/Decision closed in this finalize (unless owner explicitly overrides for trivial hotfixes with no SSOT).

## Merge style

Prefer `git merge <branch> --no-ff` so feature work stays visible in history.
