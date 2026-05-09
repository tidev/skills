# Audit workflow

7-phase process for auditing and updating an existing doc-based skill in this repo against its upstream documentation source. Includes a mandatory user approval gate before applying any changes.

**Pre-requisite:** Load `quality-standards.md` and `source-map.md` before starting this workflow.

---

## Phase 0: Pre-flight + classify source type

Before starting an audit, do two things.

### 0a. Verify the docs cache

Check that `.titanium-docs/` exists at the repo root. If missing, stop and prompt the user with:

```bash
git clone --depth 1 https://github.com/tidev/titanium-docs.git .titanium-docs
```

If present, optionally refresh:

```bash
cd .titanium-docs && git pull --ff-only && cd ..
```

Note the latest commit hash (e.g. `git -C .titanium-docs rev-parse --short HEAD`) so the audit report can record which upstream version it was compared against.

### 0b. Classify the skill's source type

| Source type | Skills | Audit approach |
|---|---|---|
| **Narrative** | `alloy-guides`, `alloy-howtos`, `ti-guides`, `ti-howtos` | Compare reference files against official guide subdirectories |
| **API** | `ti-api` | Compare against generated `.md` files in `.titanium-docs/docs/api/` |

The source type determines audit strategy:

- **Narrative**: Direct section-by-section comparison.
- **API**: Compare per-namespace; expect some references to map to many small upstream files.

---

## Phase 1: Coverage analysis

Map the current state of the skill against its upstream documentation.

### AUDIT-SKIP files

Before doing coverage analysis, scan all reference files in the skill for an `<!-- AUDIT-SKIP -->` HTML comment near the top. Files marked with this comment are manually maintained against a different upstream source (e.g. a third-party toolkit's own docs) and must be **excluded from the audit entirely**:

- Do NOT read or compare the file against any upstream `tidev/titanium-docs` source.
- Do NOT include the file in the coverage table.
- List skipped files in a separate "Skipped (manually maintained)" section of the report.
- Source-map.md should also flag these files; cross-check both signals.

If you propose any change to an AUDIT-SKIP file, that is a bug in the audit. Stop and ask the user before continuing.

### Steps

1. **Identify the source subtree** for the skill from `source-map.md`.
2. **List all files in both locations:**
   - Official docs: `Glob` the source subtree
   - Skill references: `Glob` the skill's `references/` directory
   - **Filter out AUDIT-SKIP files** (see above) — exclude them from all later phases.
3. **Read the skill's `SKILL.md`** to understand its quick reference table and overall structure.
4. **Generate a coverage table:**

   | Reference file | Official source file(s) | Ref lines | Source lines | Coverage estimate |
   |---|---|---|---|---|
   | `CONTROLLERS.md` | `Alloy_Controllers.md` | 380 | 520 | ~70% |
   | `MODELS.md` | `Alloy_Models.md` | 450 | 680 | ~65% |

### Output

Present the coverage table to the user. Note any reference files that have no clear official source mapping — these may be author additions or curated content.

---

## Phase 2: Gap identification

Compare each reference file against its upstream source in detail.

> **Use parallel agents for this phase.** Each reference file comparison is independent.

### Per-reference comparison steps

For each reference file:

1. **Read the reference file** completely.
2. **Read the corresponding official source file(s)** completely.
3. **Compare section by section.** For each section in the official docs:
   - Mark as **complete** (exists in reference with equivalent detail)
   - Mark as **partial** (exists but missing subsections, examples, or options)
   - Mark as **missing** (not covered at all)
   - Mark as **author addition** (exists in reference but NOT in official docs)
4. **Check code examples:**
   - Are all official examples present in the reference?
   - Are reference examples accurate (not hallucinated)?
   - Are examples updated to ES6+ style?
5. **Classify content without an official source.** Not all unlabeled content is training-data contamination. Distinguish three cases:
   - **Training-data contamination** (propose DELETE): methods/properties that don't exist in the SDK, wrong parameter names, overly generic advice, patterns from other frameworks (React, Vue, Cordova).
   - **Legitimate author contribution** (propose PRESERVE + MARK): verifiable real-world patterns, workarounds for known SDK bugs, best practices from production apps. Propose moving these into a `## Community-Discovered Patterns` section — never silently delete.
   - **Ambiguous** (propose ASK): cannot verify either way. Flag for user decision.
6. **Respect the `## Community-Discovered Patterns` convention.** Sections under this H2 heading are PROTECTED — never delete during audits, even without official source. See `quality-standards.md` § "Protected sections".

### Output per reference

```markdown
### CONTROLLERS.md

| Official section | Status | Notes |
|---|---|---|
| Controller lifecycle | Complete | |
| Event handling | Partial | Missing `removeEventListener` cleanup pattern |
| Conditional code | Complete | |
| Passing arguments | Missing | Not covered at all |

**Author additions (not in official docs):**
- ES6+ migration tips section — valid enhancement, keep

**Suspected training-data content:**
- `$.args.getProperty()` method — does not exist in SDK, REMOVE
```

---

## Phase 3: Consolidated report

Aggregate all per-reference findings into a single report.

### Report structure

```markdown
## Audit report: <skill-name>

**Compared against:** `tidev/titanium-docs@<commit-hash>`

### Overall health
- **References audited:** X of Y
- **Average coverage:** XX%
- **Critical gaps:** X sections completely missing
- **Training-data contamination:** X instances found

### Priority fixes (must fix)
1. [reference] Section X — completely missing, covers core functionality
2. [reference] Method Y — hallucinated, does not exist in SDK

### Recommended updates (should fix)
1. [reference] Section X — partial, missing Z examples
2. [reference] Code examples — need ES6+ update

### Low priority (nice to have)
1. [reference] Could add cross-reference to related skill
2. [reference] Platform-specific warning for Android

### Author additions to preserve
1. [reference] Section X — valid enhancement beyond official docs

### Estimated effort
- Full rewrite of X references
- Targeted updates to Y references
- Total reference files to modify: Z
```

---

## Phase 4: User approval gate

> **STOP. Do NOT proceed past this phase without explicit user approval.**

### Present to the user

1. The consolidated report from Phase 3.
2. Ask: *"Should I proceed with these updates? You can also:"*
   - Adjust priorities
   - Skip certain references
   - Request full rewrite vs targeted edits for specific files
   - Add additional items you've noticed

### Wait for one of

- **"Proceed"** / **"Go ahead"** / **"Aplica"** — Continue to Phase 5
- **"Skip X"** — Remove items from the update list
- **"Rewrite X"** — Force full rewrite of specific references
- **"Cancel"** — Stop the audit; the report can stay as a notes file for future reference

---

## Phase 5: Apply updates

Execute the approved changes from Phase 4.

### Decision: rewrite vs targeted edit

For each reference file, decide the approach:

| Condition | Approach |
|---|---|
| Coverage < 50% or heavy contamination | **Full rewrite** — re-read source, write from scratch |
| Coverage 50–80% with clear gaps | **Targeted additions** — add missing sections, fix errors |
| Coverage > 80% with minor gaps | **Targeted edits** — small additions and corrections |
| Training-data contamination found | **Full rewrite** of affected sections at minimum |

### Applying updates

> **Use parallel agents for this phase.** Each reference file update is independent.

For each reference file being updated:

1. **Re-read the official source** — never write from memory of Phase 2.
2. **Apply the changes** based on the approach:
   - **Full rewrite**: Use the `Write` tool to replace the entire file.
   - **Targeted additions**: Use the `Edit` tool to add sections.
   - **Targeted edits**: Use the `Edit` tool for specific corrections.
3. **Preserve author additions** flagged in Phase 2 (unless the user said to remove them).
4. **Apply quality standards** from `quality-standards.md`:
   - ES6+ code style
   - No dead URLs
   - English only
   - Practical code examples

### Preservation rules

When updating references, preserve:

- **Author additions** marked as valid in Phase 2
- **Cross-references** to other references in the skill
- **Platform-specific warnings** (even if not in official docs)
- **ES6+ style updates** already applied to examples

When updating references, remove or fix:

- **Training-data contamination** (hallucinated methods, wrong parameters)
- **Dead URLs** (`docs.appcelerator.com`, etc.)
- **Duplicate content** (same info in multiple sections)
- **Outdated patterns** (`var`, `Ti.include`, etc.)

---

## Phase 6: Post-audit verification

Verify the updates are correct and complete.

### Steps

1. **Spot-check** — read 2–3 updated reference files and verify:
   - New content matches official source
   - No new hallucinations introduced
   - ES6+ style maintained
   - Author additions preserved where flagged

2. **Verify `SKILL.md`** — does the quick reference table still match the reference files?
   - Were any reference files added or removed?
   - Does the description still accurately reflect the skill's scope?

3. **Check file sizes:**
   ```bash
   wc -l skills/<skill-name>/SKILL.md
   wc -l skills/<skill-name>/references/*.md
   ```
   - `SKILL.md` should be under 500 lines.
   - References should be under 800 lines each.

4. **Verify no orphaned content** — grep for references to removed or renamed sections.

### Remind the user

- One commit per audited skill: `audit(<skill>): align refs with titanium-docs <date or commit>`.
- Mention the upstream commit hash from Phase 0a in the commit body or PR description.
- If changes are substantial, surface them in the PR description so reviewers see the diff context.
