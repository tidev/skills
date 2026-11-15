# Reference synchronization workflow

## 1. Identify the request and source

Read [MAINTENANCE.md](../../../../MAINTENANCE.md) and [source-map.md](source-map.md). Select the requested directories from the five reference skills. Record the target branch, its existing changes and the full TiTools source revision. Use a clean branch or isolate existing work before replacing files.

## 2. Compare complete trees

Inventory source and destination filenames. Compare all versioned files, including references, rather than just `SKILL.md`. List added, changed and obsolete paths. Inspect the source's release scope and content changes; a newer SDK label alone is not evidence of an updated contract.

## 3. Resolve content and scope findings

Preserve content that remains in the maintained source. Review deleted paths against the source's current documentation scope and record their replacement or exclusion. If the source contains a suspected factual error, verify the exact released API, compiler or independently versioned module before propagating it. Propose the correction in TiTools instead of starting a separate local variant.

A read-only request ends with this report. For an authorized update, continue the requested synchronization. Ask only about scope or authority that the conversation does not resolve.

## 4. Synchronize the selected directories

Replace the selected destination trees with the versioned files from the reviewed source commit. Exclude ignored files and private audit resources. Copy `AUDIT-SKIP` files unchanged from their maintained source; their marker excludes a wrong-source semantic audit, not a synchronization. Keep `ti-module-update` and `ti-create-release` unchanged.

## 5. Verify the copied resources

- Source and destination paths and bytes match for each selected skill.
- Skill names match their directories, descriptions are valid YAML and frontmatter remains portable.
- Referenced local files and anchors exist; split references are routed from the entry point.
- Changed examples retain their API, platform, permission and lifecycle requirements.
- Removed content is accounted for by the reviewed source revision and its declared scope.
- No audit report, temporary path or cache appears in the public skills.

Use [quality-standards.md](quality-standards.md) for source verification. Do not claim device behavior from static checks or a name-coverage script.

## 6. Prepare the contribution

Review the full diff, including deletions and README routing. Record the TiTools commit, SDK baseline and checks in the PR. Keep the description proportional to the change and distinguish a proposed maintenance workflow from an agreement already made by maintainers.

## 7. Hand off

Report the branch or PR, what changed, validation and unresolved limits. Keep session notes and raw audit evidence outside the distributed skill folders.
