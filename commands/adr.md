# /adr — Decision Digest (DEC) from source text

## Input (required)
Paste the source text (discussion, Cursor chat, meeting notes, etc.).
Optional: title/slug suggestion, owner override.

## Purpose
Generate a **DEC digest** in:
- `.cursor/memory/decisions/DEC-NNNN-slug.md`

DEC is the agent-facing decision record. Legacy human ADR folders under `docs/` are not execution authority.

This command MUST NOT:
- Modify PROJECT_MANIFEST.md
- Modify INTEGRITY_RULES.md
- Modify STATE.yaml
- Modify VERSIONS.md
- Write to `docs/architecture/ADR/`
- Change any execution logic without owner approval

## Workflow
1) Ask the user to paste the source text (if not provided).
2) Invoke adr-steward with the pasted text.
3) adr-steward produces DEC content per `memory/decisions/README.md`.
4) Create or update `.cursor/memory/decisions/DEC-XXXX-slug.md`.
5) If Status = Active: output Enforcement Sync Proposal (not applied automatically).

## Output
- DEC file only + optional enforcement sync proposal (not applied).
