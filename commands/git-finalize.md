# Git Finalize

**Purpose:** Close a feature branch using **only local Git operations**: commit on the feature branch, run the pre-merge review, merge into local `main`, delete the local feature branch, then run **merge-moment lifecycle packaging** so SSOT artefacts match the merge tip.

## Assumption: local repository only

This command describes **no network push as its core job**: no `fetch`/`pull`/`push` required to finish the local merge. Publishing to `origin` is **`/session-end`’s responsibility** (closeout).

**Related commands:** `/git-prepare`, `/git-save`, `/gw-review`, `/session-end`, `/docpack`

## Relationship to `/session-end` and `/docpack`

```text
/git-finalize  →  pre-merge review (gw-review protocol) + local merge + merge packaging
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
- While still on `<branch>`, if this session materially changed the code being merged: update `.cursor/memory/HANDOFF.md` so it describes the **pre-merge** work (feature tip + gates). Post-merge packaging is step 6; **session closeout** is `/session-end`.

### 2. Commit on `<branch>`

```
git add -A
git commit -m "<message>"
```

- Adjust staging if you must not include some paths; **never** commit `.env`, keys, or tokens; respect `.gitignore`.
- Skip this step if there is nothing to commit.
- Prefer **not** staging gitignored `.cursor/` governance files into the product commit unless the project explicitly tracks them.

### 3. Pre-merge review (**required**)

Runs after the commit, so the reviewed change set is exactly what will merge, and before merge and branch deletion.

1. **Change set:** base = local `main`. Record `<base-sha>` (`git merge-base main <branch>`) and `<head-sha>` (`git rev-parse <branch>`). The change set is `git diff main...<branch>`.
2. **Approved scope:** the plan executed for this branch (the Current active plan, or the plan named in the session). Without a plan, use the Owner's request in this session. If no scope can be identified, the review is incomplete.
3. **Run the protocol in [`gw-review.md`](gw-review.md):** correctness, scope, safety and security, architecture and conventions, test evidence (from step 1), decision check, Doctrine_002 when applicable, finding dispositions. Proportional: a trivial diff gets a short review with N/A where checks don't apply.
4. **Reuse:** a prior review (standalone `/gw-review`, or a recorded Bugbot, security or human review) replaces step 3.3 only if it records the same `<base-sha>` and `<head-sha>` and its test evidence still applies. Otherwise run the protocol again.
5. **Verdict handling:**
   - **Approve** (advisory warnings allowed): continue to step 4 automatically; no Owner confirmation.
   - **Request Changes:** STOP before merge and branch deletion; report findings with dispositions.
   - **Block:** STOP before merge and branch deletion; report findings with dispositions.
   - **Incomplete** (diff not computable, required evidence missing, scope unidentifiable): STOP before merge and branch deletion; report what is missing.
   - In all three STOP cases, local `main` remains unchanged and the feature branch is preserved, including any commit created in step 2 before the review.
6. **After fixes:** run `/git-finalize` again. The review then covers the new `<head-sha>`, and earlier findings serve only as context.

### 4. Merge into local `main`

```
git checkout main
git merge <branch> --no-ff -m "Merge <branch> into main"
```

Resolve conflicts before continuing.

### 5. Delete the local feature branch

```
git branch -d <branch>
```

Use `-D` only if Git reports the branch is not fully merged after an intentional merge.

### 6. Post-merge lifecycle packaging (**required** at merge moment)

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

Advisory warnings and record-or-link findings from step 3 are noted in HANDOFF when relevant. Do not create backlog items from them.

**If nothing governance-related exists** for this delivery (no BK, no closure report, no ACTIVE notes): report explicitly why packaging was a no-op, still refresh HANDOFF + STATE tip.

This step does **not** replace `/session-end`. Session-end will **re-verify** packaging and perform **push**.

### 7. Report (local + handoff to session-end)

Report:

1. Commits made on `<branch>` (if any), and the pre-merge review: verdict, reviewed `<base-sha>`...`<head-sha>`, fresh or reused (with its source), advisory warnings
2. Merge commit hash on `main`
3. Confirmation that local `<branch>` was deleted
4. Docpack / lifecycle artefacts touched (or explicit no-op reason)
5. **Next required:** `/session-end` (verify + push / waiver)

**Do not** run `git push` inside `/git-finalize` unless the owner explicitly ordered push **in the same message as finalize** (prefer session-end for publish).

---

## Guards

- Refuse if on `main`.
- Refuse if tests or lint fail.
- Refuse to merge or delete the branch unless the pre-merge review verdict is Approve (fresh or validly reused).
- Do not ask the Owner to confirm an Approve verdict.
- Prefer refusing if post-merge lifecycle packaging (step 6) was skipped while a tracked BK/delivery/Decision closed in this finalize (unless owner explicitly overrides for trivial hotfixes with no SSOT).

## Merge style

Prefer `git merge <branch> --no-ff` so feature work stays visible in history.
