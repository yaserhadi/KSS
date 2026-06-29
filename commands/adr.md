# /adr — Deprecated alias

**Deprecated.** This command forwards to **`/dec`**.

Use `/dec` for all new decision digests. ADR is retired from the execution model in DEC-adopting projects.

## Forwarding

When invoked, execute the `/dec` workflow exactly:

1. Read `~/.cursor/commands/dec.md` (installed from KSS `commands/dec.md`).
2. Invoke **dec-steward** (not independent ADR logic).
3. Write output to `.cursor/memory/decisions/DEC-NNNN-slug.md` only.

Do not create ADR files. Do not write to `docs/architecture/ADR/`.
