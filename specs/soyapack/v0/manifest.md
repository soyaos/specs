# SoyaPack v0 — `soyapack.yaml` Manifest Reference

The manifest is YAML. Field names are `snake_case`. Unknown top-level fields **must** be rejected by parsers; unknown fields nested under `x-` (RFC-7807-style extension prefix) are passthrough.

## Common (all KINDs)

| Field | Type | Required | Notes |
|---|---|---|---|
| `spec_version` | string | ✓ | Must be `"soyapack.v0"`. Parsers refuse other values. |
| `kind` | enum | ✓ | One of `Skill` / `Agent` / `Memory`. |
| `name` | string | ✓ | Slug. Lowercase, hyphens, 1–48 chars, must match `^[a-z][a-z0-9-]{0,46}[a-z0-9]$`. |
| `version` | string | ✓ | SemVer 2.0. Pre-1.0 packs use `0.x.y`. |
| `description` | string | ✓ | One paragraph, ≤ 280 chars. |
| `authors` | array of `{name, email?}` | ✓ | At least one entry. |
| `license` | SPDX id | ✓ | E.g. `"MIT"` / `"Apache-2.0"`. |
| `homepage` | URL | optional | Project page. |
| `runtime.compat` | SemVer range | ✓ | Compatible SoyaOS range, e.g. `">=0.1.0 <0.2.0"`. |
| `determinism` | enum | ✓ | `pure` / `read-only` / `stateful`. |
| `affinity` | enum | optional | `planet` / `moon` / `comet` / `any`. Default: `any`. |
| `deps.lockfile` | string | optional | sha256 hex of an in-archive lockfile. |
| `sbom` | string | optional | Filename relative to archive root; defaults to `sbom.spdx.json`. |
| `signatures` | array | server-injected | COSE signatures. Authors don't write this. |

## Agent-only

| Field | Type | Notes |
|---|---|---|
| `entry` | string | Path to entry prompt or handler script. |
| `expose.openai_compat` | enum | `chat` / `responses` / `both`. (DD-005) |
| `expose.virtual_model_id` | string | Must be of the form `soya:<slug>`. Defaults to `soya:{name}`. |
| `inputs` | array of `{name, type, optional?, items?}` | Input parameter schema. |
| `prompt.scaffold` | enum | `minimal-input-high-quality` / `chain-of-thought` / `…`. |
| `prompt.tools` | array of strings | Tool names the Agent may call. |
| `artifacts` | array of `{kind, schema}` | Output forms. `kind ∈ {html, pdf, long_image, markdown, xlsx, mp4}`. (Proposed DD-012) |
| `schedules` | array of schedule entries | See [Schedules](#schedules). (DD-007) |
| `channels` | array of `{kind, binding_template}` | See [Channels](#channels). (DD-006) |
| `actions` | array of action entries | See [Actions](#actions). (DD-010) |
| `state` | object | See [State](#state). (DD-010) |
| `sandbox` | object | See [Sandbox](#sandbox). (DD-011) |
| `uses` | array of `"name@version"` | Skill / Memory pin-exact references. |

### Schedules (DD-007)

```yaml
schedules:
  - cron: "0 9 * * *"      # or `once: "2026-06-01T09:00:00Z"`
    tz: "Asia/Shanghai"
    payload: { sources: default, max_items: 8 }
    idempotency_key: "newsbeam:{date}"
    missed_fire: skip       # skip | once | backfill
```

### Channels (DD-006)

```yaml
channels:
  - kind: dingtalk          # dingtalk | feishu | wework | wechat-mp | wechat-cs | webhook
    binding_template: "<bound at deploy time>"
```

### Actions (DD-010)

```yaml
actions:
  - id: generate_post
    on: per_row             # per_row | button | api
    handler: prompts/generate_post.md
    timeout: 60s
    artifacts: [wechat_post.v1]
```

### State (DD-010)

```yaml
state:
  scope: agent              # agent | user | tenant
  store: kv                 # memory | kv | db
```

### Sandbox (DD-011)

```yaml
sandbox:
  isolation: container      # process | container | microvm
  image: video-base@0.1.0
  budget_seconds_max: 300
  cold_start_target_ms: 10000
  capabilities:
    network.out:
      - host: api.openai.com
        port: 443
        proto: https
    fs.read:
      - /workdir
    fs.write:
      - /workdir/out
    determinism_tier: read-only
```

## Skill-only

| Field | Type | Notes |
|---|---|---|
| `entry` | string | Path to `SKILL.md` (progressive disclosure: metadata ≤ 100 tok, body ≤ 5000 tok, scripts on-demand). |
| `capabilities` | (see capabilities.md) | Strict allowlist. Default deny. |
| `inputs`, `outputs` | JSON Schema | Required. |

## Memory-only

| Field | Type | Notes |
|---|---|---|
| `mount.partition` | string | Logical partition name. |
| `mount.access` | enum | `ro` / `rw`. |
| `mount.format` | enum | `embeddings` / `kv` / `json-lines`. |
| `mount.embedding.model` | string | E.g. `"text-embedding-3-large"`. |
| `mount.embedding.fingerprint` | sha256 | Pin against drift. |
| `mount.embedding.dim` | int | Vector dimension. |
| `contents` | array of `{path, sha256, size}` | Content-addressed manifest of data files. |

## Canonicalization

The signature in `signature.cose` is over **canonical JSON** of the YAML manifest:
- Keys sorted lexicographically at every level
- No comments
- No trailing whitespace
- LF line endings
- UTF-8 NFC normalization for string values

Implementations should treat parse-and-recanonicalize as the verification step.

## Compatibility & drift

Field additions in `v0.*` are non-breaking only if they have a default. Field removals or shape changes require a bump to `v1`.

## See also

- [Capabilities](./capabilities.md) — full capability vocabulary
- [Bundle layout](../../bundle/v0.md) — OCI mediaType + tar.zst archive
- [JSON Schema](../../../schemas/soyapack/v0/manifest.schema.json)
