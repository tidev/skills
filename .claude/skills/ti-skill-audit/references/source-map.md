# Reference skill sources

## Maintained copies

| Official directory | Maintained source |
| --- | --- |
| `skills/ti-api/` | `macCesar/titools:skills/ti-api/` |
| `skills/ti-guides/` | `macCesar/titools:skills/ti-guides/` |
| `skills/ti-howtos/` | `macCesar/titools:skills/ti-howtos/` |
| `skills/alloy-guides/` | `macCesar/titools:skills/alloy-guides/` |
| `skills/alloy-howtos/` | `macCesar/titools:skills/alloy-howtos/` |

Copy from a reviewed Git commit, not an unidentified working tree. Include every versioned supporting file and remove paths retired by the source revision after reviewing their purpose. Do not copy ignored audit evidence, caches or operating-system files.

## Documentation authority

- Core API: `tidev/titanium-sdk/apidoc/` at the skill's declared GA tag. SDK 14.0.0.GA is `14_0_0_GA` at `269fc733a813ea4ef0b27a36b34a63e8a5107aa0`.
- Release cross-check: `tidev/titaniumsdk.com/registry/sdk/<version>/contents.json`, then its referenced `_pool/` files. Pool membership alone does not establish the release.
- Current guides: `tidev/titaniumsdk.com/content/docs/`; read rendered Markdown when a source includes generated tables.
- Alloy compiler: the released Alloy version used by the guide, independently of the SDK version.
- Native module APIs: each module's own release and documentation.
- PurgeTSS: its own documentation and released package. `AUDIT-SKIP` references remain independently sourced while being copied from TiTools.

The archived `tidev/titanium-docs` tree is historical provenance only. The reference files themselves route topics to their current sources; do not retain an old per-file map after the maintained source replaces or splits those files.
