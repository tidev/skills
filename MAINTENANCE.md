# Maintaining the reference skills

The five documentation skills are synchronized from [TiTools](https://github.com/macCesar/titools), where their reference content is maintained and checked against official sources. Keep the two distributions available by copying one maintained source instead of editing two versions independently.

| Skill | Source directory in TiTools | Documentation authority |
| --- | --- | --- |
| `ti-api` | `skills/ti-api/` | Released `tidev/titanium-sdk` API definitions and the matching version registry |
| `ti-guides` | `skills/ti-guides/` | Current `tidev/titaniumsdk.com` guides and released SDK contracts |
| `ti-howtos` | `skills/ti-howtos/` | Current official native integration guides and released contracts |
| `alloy-guides` | `skills/alloy-guides/` | Current official Alloy guides and the released compiler |
| `alloy-howtos` | `skills/alloy-howtos/` | Current official Alloy CLI and configuration guides |

`ti-module-update` and `ti-create-release` are maintained here independently of this synchronization.

## Synchronize an update

1. Start from a clean contribution branch. Use an existing TiTools checkout or obtain a specific Git revision from its public repository.
2. Record the full TiTools commit in the pull request. Inspect the source changes and its declared SDK baseline before copying.
3. Replace the five complete directories above with their versioned counterparts from that revision, including `SKILL.md` and every supporting reference. Remove obsolete destination files that no longer exist in the source; an overlay copy alone can leave outdated guidance behind.
4. Exclude ignored files, caches, audit reports and operating-system metadata. A Git archive of the selected directories provides the versioned files without `.DS_Store` or private maintainer resources.
5. Compare the paths and bytes of the five directory trees against the selected source. Keep the copied content identical. If a correction is needed, propose it in TiTools and synchronize the reviewed result here.
6. Validate frontmatter, local references, Markdown anchors and examples affected by the update. Check that the two workflow skills are unchanged. Review every removed reference and explain its replacement or the reason it is no longer part of the source catalog.
7. Open a pull request containing the source commit, SDK baseline, relevant changes and validation results.

A synchronization copies content that has already been maintained upstream; it does not independently re-audit every API. A suspected contract error still needs a source check before it is forwarded or published.

## Maintainer command in TiTools

From a TiTools source checkout, inspect the target first:

```sh
npm run sync:tidev-skills -- --target /path/to/tidev-skills --check
```

Prepare a clean contribution branch in this repository. Use `--apply` for a copy without publication. For an authorized PR, the agent reviews the final title and description with `humanizer:humanizer` and saves the description to a file before running:

```sh
npm run sync:tidev-skills -- --target /path/to/tidev-skills --publish --title "docs: update Titanium and Alloy reference skills" --body-file /path/to/reviewed-pr.md
```

The command copies committed source files, commits the changed mirror paths and pushes to the contribution fork. It creates or updates the open PR for the branch; it does not merge it. Keep the two workflow skills outside that synchronization.

## Official sources and release boundaries

SDK 14.0.0.GA is tag `14_0_0_GA` in `tidev/titanium-sdk`. Match the skill's selected release to the corresponding `registry/sdk/<version>/` in `tidev/titaniumsdk.com`; the shared `_pool/` is not a release selector. Native modules have their own release versions.

Current guides come from `tidev/titaniumsdk.com/content/docs/`. The archived `tidev/titanium-docs` repository and the ignored `.titanium-docs/` cache are historical provenance, not the source for new guide updates.

`AUDIT-SKIP` markers protect independently sourced references, such as PurgeTSS, from an audit against the wrong documentation. They do not prevent copying the maintained version of that file from TiTools. Preserve declared source and version boundaries when reviewing those copies.

## Maintenance helper

The internal `.claude/skills/ti-skill-audit/` skill checks synchronization scope, source revisions and validation. It is not an end-user skill and is excluded from the public skill catalog. Maintainers using other agents can follow this document and its [workflow](.claude/skills/ti-skill-audit/references/audit-workflow.md) directly.
