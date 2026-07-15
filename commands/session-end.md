# Session End

**Purpose**: **Authoritative session closeout.** Persist HANDOFF, verify the delivery pipeline completed correctly (`/git-finalize` when applicable, `/docpack` lifecycle, and **remote publish** when product commits must leave the machine), then report a single done/not-done checklist.

**Related Commands**: `/boot`, `/gw-handoff`, `/docpack`, `/git-finalize`, `/git-save`

## Command roles (do not confuse)

| Command | Owns | Does **not** own |
|---------|------|------------------|
| `/git-finalize` | Local merge feature→`main`, delete **local** feature branch, **merge-moment** lifecycle packaging | Push / remote cleanup / declaring the **session** closed |
| `/docpack` | Packaging tool (standalone mid-session, or embedded by finalize / session-end) | Git merge; push; full closeout audit |
| **`/session-end`** | **Session closed only when the closeout checklist passes** (or owner records an explicit waiver) | Implementing product features |

After `/git-finalize`, you still run **`/session-end`**. Do **not** treat finalize as “session done.” Finalize is local Git + merge packaging; session-end verifies packaging, repairs gaps, and handles **push**.

---

## Workflow

### 1. Read paths

`.cursor/DOC_POLICY.yaml` or defaults: `.cursor/memory/`, `.cursor/reports/`, `.cursor/plans/`, `.cursor/memory/roadmap/`.

### 2. Closeout audit (required — run before calling the session closed)

Run these checks and **repair** failures (or stop with a clear block). Record each row in the final report.

#### 2.1 Git / finalize integrity

| Check | Pass criteria | If fail |
|-------|---------------|---------|
| Current branch | On `main` (or project default trunk) when session merged work | Switch / finish finalize first |
| Feature branch for this delivery | Local feature branch **gone** (or never created) | Complete `/git-finalize` or delete after merge |
| Merge tip | `main` HEAD is the expected merge / delivery commit | Do not close session claiming deliverable done |
| Working tree (product) | Clean for intended close, or dirty only with known non-product paths | Stage/commit on a branch via proper git commands — do not abandon dirty product work silently |

If this session used `/git-finalize`: confirm its report (merge hash, branch deleted, packaging step) exists in conversation or HANDOFF.

#### 2.2 Docpack / lifecycle integrity

| Check | Pass criteria | If fail |
|-------|---------------|---------|
| Tip vs SSOT | HANDOFF + STATE `head` match `main` HEAD | **Run `/docpack`** (session-end embedded) |
| Stale phrases | No false `pending merge` / `await merge` / Decision **open** after merge already happened | **Run `/docpack`** repair |
| ACTIVE.plan / BACKLOG | Match delivery state (no PARTIAL / await-merge when delivered) | **Run `/docpack`** |
| Closure / runbook | Merge commit recorded when this session closed a BK/UAT Decision | **Run `/docpack`** |

**Rule:** If `/git-finalize` step 5 already packaged correctly, docpack may be a **verify-only** pass (report “Docpack: verified — no further writes”). If anything drifts, **repair now** — do not tell the owner to “run docpack later.”

#### 2.3 Remote publish (push) — part of session close when product advanced

`/git-finalize` never pushes. **`/session-end` owns publish check.**

| Situation | Action |
|-----------|--------|
| `main` **ahead** of `origin/main` (or upstream) after this session’s product commits | **`git push origin main`** (or project upstream). Then confirm `main` matches remote. |
| Local feature branch was pushed earlier and is fully merged | Delete remote feature branch if still present: `git push origin --delete <branch>` |
| Already synced with origin | Report `Push: already synced` |
| `main` **behind** origin | **STOP** — do not force-push; pull/rebase per owner; do not claim session closed clean |
| Owner explicitly waives publish | Record waiver in HANDOFF (`Push: waived by owner — <reason>`); session may end locally only |
| Governance-only session (no product tip change) | Push N/A for product repo; still sync HANDOFF/STATE |

Never force-push `main`/`master` unless the owner explicitly orders it in this session.

### 3. Generate handoff summary

Session summary, test status, files changed, decisions, open issues, next steps.
Include **git tip** (branch + HEAD) and **push status**.

### 4. Update HANDOFF.md

Backup prior handoff to `.cursor/reports/HANDOFF_BACKUP_YYYY-MM.md`, then replace HANDOFF.md so it matches the **passed** closeout (tip + packaging + push).

### 5. Docpack apply/repair

Apply section 2.2: run `/docpack` in **session-end embedded** mode when verify fails or triggers match; otherwise record verified.

Docpack trigger reminders (non-exhaustive):

| Session produced… | Action |
|-------------------|--------|
| Conventions / DEC-worthy architecture | Run `/docpack` |
| BK/CAP delivery, merge, finalize, UAT/Decision close | Run `/docpack` (or verify after finalize packaging) |
| SSOT still disagrees with tip | Run `/docpack` repair |
| Explore-only / no durable outcomes | Skip with reason |

### 6. Optional stewards

- Architecture decisions not yet captured → `/dec`

### 7. Confirm completion (session closed only if checklist passes)

```text
SESSION CLOSEOUT
Git / finalize:  PASS | FAIL | N/A
Docpack:         verified | applied | skipped (<reason>)
Push:            synced | pushed (<range>) | waived | N/A | BLOCKED
Artefacts:       [list or none]
HANDOFF:         updated
Next:            /boot
```

**Do not** say “session ended” if Git/finalize or Docpack is FAIL, or Push is BLOCKED without an owner waiver.

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
