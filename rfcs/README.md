# SoyaOS RFCs

Substantive cross-cutting changes go through an RFC before landing as code.

## When you need an RFC

Open an RFC when the change:

- **Crosses repos** — touches both `soyaos/soyaos` and `soyaos/protos` (or any SDK).
- **Changes a spec** — anything under `specs/` or `schemas/`.
- **Changes a Design Decision** — adds a new `DD-NNN` or alters an Accepted one.
- **Changes the public CLI surface** — new top-level command, breaking flag rename.
- **Changes the OCI mediaType or bundle layout.**

Bug fixes, internal refactors, and additive Stage 2-5 features that fit cleanly inside an existing module are fine as plain PRs — no RFC needed.

## Workflow

1. Copy [`0000-template.md`](./0000-template.md) to `<NNNN>-<short-slug>.md` where `NNNN` is the next free number.
2. Fill in the template. Be specific about *why now*, *what we're not doing*, and *who would be affected*.
3. Open a PR adding the file. Title: `RFC NNNN: <one-line>`. Body links design decisions and spec versions.
4. Discussion happens in the PR. The maintainer team needs ≥ 2 approvals to merge; objections from anyone with `@soyaos/core-maintainers` block.
5. On merge the RFC becomes `accepted`. Implementation PRs in downstream repos cite the RFC number.

## Statuses

| Status | Meaning |
|---|---|
| `draft` | Open PR, discussion ongoing. |
| `accepted` | Merged. Implementation may begin. |
| `superseded by NNNN` | A later RFC replaces this one. |
| `withdrawn` | Author or team decided not to proceed. Kept for context. |
| `final` | Implemented and shipped. RFC is now a historical record. |

## Locking conventions

When an RFC is accepted, the spec it lands under bumps its `vN` only if the change is breaking. Otherwise the spec file gets a section called `## RFC NNNN — <topic>` describing the addition.

## Index

The directory listing is the index. RFCs are not reordered — read in numeric order.
