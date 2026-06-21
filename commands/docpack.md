---
name: docpack
description: Package session documentation into .cursor/ SSOT using Detect-Classify-DEC-Proposal-Apply-Evidence workflow.
---

# Docpack — Documentation Packaging

Update project documentation after meaningful work using a safe, confirmable workflow aligned with post-`docs/` SSOT.

## Purpose

Capture session changes in `.cursor/memory/`, `.cursor/reports/`, and project conventions — **not** legacy `docs/` as execution SSOT.

Phase C human documentation (when enabled) is optional and **never** execution authority.

## Doctrine

See [KSS_Doctrine_001](../docs/KSS_EVOLUTION.md#evol-005--extraction-direction-kss_doctrine_001) in project adoption docs.

## Workflow

```text
Detect
  ↓
Classify Artifacts
  ↓
DEC Completeness Check (advisory)
  ↓
Proposal
  ↓
Apply (after confirmation)
  ↓
Evidence
```

This is **not** automatic. Each phase produces output before the next runs.

---

## 1. Read Paths

Read `.cursor/DOC_POLICY.yaml` for canonical paths only:

- `.cursor/memory/` — authoritative and operational content
- `.cursor/reports/` — evidence ledger
- `.cursor/memory/decisions/` — DEC digests
- `.cursor/memory/conventions/` — engineering conventions (optional)
- `.cursor/plans/` — temporary intent (active plans only)

If `DOC_POLICY.yaml` is missing, use the defaults above and proceed.

**Do not** read deleted or legacy `docs/` trees for execution behavior.

---

## 2. Detect

Extract signals from the session:

- What files were modified?
- What decisions were made?
- Was there user-facing impact (Phase C candidate)?
- Were there lessons learned?

### Documentation gap check

| Change type | Check for stale or missing docs |
|-------------|-----------------------------------|
| DB schema, migrations | `.cursor/memory/conventions/database-conventions.md` |
| API, routes, auth flow | `.cursor/memory/conventions/api-conventions.md` |
| RBAC, permissions, roles | `.cursor/memory/conventions/rbac-conventions.md` |
| Test helpers, patterns | `tests/README.md` (project code doc, not SSOT) |

---

## 3. Classify Artifacts

For each detected change, classify using the governance layer model:

| Layer | Location | Examples |
|-------|----------|----------|
| **L1 Authoritative** | `.cursor/memory/` | MANIFEST, DEC-*, INTEGRITY_RULES |
| **L2 Operational** | `.cursor/memory/` | STATE.yaml, HANDOFF.md, ACTIVE.plan |
| **L3 Evidentiary** | `.cursor/reports/` | Session reports, audit evidence |
| **L4 Temporary** | `.cursor/plans/` | Active plan todos only |
| **Informational** | `.cursor/goals/`, LESSONS | GOALS, ROADMAP — not execution gates |
| **Phase C** | Human docs (optional) | End-user guides — never SSOT |

**Agent rules:**

- Do **not** treat a **report** as authoritative over **DEC**
- Do **not** treat an **archived plan** as current over **STATE/HANDOFF**
- Do **not** write to `docs/architecture/ADR/` — use `/adr` → DEC digest in `.cursor/memory/decisions/`

---

## 4. DEC Completeness Check (Advisory)

Scan session context for decision indicators:

**Triggers** (any suggests an architectural decision):

- Module boundary created or changed
- API contract modified (routes, DTOs, auth, versioning)
- Tenancy / DB schema / migrations changed
- Security model / RBAC / permissions changed
- Language: "decided", "chose", "alternative", "trade-off", "breaking change"

**Check:** If triggers detected AND no new/updated DEC in session → output **DEC Missing Warning** (advisory, not blocking).

**Output format:**

```
**DEC Completeness Check (Advisory)**:
- Decision indicators detected: [list]
- DEC coverage found: [DEC-NNNN] / None
- Status: OK / Warning

**If Warning**:
> Recommended: Run `/adr` to capture the decision as a DEC digest.
> If DEC not needed: "DEC Not Needed: <reason>"
```

---

## 5. Propose

Present update plan grouped by track:

```
DOCPACK PROPOSAL

**DEC Completeness Check**: [OK / Warning]

AI Track (.cursor/memory/) — via AI Knowledge Steward:
- [ ] HANDOFF.md — session summary [required]
- [ ] STATE.yaml — specific field changes [when meaningful]
- [ ] LESSONS.md — [if applicable]
- [ ] VERSIONS.md — [if breaking change]
- [ ] conventions/*.md — [if schema/API/RBAC changed]

Evidence Track (.cursor/reports/):
- [ ] [report-name].md — [if audit/evidence warranted]

DEC Track (.cursor/memory/decisions/):
- [ ] DEC-NNNN-slug.md — [if /adr run or manual DEC needed]

Phase C (optional — human docs only):
- [ ] [describe human-facing doc] — [only if Phase C enabled AND user impact confirmed]

Project code docs (non-SSOT):
- [ ] tests/README.md — [if test helpers or patterns changed]

Confirm? [Y/n] or specify which to apply
```

**Forbidden proposal targets:**

- `docs/architecture/ADR/` as docpack destination
- `docs/reference/` as default track
- `docs/` as execution SSOT in propose/apply
- User Documentation Steward for live `docs/` SSOT updates

---

## 6. Apply

Only after confirmation:

1. **AI Knowledge Steward** — updates `.cursor/memory/` (HANDOFF, STATE, LESSONS, conventions, VERSIONS)
2. **ADR Steward** — if DEC digest requested via `/adr` (not via docpack directly unless confirmed)
3. **User Documentation Steward** — **Phase C only** when explicitly enabled and user impact confirmed; writes human-facing docs, never execution SSOT
4. Write evidence to `.cursor/reports/` when the session warrants an audit trail

Output summary of what was updated and where.

**Fallback:** If stewards cannot be invoked, apply updates directly to confirmed targets and report what changed. Do not fail silently.

---

## 7. Evidence

After apply, record:

- Files updated (paths)
- DEC IDs created or referenced
- Reports written
- STATE/HANDOFF fields changed

If docpack closes a gated phase, note gate flags only in STATE when owner-authorized — do not self-approve `runtime_install_gate` or KSS publish gates.

---

## Enforcement Defaults

| Target | When | Confirmation |
|--------|------|--------------|
| HANDOFF.md | End of meaningful session | Auto-apply safe default |
| STATE.yaml `last_update` | Session touch | Auto-apply safe default |
| STATE.yaml meaningful fields | Phase/gate changes | Require confirmation |
| LESSONS.md | Lessons learned | Require confirmation |
| Conventions | Schema/API/RBAC changes | Require confirmation |
| DEC digests | Architecture decisions | Require confirmation (/adr) |
| Reports | Audit/evidence needed | Require confirmation |
| Phase C human docs | User impact + Phase C enabled | Require confirmation |
| tests/README.md | Test pattern changes | Require confirmation |

---

## Integration with Stewards

| Steward | Track | Scope |
|---------|-------|-------|
| **AI Knowledge Steward** | `.cursor/memory/` | Operational and authoritative updates |
| **ADR Steward** | `.cursor/memory/decisions/` | DEC digests via `/adr` |
| **User Documentation Steward** | Phase C human docs | Optional; never execution SSOT |

Each steward writes only to its assigned track.

---

## When to Use

Run `/docpack` at the end of any meaningful work session:

- After completing a feature
- After fixing a bug with lessons learned
- After architectural decisions (pair with `/adr`)
- Before ending a development session

---

## Example

```
/docpack
```

The command analyzes the session, classifies artifacts, checks DEC coverage, proposes updates, and applies only after confirmation.
