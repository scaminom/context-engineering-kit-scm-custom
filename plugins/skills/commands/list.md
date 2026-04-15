---
description: List all skills installed in this project's .claude/skills/ with their descriptions and references
argument-hint: No arguments
---

# List Project Skills

Scan `.claude/skills/` in the project root and report what's installed.

## Step 1 — Locate skills

Use Glob to find `.claude/skills/*/SKILL.md` (max depth 2). If none found, report *"No skills installed in this project. Run `/skills:setup-*` to add some."* and stop.

## Step 2 — For each skill, extract metadata

For each `SKILL.md` file, Read the first ~15 lines to parse frontmatter:
- `name:` (fallback to directory name if missing).
- `description:` — one-liner.

Then check for a `references/` subdir in the same skill folder. If present, count files.

## Step 3 — Report table

Output a markdown table:

| Name | Description | References |
|---|---|---|
| dotnet-expert | .NET 8+ expert (minimal APIs, CQRS, EF Core, ...) | 8 files |
| design-pattern-advisor | GoF pattern recommendations | 1 file |

Below the table:
- Total skills: N
- Total reference files: N
- Note: *"Skills activate automatically based on their `description:` triggers. No CLAUDE.md pointer needed."*

If zero skills, link to `/skills:setup-*` commands for the four bundled installers.
