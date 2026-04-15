---
description: List all rules installed in this project's .claude/rules/ with their globs and pointer status
argument-hint: No arguments
---

# List Project Rules

Scan `.claude/rules/` in the project root and report what's installed.

## Step 1 — Locate rules

Use Glob to find `.claude/rules/*.md` in the project root (max depth 1). If none found, report *"No rules installed. Run `/rules:setup-*` to add some."* and stop.

## Step 2 — For each rule, extract metadata

For each file, Read the first ~10 lines to parse frontmatter:
- `description:` — one-liner.
- `globs:` — array value.

Also detect if the rule body contains a `## Skill reference` section (grep).

## Step 3 — Check CLAUDE.md pointers

Read `CLAUDE.md`. Find the `## Agent docs` section (if any). For each rule file, check whether a pointer line `- \`.claude/rules/<slug>.md\`` exists under that section. Mark rules WITHOUT a pointer as orphaned.

## Step 4 — Report table

Output a markdown table:

| Slug | Description | Globs | Pointer | Skill ref |
|---|---|---|---|---|
| solid | SOLID principles | `**/*.cs` | ✓ | — |
| dotnet | .NET coding guidance | `**/*.cs` | ✓ | ✓ |
| frontend | Frontend UI quality | `src/**/*.{ts,tsx}` | ✗ orphan | ✓ |

Below the table, summarize:
- Total rules: N
- Orphans (no pointer): N (list slugs)
- With skill references: N (list slugs)

If orphans exist, suggest: *"Run `/rules:doctor` for a full audit and fix suggestions."*
