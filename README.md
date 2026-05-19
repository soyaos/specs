# soyaos/specs

**Source of truth** for SoyaOS contracts that span multiple implementation repos.

Code in [`soyaos/soyaos`](https://github.com/soyaos/soyaos), SDK code in `soyaos/sdk-*`, the Forge in `soyaos/soyaforge`, and channel adapters in `soyaos/connectors` all **conform to** the documents here. When code and spec disagree, **the spec wins** — fixes go to whichever side actually has the bug.

## Contents

```
specs/
├── soyapack/v0/         # SoyaPack manifest, capability declarations
├── bundle/v0.md         # OCI mediaType + .spk archive layout
└── cli/v0.md            # Canonical `soyaos` CLI verbs + flags

schemas/
└── soyapack/v0/         # JSON Schema for SoyaPack manifest — IDE & CI consumable

rfcs/
├── README.md            # How the SoyaOS RFC process works
└── 0000-template.md     # Copy this when proposing a change
```

## Version policy

- Each spec lives under a `vN/` directory. Breaking changes bump `N` and live in `v(N+1)/` — the old version stays readable for migration.
- The current pre-release is **`v0`**. It is **unstable**; expect fields to be added and renamed before `v1` ships alongside SoyaOS v0.1.0 GA.

## Conformance

Every implementation that claims to implement a spec **must** declare which version it implements (e.g. `spec_version: soyapack.v0` in a manifest, `soyaos --spec-version` returning `cli.v0`). Code that drifts from the declared version is a bug.

## Quick links

- [SoyaPack v0 overview](specs/soyapack/v0/README.md)
- [Manifest field reference](specs/soyapack/v0/manifest.md)
- [Capability vocabulary](specs/soyapack/v0/capabilities.md)
- [Bundle layout & OCI mediaType](specs/bundle/v0.md)
- [CLI v0 surface](specs/cli/v0.md)
- [JSON Schema](schemas/soyapack/v0/manifest.schema.json)
- [RFC process](rfcs/README.md)

## License

[MIT](LICENSE) — Copyright (c) 2026 SoyaOS Contributors.
