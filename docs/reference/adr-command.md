# Recording Decisions — `/dec` (and the deprecated `/adr` alias)

**Audience:** KSS adopters.

KSS records architectural and governance decisions as **DEC digests**: short, agent-facing decision records stored in the project's decisions folder (`.cursor/memory/decisions/` by default, per `DOC_POLICY` → `decisions`). DEC digests are canonical decision records; ADR documents are historical provenance only and are not execution authority.

**Full specs:** [`commands/dec.md`](../../commands/dec.md) and [`agents/dec-steward.md`](../../agents/dec-steward.md).

---

## When to use `/dec`

- A discussion (chat, meeting notes, review thread) has reached an architectural or governance decision that future sessions must respect.
- `/preplan` or `/gw-review` reports a missing decision.

Not every choice needs a DEC. Routine implementation details belong in the plan or the code.

## Before the first `/dec`

The dec-steward agent writes DEC digests using the project's own template at `.cursor/memory/decisions/README.md`. KSS does not ship this template; create it with the fields your project wants (for example context, options, decision, consequences, status, owner).

## How it works

1. Run `/dec` and paste the source text (optionally a title or owner).
2. The **dec-steward** agent extracts the decision using your template.
3. It creates or updates `.cursor/memory/decisions/DEC-NNNN-slug.md`.
4. If the DEC status is **Active**, it also outputs an **Enforcement Sync Proposal** — the constraints that should be mirrored into `PROJECT_MANIFEST.md` or `INTEGRITY_RULES.md`. The proposal is **not applied automatically**; the Owner approves any enforcement change.

## What `/dec` must not do

- Modify `PROJECT_MANIFEST.md`, `INTEGRITY_RULES.md`, `STATE.yaml` or `VERSIONS.md`
- Write to `docs/architecture/ADR/` or create `ADR-*.md` files
- Change execution logic without Owner approval

## How DEC fits the authority model

Reading order: DOC_POLICY → PROJECT_MANIFEST → DEC → INTEGRITY_RULES → STATE → HANDOFF → GOALS. MANIFEST holds locks, DEC holds canonical decisions, INTEGRITY_RULES holds verifiable guardrails. The reading order does not establish precedence when sources conflict: when they disagree, the agent stops and reports the conflict for an Owner decision. See the [Handbook](../handbook.md#authority-order).

## `/adr` — deprecated alias

`/adr` remains only for backward compatibility. It forwards to `/dec` and the dec-steward agent and produces DEC digests — never ADR files. Use `/dec` for all new decisions.

Historical ADRs (for example, `Original path: docs/architecture/ADR/...` lines inside migrated DEC files) are kept as provenance only. See [MIGRATION_v1.0.0_to_v1.1.0.md](../migrations/MIGRATION_v1.0.0_to_v1.1.0.md) and [KSS_EVOLUTION.md](../KSS_EVOLUTION.md) (EVOL-004, EVOL-007).
