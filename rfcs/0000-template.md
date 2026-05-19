# RFC NNNN — <one-line title>

- **Author(s)**: <name(s)>
- **Date**: YYYY-MM-DD
- **Status**: draft | accepted | superseded by NNNN | withdrawn | final
- **Targets**: `specs/...` / `schemas/...` / `DD-...` / code repos affected

## Summary

One paragraph. What is changing, and what is the user-visible result.

## Motivation

Why now? What concrete problem does this solve that the current design doesn't? Cite a use case (ideally one of the four v0.1.0 flagships: Compo / NewsBeam / EstateMuse / SilentCut). Cite prior decisions (`DD-NNN`).

## Detailed design

Be specific. Field names, schema diffs, protocol changes, file layouts. If you'd write code in a PR, write it here in a fenced block.

If the change touches a spec under `specs/`, paste the new section verbatim.

## What we're explicitly not doing

Bullet list. The fastest way to lose an RFC is to leave its scope ambiguous.

## Alternatives considered

For each, one paragraph: what it was, why it's worse than the chosen design.

## Migration

If accepted, who has to do what, in what order, before this is safe to merge into:

- `soyaos/soyaos`
- `soyaos/protos`
- SDK repos
- Pack authors in the wild

If there is no migration cost (purely additive with a default), say so explicitly.

## Backward compatibility

What breaks. What's deprecated. How long the old form is honored. When the old form is removed.

## Security / privacy considerations

Capability changes? New trust boundary? Anything reaching outside the sandbox? If "none", say "none" — don't omit the section.

## Open questions

Unresolved decisions, ranked. Each must be answered before the RFC can transition to `accepted`.

## Acceptance criteria

Concrete tests / behaviors that demonstrate the RFC was correctly implemented. The implementation PR cites these.

## References

- Design Decisions affected
- Prior RFCs
- External material
