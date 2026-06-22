# Review

**Purpose**: Review a diff or change set for safety, security, and rule compliance.

**Related Commands**: `/gw-triage`, `/gw-riskcheck`, `/gw-handoff`

---

**Workflow**:
1. Analyze the diff or change set
2. Check against safety rules:
   - No hardcoded secrets or credentials
   - No destructive actions without approval
   - No production changes without explicit approval
3. Check against security rules:
   - Input validation present
   - No SQL injection vulnerabilities
   - No XSS vulnerabilities
   - Proper authentication/authorization
4. Check against project/compliance rules:
   - Follows modular boundaries
   - Least privilege applied
   - Audit trail maintained where required
5. **Architectural Decision Check**:
   - Does this change involve a choice between alternatives?
   - Does it establish a new pattern, API contract, or module boundary?
   - Does it have long-term architectural implications?
   - Does it affect security boundaries or data access rules?
   - Is there an Existing DEC that covers this decision?
6. **Doctrine_002 / Layer Responsibility Check** (mandatory):
   - Governing rule: `docs/KSS_Doctrine_002.md` (`classification: governance_rule`)
   - Implementation aid: `docs/LAYER_VIOLATION_CHECKLIST.md`
   - Any checklist **Fail** → add to **Critical** findings (block merge)

**Output Format**:
```
## Change Review

**Scope**: [Brief description of what is being reviewed]

**Findings**:

### Critical (must fix before merge)
- [ ] [Finding description]

### Warning (should fix)
- [ ] [Finding description]

### Info (consider)
- [ ] [Finding description]

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
> Run `/adr` (DEC digest) before proceeding. Paste the discussion context.
> Decision indicators detected: [list what triggered this]

**Doctrine_002 / Layer Responsibility Check** (mandatory):
- Governing rule: [`KSS_Doctrine_002.md`](../docs/KSS_Doctrine_002.md)
- Checklist: [`LAYER_VIOLATION_CHECKLIST.md`](../docs/LAYER_VIOLATION_CHECKLIST.md)
- [ ] L2 defines authority: Pass / Fail / N/A
- [ ] L3 overrides L1: Pass / Fail / N/A
- [ ] docs/ as execution SSOT: Pass / Fail / N/A
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
- When in doubt, prefer safety over speed
- Always cite evidence for findings
- Stop and ask if context is missing
