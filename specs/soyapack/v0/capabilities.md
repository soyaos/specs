# SoyaPack v0 — `capabilities.yml` Reference

Capabilities are **the contract between the Pack and the Runtime**. Default is **deny-all**; whatever isn't listed is forbidden. The Comet executor refuses to run a Pack that attempts to exceed its declared capabilities.

The `capabilities` block can live inside `soyapack.yaml` (small Packs) or in a sibling `capabilities.yml` (larger Packs). Both are signed.

## Schema

```yaml
capabilities:
  network.out:                        # outbound network egress
    - host: api.openai.com
      port: 443
      proto: https                    # http | https | tcp | udp
      pin: <sha256-of-cert>           # optional cert pinning
      quota_per_call: 8192            # max bytes per request, optional

  fs.read:                            # filesystem read allowlist
    - /workdir
    - /etc/ssl/certs

  fs.write:                           # filesystem write allowlist
    - /workdir/out

  syscalls:                           # explicit syscall allowlist (Landlock / Seatbelt)
    - openat
    - read
    - write
    - close
    # … no exec, no fork, no socket unless declared

  llm:                                # LLM call quota
    model: any                        # or a specific model id
    temperature: { min: 0.0, max: 1.5 }
    max_tokens: 8192
    quota_per_call: 1                 # number of model calls per Pack invocation
    quota_per_day: 1000               # daily cap

  mcp.tools:                          # MCP tools this Pack may invoke
    - tool.parse_input
    - tool.rss_fetch

  memory:                             # mounted Memory Pack dependencies
    - name: estatemuse-state
      access: rw

  storage:                            # external storage targets
    nas:
      - kind: webdav                  # smb | nfs | webdav | s3
        mount: /mnt/nas
        access: rw

  secrets:                            # secret references (resolved by Runtime, never embedded)
    - DINGTALK_BOT_TOKEN
    - NAS_CRED_REF

  resources:                          # compute budget
    cpu: 2                            # vCPU
    ram_mb: 1024
    timeout_s: 60
    gpu: 0                            # GPU count (0 = none)
```

## Resolution rules

1. **Default deny.** A field omitted from `capabilities.yml` means the Pack is denied that capability entirely.
2. **Allowlist semantics.** Within a field, list members are unioned. No wildcards in v0 (explicit only).
3. **Quota is enforced per Pack invocation**, not per-second; bursting is allowed within a single call.
4. **Capability override is server-side only.** A deployment operator can *narrow* the declared capabilities at install time but never *widen* them.

## Error envelope

When a capability check fails at runtime the executor returns a typed error:

```json
{
  "error": {
    "type": "soyaos.capability_denied",
    "code": "network.out.host_not_allowed",
    "detail": "host 'evil.example.com' not in capabilities.network.out",
    "pack": "soyapack://<sha256>",
    "request_id": "..."
  }
}
```

Implementations should map this to `pkg/runtime.ErrDeniedByCapability` (see `soyaos/soyaos`).

## Mapping to the Go runtime

| YAML key | Go type |
|---|---|
| `capabilities.network.out` | `factory.SandboxDecl.Capabilities.NetworkOut []EgressRule` |
| `capabilities.fs.read` | `factory.SandboxDecl.Capabilities.FSRead []string` |
| `capabilities.fs.write` | `factory.SandboxDecl.Capabilities.FSWrite []string` |
| `capabilities.syscalls` | `factory.SandboxDecl.Capabilities.Syscalls []string` |
| `capabilities.llm.*` | `factory.SandboxDecl.Capabilities.LLM` |
| `capabilities.mcp.tools` | `factory.SandboxDecl.Capabilities.MCPTools []string` |
| `capabilities.memory` | `factory.SandboxDecl.Capabilities.Memory []MemoryMount` |
| `capabilities.storage.nas` | `factory.SandboxDecl.Capabilities.NAS []NASMount` |
| `capabilities.secrets` | `factory.SandboxDecl.Capabilities.Secrets []string` |
| `capabilities.resources.*` | `factory.SandboxDecl.Capabilities.Resources` |
