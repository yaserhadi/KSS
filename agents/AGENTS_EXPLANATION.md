# Agents — Copy Instructions

**Destination:** `~/.cursor/agents/`  
**Source of truth:** `KSS/agents/` (this repository)

| File | Purpose | When Invoked |
|------|---------|--------------|
| dec-steward.md | Extract DEC digests from pasted text | Via `/dec` command |
| adr-steward.md | Deprecated alias → dec-steward | Via `/adr` (forwards to `/dec`) |
| ai-knowledge-steward.md | Maintain `.cursor/memory/` and `.cursor/reports/` | Via /docpack (governance track) |
| user-doc-steward.md | Human docs at DOC_POLICY `human_docs` only | Via /docpack — skip if path undefined |
