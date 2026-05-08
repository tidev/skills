# titanium-skills

Reusable agent skills for working with the [**Titanium SDK**](https://titaniumsdk.com) — native modules, apps, hooks, build tooling.

Skills here follow the open [agent skills specification](https://agentskills.io/specification) and work with any compatible coding agent — **Claude Code**, **Gemini CLI**, **Codex / OpenAI Codex CLI**, **GitHub Copilot CLI**, and other LLM-driven assistants that load skills from a local directory.

## Skills

| Skill | What it does |
| --- | --- |
| [`ti-module-update`](./skills/ti-module-update/SKILL.md) | Update a Titanium native module's third-party dependency (`.xcframework` in `ios/platform`, `ios/spm.json`, or `android/build.gradle`) and the Titanium SDK itself, verify with `ti build --build-only`, document breaking changes in the README, and open a PR with the last two active maintainers as reviewers. |

More skills will land here over time.

## Usage

Install all skills from this repo into your agent's skills directory in one command:

```bash
npx skills add tidev/skills
```

That registers every skill under `skills/` (currently `ti-module-update`, more to come) for your active agent. Once installed, the agent discovers each skill by name — invoke it with the matching slash command, e.g.:

```
/ti-module-update
```

To install a single skill, append its folder name:

```bash
npx skills add tidev/skills/ti-module-update
```

### Supported agents

`npx skills add` installs into the right location for whichever agent it detects. Verified targets:

| Agent | Install location |
| --- | --- |
| Claude Code | `~/.claude/skills/<name>/` |
| Codex / OpenAI Codex CLI | `~/.agents/skills/<name>/` |
| Gemini CLI | activated via Gemini's skill mechanism |
| GitHub Copilot CLI | auto-discovered from the plugin's skills dir |
| Other agents implementing [agentskills.io](https://agentskills.io/specification) | the agent's local skills directory |

### Manual install (no npx)

Each skill is just `skills/<name>/SKILL.md` with standard frontmatter, so a clone + symlink works for any agent:

```bash
git clone https://github.com/tidev/skills.git ~/src/tidev-skills

# Claude Code
ln -s ~/src/tidev-skills/skills/ti-module-update ~/.claude/skills/ti-module-update

# Codex
ln -s ~/src/tidev-skills/skills/ti-module-update ~/.agents/skills/ti-module-update
```

## Contributing a skill

1. Add `skills/<skill-name>/SKILL.md` with `name` and `description` in the YAML frontmatter.
2. Add a row to the table above with a one-line description.
3. Read [`AGENTS.md`](./AGENTS.md) for conventions.

## License

MIT — see [LICENSE](./LICENSE) once added.
