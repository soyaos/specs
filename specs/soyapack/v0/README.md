# SoyaPack v0 — Overview

> A SoyaPack is the **atomic delivery unit** for anything that runs on SoyaOS: an Agent, a Skill, or a Memory Pack. A Pack is a signed, content-addressed bundle whose contents are described by a `soyapack.yaml` manifest.

This directory is the canonical spec. It is normative; the Go types in [`soyaos/soyaos/pkg/soyapack`](https://github.com/soyaos/soyaos/tree/main/pkg/soyapack) implement it.

## Three KINDs

| Kind | What it is | Typical contents |
|---|---|---|
| `Skill` | A pure-or-read-only capability that other Agents call | prompts/, tools/, examples/ |
| `Agent` | An invocable assistant that exposes a chat surface | manifest with `expose.openai_compat`, prompts/, templates/, tools/ |
| `Memory` | A pre-built knowledge / state bundle other Packs can mount | contents/, embeddings.bin, manifest with `mount` section |

A single SoyaPack archive carries exactly one Kind.

## Mandatory pieces of a Pack

```
my-pack/                          # directory or .tar.zst archive
├── soyapack.yaml                 # manifest (REQUIRED)
├── capabilities.yml              # capability declarations, see capabilities.md
├── sbom.spdx.json                # SPDX 2.3 software bill of materials
├── signature.cose                # COSE_Sign1 over the canonicalized manifest+contents
├── runtime-hints.yml             # optional: image/profile/budget hints for Comet
├── prompts/                      # Agent prompts, *.md
├── templates/                    # output templates (HTML / xlsx / etc.)
├── tools/                        # tool descriptors (MCP-style JSON)
├── contents/                     # data files (Memory kind)
└── examples/                     # reproduction inputs + expected outputs
```

See [`manifest.md`](./manifest.md) for the manifest schema and [`../bundle/v0.md`](../bundle/v0.md) for the archive format.

## Canonical OCI mediaType

```
application/vnd.soyaos.pack.v1+json
```

(The `v1` lives in the mediaType because, once we sign and distribute Packs through OCI registries, changing the mediaType is the breaking-change escape hatch. The spec **version** is `v0` because the *content* under that mediaType is still pre-stable.)

## Determinism axis

Every Pack declares one of three `determinism` levels:

- `pure` — same inputs → byte-identical outputs. Safe to memoize. No clock, no network, no fs writes.
- `read-only` — may read from declared resources but does not modify state. Safe to retry.
- `stateful` — modifies external state. Idempotency keys required for safe retry.

The Runtime side rejects executions that exceed the declared level.

## Files

- [`manifest.md`](./manifest.md) — the `soyapack.yaml` field reference
- [`capabilities.md`](./capabilities.md) — the `capabilities.yml` field reference (network.out, fs, llm, secrets, …)
- [`../../bundle/v0.md`](../../bundle/v0.md) — OCI mediaType and archive layout
- [`../../../schemas/soyapack/v0/manifest.schema.json`](../../../schemas/soyapack/v0/manifest.schema.json) — JSON Schema (IDE / CI)
