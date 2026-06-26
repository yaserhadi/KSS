# Triage

**Purpose**: Ask clarifying questions only if necessary, otherwise propose the safest default plan.

**Related Commands**: `/gw-riskcheck`, `/gw-handoff`, `/gw-review`

---

**Workflow**:
1. Analyze the user's request for clarity and completeness
2. If the request implies production impact or destructive actions, do not propose a plan; defer to `/gw-riskcheck`
3. If request is clear and unambiguous (and low-risk):
   - Propose the safest, most conservative plan immediately
   - Prefer reversible over irreversible actions
   - Prefer additive over destructive changes
4. If request is ambiguous or missing critical context:
   - Ask 1-2 critical clarifying questions only
   - Do not ask obvious or unnecessary questions
   - Focus on: scope, environment, constraints, expected outcome
5. **Project boundary rule (if present)**:
   - For any feature or structural change (not pure docs/read-only), check whether the project defines a placement/boundary rule under `.cursor/rules/` (e.g. module vs kernel vs new package).
   - If such a rule exists, follow it and cite it in the triage output. If none exists, triage scope and destination at a high level only.
   - **Triaged first, confirmed at plan start** — if scope grows into a full feature surface, re-run this check.
6. **Architectural Decision Check**:
   - Does this change involve a choice between alternatives?
   - Does it establish a new pattern, API contract, or module boundary?
   - Does it have long-term architectural implications?
   - Does it affect security boundaries or data access rules?
   - Is there an Existing DEC that covers this decision?
7. **Doctrine_002 / Layer Responsibility Check** (when proposing destination):
   - Governing rule: [`KSS_Doctrine_002.md`](../docs/KSS_Doctrine_002.md); aid: [`LAYER_VIOLATION_CHECKLIST.md`](../docs/LAYER_VIOLATION_CHECKLIST.md)
   - Adopting project: `.cursor/CURSOR_RUNTIME.md` when present (project SSOT wins)
   - Cite in triage output when placement touches L1/L2/L3 boundaries

**Output Format**:
```
## Triage Result

**Request clarity**: Clear / Needs clarification

**Questions** (if any):
1. [Critical question]
2. [Critical question]

**Proposed Plan** (if clear):
- Approach: [safest default approach]
- Scope: [what will be changed]
- Risk level: Low / Medium / High
- Reversible: Yes / No

**Evidence Referenced**:
- [files/commands/tests that support the conclusions]

**Project boundary rule (if applicable)**:
- **Rule**: [path under .cursor/rules/ or N/A]
- **Destination**: [per project rule — e.g. existing module / kernel / new module]
- **Rationale**: [one or two sentences]

**Architectural Decision Check**:
- [ ] Involves architectural choice: Yes / No
- [ ] Decision type: [DB/API/Security/Module/Integration/None]
- [ ] Existing DEC covers this: [DEC-NNNN] / None found / N/A
- [ ] DEC recommended: Yes / No

**If DEC recommended**:
> Run `/adr` (DEC digest) before proceeding. Paste the discussion context.
> Decision indicators detected: [list what triggered this]

**Doctrine_002 / Layer Responsibility Check**:
- Governing rule: [`KSS_Doctrine_002.md`](../docs/KSS_Doctrine_002.md)
- Checklist: [`LAYER_VIOLATION_CHECKLIST.md`](../docs/LAYER_VIOLATION_CHECKLIST.md)
- [ ] L2 defines authority: Pass / Fail / N/A
- [ ] L3 overrides L1: Pass / Fail / N/A
- [ ] Project `docs/` not used as execution SSOT: Pass / Fail / N/A
- [ ] Reports over DEC: Pass / Fail / N/A
- [ ] Ungated runtime install: Pass / Fail / N/A
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
- Project-specific boundary rules live in the adopting repo's `.cursor/rules/`, not in the KSS package
- When in doubt, prefer safety over speed
- Always cite evidence for findings
- Stop and ask if context is missing
