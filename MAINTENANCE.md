# MAINTENANCE.md

Internal guide for maintaining the doc-based skills in this repo. End users do not need to read this — the skills work as-is once installed.

## Doc-based skills in this repo

The following skills under `skills/` track upstream documentation in [`tidev/titanium-docs`](https://github.com/tidev/titanium-docs):

| Skill | Upstream subtree |
|---|---|
| `ti-api` | `docs/api/` |
| `ti-guides` | `docs/guide/Titanium_SDK/Titanium_SDK_Guide` |
| `ti-howtos` | `docs/guide/Titanium_SDK/Titanium_SDK_How-tos` |
| `alloy-guides` | `docs/guide/Alloy_Framework/Alloy_Guide` |
| `alloy-howtos` | `docs/guide/Alloy_Framework/Alloy_How-tos` |

When upstream docs change, the reference files inside each skill should be re-aligned. This is the workflow.

## When to audit

Audits run on demand, not on a schedule. The trigger is upstream change:

- A new release or notable commit lands in `tidev/titanium-docs`.
- You notice drift between a reference and current SDK behavior.
- A user reports that a skill's content is stale or incorrect.

**Audit only the skill whose upstream subtree changed.** The mapping is in the table above. If the upstream change is under `docs/guide/Alloy_Framework/Alloy_Guide/`, only audit `alloy-guides` — not the other three. Running unnecessary audits wastes time and risks introducing churn from unrelated diffs.

If you're not sure which skill maps to an upstream change, run `git log -- <path>` inside `.titanium-docs/` and cross-reference the table above.

## Audit workflow (Claude Code)

A maintenance-only skill ships at `.claude/skills/ti-skill-audit/`. It is auto-discovered by Claude Code when working inside this repo and is not part of the public `skills/` set.

The audit is a 7-phase process with a hard-stop approval gate. See `.claude/skills/ti-skill-audit/references/audit-workflow.md` for the full spec.

### One-time setup

The audit compares reference files against a local clone of `tidev/titanium-docs` at `.titanium-docs/` in the repo root. The folder is gitignored.

```bash
git clone --depth 1 https://github.com/tidev/titanium-docs.git .titanium-docs
```

### Running an audit

In Claude Code, invoke:

```
/ti-skill-audit alloy-howtos
```

The auditor will:

1. Verify `.titanium-docs/` exists; if missing, prompt you to clone.
2. Optionally `git pull --ff-only` to refresh the cache.
3. Note the upstream commit hash for the audit report.
4. Run coverage analysis (Phase 1) and gap identification (Phase 2).
5. Generate a consolidated report (Phase 3).
6. **Stop** and wait for your explicit approval (Phase 4).
7. Apply the approved updates and verify (Phases 5–6).

### Refreshing the docs cache

Before each audit:

```bash
cd .titanium-docs && git pull --ff-only && cd ..
```

### Committing audit results

One commit per audited skill, e.g.:

```
audit(alloy-howtos): align refs with titanium-docs@a3f2b1c
```

Mention the upstream commit hash from Phase 0 in the commit body or PR description.

## Protected sections

Each reference file may contain a `## Community-Discovered Patterns` (H2) section. These sections capture verified real-world patterns that are intentionally **not** in the official docs. **The auditor will never delete them** during a sync.

If an upstream release adds official coverage for a pattern, the auditor will move that content into the main section and cite the source. If SDK behavior changed, it will update the pattern. An outdated workaround is just as bad as a hallucinated one. The H2 protects the section, but doesn't freeze it.

## AUDIT-SKIP files

Some reference files are sourced from elsewhere and should not be compared against `tidev/titanium-docs` at all. They carry an HTML comment near the top:

```html
<!-- AUDIT-SKIP: ... reason ... -->
```

The auditor reads that marker in Phase 0 and excludes the file from every later phase. Current example: `skills/alloy-guides/references/PURGETSS.md`, which is maintained against the PurgeTSS toolkit's own docs at [purgetss.com](https://purgetss.com), not against the Alloy guide upstream. To update one of these files, edit it manually.

## Maintaining without Claude Code

The `ti-skill-audit` skill is Claude-Code-specific (it lives under `.claude/skills/`). Maintainers using other agents can:

1. Read `.claude/skills/ti-skill-audit/references/audit-workflow.md` as a manual checklist.
2. Apply the same workflow by hand, comparing files in `.titanium-docs/` against the corresponding `references/` folder.

The methodology stays the same regardless of agent: coverage analysis, gap identification, hard-stop approval, preserve community patterns, respect AUDIT-SKIP markers.

## File structure conventions

Every doc-based skill follows:

```
skills/<skill-name>/
  SKILL.md                # Entry point (~200–500 lines)
  references/
    TOPIC_ONE.md          # Deep reference (~200–800 lines each)
    TOPIC_TWO.md
    ...
```

### Frontmatter rules

Per [`AGENTS.md`](AGENTS.md) and the [agentskills.io specification](https://agentskills.io/specification):

- Only `name` and `description` are guaranteed to be portable across agents.
- `description` must start with `Use when…` and describe trigger conditions, not workflow.
- Total frontmatter ≤ 1024 characters.
- Skills are agent-neutral — no Claude-specific fields like `argument-hint` or `allowed-tools` in `skills/`.

The `.claude/skills/ti-skill-audit/` skill is exempt from cross-agent rules because it lives under `.claude/skills/` (Claude Code-only by location).

## Related references

- Agentskills.io spec: <https://agentskills.io/specification>
- TiDev (current Titanium maintainer): <https://github.com/tidev>
- Titanium docs source: <https://github.com/tidev/titanium-docs>
- Titanium SDK: <https://github.com/tidev/titanium-sdk>
