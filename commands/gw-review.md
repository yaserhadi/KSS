# Review

**Purpose**: Review the **actual change set** — `git diff <base>...HEAD` or the stated files — for correctness, scope, security, architecture, and rule compliance before merge. Reviews changes, not plans.

**Related Commands**: `/doplan`, `/dec`, `/git-finalize`

---

## When to use

| Situation | Review |
|-----------|--------|
| Code, security, data, schema, or governance-affecting change before merge | **Required** |
| Trivial, reversible edit (typo, comment, formatting) | Optional |
| An equivalent review is already documented for the same diff (e.g. recorded Bugbot, security review, or human review) | Not needed again unless there is a material reason (diff changed, new risk found) |

A verdict is not an instruction to merge or push. Merge is `/git-finalize`; publication is `/session-end`.

---

**Workflow**:
1. Identify the change set: base branch, `git diff --name-status <base>...HEAD`, and the plan or scope it implements
2. **Correctness**: the change does what it claims; edge cases and error paths handled
3. **Scope**: the diff matches the approved plan/scope; no unrelated files; no unjustified changes
4. Check against safety rules:
   - No hardcoded secrets or credentials
   - No destructive actions without approval
   - No production changes without explicit approval
5. Check against security rules:
   - Input validation present
   - No SQL injection vulnerabilities
   - No XSS vulnerabilities
   - Proper authentication/authorization
6. Check against project/compliance rules:
   - Follows modular boundaries
   - Least privilege applied
   - Audit trail maintained where required
7. **Enforceable Convention Check** (when the change touches API, DB/migrations, or RBAC surfaces):
   - Apply the adopting project's `.cursor/memory/conventions/` and active MANIFEST/DEC locks for those surfaces
   - Items tagged **Advisory** in conventions are guidance only; MANIFEST/DEC/INTEGRITY win on conflict
   - Mark N/A when the diff has no API/DB/RBAC product surface
8. **Test and verification evidence**: tests/gates run for this diff, with results; missing evidence → finding
9. **Architectural Decision Check**:
   - Does this change involve a choice between alternatives?
   - Does it establish a new pattern, API contract, or module boundary?
   - Does it have long-term architectural implications?
   - Does it affect security boundaries or data access rules?
   - Is there an Existing DEC that covers this decision?
10. **Doctrine_002 / Layer Responsibility Check** (when the change touches governance layers; otherwise N/A):
    - Governing rule: [`KSS_Doctrine_002.md`](../docs/KSS_Doctrine_002.md) (`classification: governance_rule`)
    - Implementation aid: [`LAYER_VIOLATION_CHECKLIST.md`](../docs/LAYER_VIOLATION_CHECKLIST.md)
    - Adopting project: `.cursor/CURSOR_RUNTIME.md` when present (project SSOT wins)
    - Any checklist **Fail** → add to **Critical** findings (block merge)
11. **Finding disposition**: give every finding exactly one disposition (below)

Every check allows **N/A** when irrelevant to the change scope.

## Finding disposition

| Disposition | Meaning | Effect on verdict |
|-------------|---------|-------------------|
| **Blocks this merge** | Critical issue introduced or exposed by this change | Block |
| **Fix within authorized scope** | Must be fixed before merge, inside the already-approved scope | Request Changes |
| **Record or link for later evaluation** | Pre-existing or unrelated to this change | Does not block this merge by itself |
| **Owner product decision** | Potential new requirement or scope change | None — surfaced for the Owner |

Warnings are advisory unless assigned **Fix within authorized scope**; that disposition requires **Request Changes** until resolved.

Evidence fields per finding:

- **Status:** Confirmed / Observed / Awaiting verification
- **Origin:** Baseline / Regression / Unknown origin
- **Severity** (reviewer's assessment of technical impact) — separate from **priority** (Owner-controlled)
- **Evidence:** source location, command output, or reproduction steps
- **Linked item:** existing finding, technical-debt item, or backlog item, if any

Do not create backlog items or widen the implementation scope from a review; record or link per the project's own registers.

## Verdict

| Verdict | When |
|---------|------|
| **Approve** | No Critical findings; any warnings are recorded |
| **Request Changes** | Fixable issues must be resolved before merge |
| **Block** | Critical findings — security or data-loss risk, a Doctrine Fail, unapproved scope, or a missing required decision. Re-review after the fix |

**Output Format**:
```
## Change Review

**Scope**: [Change set reviewed — base...head, files, plan/scope it implements]

**Findings**:

### Critical (must fix before merge)
- [ ] [Finding description]

### Warning (should fix)
- [ ] [Finding description]

### Info (consider)
- [ ] [Finding description]

**Findings disposition**:

| Finding | Disposition | Status | Origin | Severity | Evidence | Linked item |
|---------|-------------|--------|--------|----------|----------|-------------|
| [short title] | Blocks this merge / Fix within scope / Record or link / Owner decision | Confirmed / Observed / Awaiting verification | Baseline / Regression / Unknown | Low / Medium / High / Critical | [path, output, repro] | [ID or N/A] |

**Correctness and Scope**:
- [ ] Change does what it claims
- [ ] Diff matches approved scope; no unrelated files

**Safety Check**:
- [ ] No hardcoded secrets
- [ ] No destructive actions without approval
- [ ] Rollback path exists

**Security Check**:
- [ ] Input validation present
- [ ] No injection vulnerabilities
- [ ] Auth/authz properly implemented

**Compliance Check**:
- [ ] Follows module boundaries
- [ ] Least privilege applied
- [ ] Audit requirements met

**Enforceable Convention Check** (when API / DB / RBAC surfaces touched):
- [ ] Project conventions and active MANIFEST/DEC locks applied
- [ ] N/A — no API/DB/RBAC product surface

**Test and Verification Evidence**:
- [ ] [Command/gate] — [result] / Pending / N/A

**Verdict**: Approve / Request Changes / Block

**Summary**:
[1-2 sentence summary of review findings]

**Evidence Referenced**:
- [files/commands/tests that support the conclusions]

**Architectural Decision Check**:
- [ ] Involves architectural choice: Yes / No
- [ ] Decision type: [DB/API/Security/Module/Integration/None]
- [ ] Existing DEC covers this: [DEC-NNNN] / None found / N/A
- [ ] DEC recommended: Yes / No

**If DEC recommended**:
> Run `/dec` before proceeding. Paste the discussion context.
> Decision indicators detected: [list what triggered this]

**Doctrine_002 / Layer Responsibility Check** (N/A when no governance layer touched):
- Governing rule: [`KSS_Doctrine_002.md`](../docs/KSS_Doctrine_002.md)
- Checklist: [`LAYER_VIOLATION_CHECKLIST.md`](../docs/LAYER_VIOLATION_CHECKLIST.md)
- [ ] L2 defines authority: Pass / Fail / N/A
- [ ] L3 overrides L1: Pass / Fail / N/A
- [ ] Project `docs/` not used as execution SSOT: Pass / Fail / N/A
- [ ] Reports over DEC: Pass / Fail / N/A
- [ ] Ungated runtime install: Pass / Fail / N/A
- **Any Fail** → Verdict must be **Block** or **Request Changes**
```

**Decision Indicators** (triggers DEC recommendation):
- Trade-off language: "chose X over Y", "decided to", "alternative considered"
- New patterns: database schema, API endpoint design, authentication flow
- Breaking changes: contract modifications, interface changes
- Security boundaries: permission model, data access rules
- Module boundaries: new module, cross-module dependency

---

## Notes

- This command complements the `safe-coding-practices` skill
- Pre-implementation scope and risk assessment belongs to `/doplan`; this command reviews what was actually changed
- When in doubt, prefer safety over speed
- Always cite evidence for findings
- Stop and ask if context is missing
