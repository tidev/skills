---
name: ti-skill-audit
description: 'Use when checking or synchronizing ti-api, ti-guides, ti-howtos, alloy-guides or alloy-howtos from TiTools into this repository, after an upstream reference update or when a copied API contract appears wrong. Maintenance-only; not for end-user Titanium projects.'
metadata:
  internal: true
---

# Reference skill maintenance

Maintain the five reference skills as copies of a reviewed TiTools revision. Read [MAINTENANCE.md](../../../MAINTENANCE.md), the [source map](references/source-map.md), and the [workflow](references/audit-workflow.md) before synchronizing. Use the [quality standards](references/quality-standards.md) when checking a suspected content error.

## Scope

- `ti-api`
- `ti-guides`
- `ti-howtos`
- `alloy-guides`
- `alloy-howtos`

The workflow skills `ti-module-update` and `ti-create-release` are outside this synchronization.

## Invocation

A named skill selects one of the five directories. With no name, use the requested scope from the conversation; ask only if the selection remains unclear.

Record the source commit, compare complete directory trees, review obsolete destination files and verify the copied content. An update request authorizes its in-scope synchronization; a read-only audit ends with findings. Resolve an undecidable scope or permission boundary before applying changes.

Keep source corrections in TiTools so that later synchronization does not overwrite a separately maintained local variant. Do not use the archived documentation cache as a current source. Preserve release-specific API contracts and independently versioned module or PurgeTSS guidance.
