# AGENTS.md

Guidance for AI agents — **Claude Code, Gemini CLI, Codex / OpenAI Codex CLI, GitHub Copilot CLI, and any other LLM-driven coding assistant** — working **inside** this repository. If you are an agent invoked by a user in a Titanium project to *use* a skill, read the relevant `skills/<name>/SKILL.md` directly and follow it.

## What this repo is

A flat collection of reusable, agent-agnostic skills for [**Titanium SDK**](https://titaniumsdk.com) workflows: native modules, apps, hooks, build tooling, packaging, releases.

Skills here conform to the open [agentskills.io specification](https://agentskills.io/specification), so any compatible agent can load them. Every skill is a self-contained folder under `skills/` with a single `SKILL.md` (plus optional supporting files). No build step, no source code — just markdown.

## Layout

```
.
├── README.md           # human-facing index of skills
├── AGENTS.md           # this file
└── skills/
    └── <skill-name>/
        ├── SKILL.md    # required: skill definition
        └── ...         # optional: scripts, references, templates
```

- One skill per folder.
- The folder name **must** equal the `name:` field in the skill's YAML frontmatter (kebab-case, letters/digits/hyphens only — no slashes, no spaces).

## Skill format

Every `SKILL.md` starts with YAML frontmatter:

```markdown
---
name: <kebab-case-name>
description: Use when <triggering conditions, symptoms, file markers>. Keep ~500 chars or less.
---

# <Skill title>

## Overview
What it is. Core principle in 1-2 sentences.

## When to Use
Bullet triggers + when NOT to use.

## Workflow / Steps
...
```

Rules:
- `description` describes **when to use** the skill — concrete triggers, error messages, file markers — *not* a summary of the workflow. Future agents read the description to decide whether to load the full skill; if you summarize the workflow there, agents may follow the summary instead of the skill.
- Frontmatter total ≤ 1024 chars. See [agentskills.io/specification](https://agentskills.io/specification).
- Write in third person. Start the description with `Use when…`.

## Design principles for skills in this repo

- **Concrete file paths and commands.** Skills target Titanium repos and should reference real files (`ios/manifest`, `android/manifest`, `android/build.gradle`, `ios/titanium.xcconfig`, `ios/spm.json`, `tiapp.xml`, `timodule.xml`) and real CLI invocations (`ti build`, `ti sdk install`, `ti sdk select`).
- **Vendor-neutral.** Skills should apply to Titanium modules generally — don't hard-code one specific SDK (Facebook, Stripe, Clerk, …) in examples or triggers. Use `<groupId>:<artifactId>:<version>` style placeholders.
- **Verify before claiming success.** A green build / test run is the proof. Skills should not let agents claim "done" on assertion alone.
- **Define explicit hand-back conditions.** State when the agent should stop and ask the human (e.g. "after 2-3 failed debug attempts", "when an SDK rename requires a `module.xcconfig` change you can't verify").
- **No backwards-compat noise.** If a skill changes, change it; don't accumulate "deprecated since v…" sections.

## Common operations for agents in this repo

### Add a new skill

1. Pick a kebab-case `<skill-name>` (verb-led where possible: `ti-module-update`, `ti-app-hook-add`).
2. Create `skills/<skill-name>/SKILL.md` with frontmatter and content.
3. Add a row to the **Skills** table in `README.md` with a one-line description.
4. Commit on a feature branch and open a PR.

### Edit an existing skill

1. Make minimal targeted edits — preserve the frontmatter contract.
2. If `description` changes meaning, update the `README.md` row to match.
3. Don't fork content into a sibling file unless the inline content > ~150 lines or it's a reusable script/template.

### Don't

- Don't write skill content into the README. The README only lists and one-line-describes.
- Don't introduce a `package.json`, build pipeline, or generated docs unless explicitly requested.
- Don't reference private repos, internal URLs, or vendor-specific examples that pin a skill to one SDK.

## Resources

- Skill spec (cross-agent): <https://agentskills.io/specification>
- Claude Code skills: <https://docs.claude.com/en/docs/claude-code/skills>
- Codex CLI: <https://github.com/openai/codex>
- Gemini CLI: <https://github.com/google-gemini/gemini-cli>
- GitHub Copilot CLI: <https://docs.github.com/en/copilot/github-copilot-in-the-cli>
- Titanium SDK: <https://titaniumsdk.com> · source: <https://github.com/tidev/titanium-sdk>
