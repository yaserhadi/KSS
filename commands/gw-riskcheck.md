# Risk Check

**Purpose**: Identify security, compliance, and production risks. Flag required approvals.

**Related Commands**: `/gw-triage`, `/gw-handoff`, `/gw-review`

---

**Workflow**:
1. Analyze the proposed change for risk categories:
   - **Security**: Auth, permissions, secrets, injection, XSS
   - **Compliance**: Audit trails, data retention, PII handling
   - **Production**: Live system impact, downtime, data loss
   - **Data**: Schema changes, migrations, bulk updates
   - When assessing compliance risks, refer to "applicable regulatory and internal compliance requirements" rather than assuming specific frameworks (SOX, PCI-DSS, etc.) unless explicitly mentioned by the user.
2. Check against CAB governance rules (user rules)
3. Approvals must align with Global Rules (CAB, Production Safety, Data Protection)
4. Determine required approvals
5. Assess risk level using safeguard-based criteria:
   - **Critical**: Rollback is untested OR production backup/recovery cannot be confirmed OR no staging environment available OR safeguards are absent/uncertain
   - **High**: Rollback plan exists and tested, backup available, batching and monitoring in place, but significant production impact expected
   - **Medium**: Rollback plan exists, limited production impact, staging available, safeguards confirmed
   - **Low**: Minimal production impact, well-tested rollback, staging environment available, all safeguards in place
   - Base the level on presence/absence of safeguards (backup, rollback, staging, monitoring) and blast radius, not just on change sensitivity.
6. **Architectural Decision Check**:
   - Does this change involve a choice between alternatives?
   - Does it establish a new pattern, API contract, or module boundary?
   - Does it have long-term architectural implications?
   - Does it affect security boundaries or data access rules?
   - Is there an Existing DEC that covers this decision?
7. **Doctrine_002 / Layer Responsibility Check** (mandatory):
   - Governing rule: [`KSS_Doctrine_002.md`](../docs/KSS_Doctrine_002.md); aid: [`LAYER_VIOLATION_CHECKLIST.md`](../docs/LAYER_VIOLATION_CHECKLIST.md)
   - Adopting project: `.cursor/CURSOR_RUNTIME.md` when present (project SSOT wins)
   - Any **Fail** → minimum **High** risk; flag for `/gw-review` before implementation

**Risk Triggers Requiring CAB Review**:
- Production-impacting changes
- Security-sensitive changes
- Data-affecting changes (schema, migrations, bulk updates)
- Access-control or permission changes
- High-risk or irreversible changes
- Cross-module or system-wide changes

**Output Format**:
```
## Risk Assessment

**Risk Categories Identified**:
- [ ] Security
- [ ] Compliance
- [ ] Production
- [ ] Data

**Risk Level**: Low / Medium / High / Critical
**Risk Level Rationale**: [Brief explanation based on safeguards (backup/rollback/staging), blast radius, and reversibility]

**Assumptions** (if any):
- [What was inferred vs explicitly stated by the user]
- [Any assumptions about scope, environment, or impact]

**CAB Review Required**: Yes / No
**Reason**: [why CAB is or isn't required]

**Production Approval Required**: Yes / No

**Identified Risks**:
1. [Risk description] - Severity: [Low/Medium/High]
2. [Risk description] - Severity: [Low/Medium/High]

**Mitigations**:
1. [Mitigation for risk 1]
2. [Mitigation for risk 2]

**Rollback Plan**:
- [How to revert if something goes wrong]

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
- [ ] Project `docs/` not used as execution SSOT: Pass / Fail / N/A
- [ ] Reports over DEC: Pass / Fail / N/A
- [ ] Ungated runtime install: Pass / Fail / N/A
- **Any Fail** → Risk level minimum **High**; CAB likely required
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
