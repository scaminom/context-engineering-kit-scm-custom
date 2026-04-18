---
description: List all rules installed in this project's .claude/rules/ with their paths
argument-hint: No arguments
---

# List Project Rules

Scan `.claude/rules/` (recursively) in the project root and report what's installed.

## Step 1 — Locate rules

Use Glob to find `.claude/rules/**/*.md`. If none found, report *"No rules installed. Run `/rules:setup-*` to add some."* and stop.

## Step 2 — For each rule, extract metadata

For each file, Read the first ~15 lines to parse YAML frontmatter:
- `description:` — one-liner (optional).
- `paths:` — YAML list of glob patterns (optional; absent = always-loaded rule).

Also detect if the rule body contains a `## Skill reference` section (grep).

## Step 3 — Report table

Output a markdown table:

| Slug | Description | Paths | Load mode | Skill ref |
|---|---|---|---|---|
| solid | SOLID principles | `**/*.cs` | path-scoped | — |
| dotnet | .NET coding guidance | `**/*.cs`, `**/*.csproj` | path-scoped | ✓ |
| kiss | KISS principle | *(none)* | always-on | — |

`Load mode`:
- `always-on` — rule has no `paths:` frontmatter; loaded every session.
- `path-scoped` — rule has `paths:`; auto-loads when Claude reads files matching the patterns.

Below the table, summarize:
- Total rules: N
- Always-on: N (list slugs)
- Path-scoped: N
- With skill references: N (list slugs)

If any rules look out of place, suggest: *"Run `/rules:doctor` for a full audit."*
