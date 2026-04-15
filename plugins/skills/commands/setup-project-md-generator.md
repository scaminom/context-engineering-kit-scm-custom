---
description: Install the project-md-generator skill into this project's .claude/skills/ directory
argument-hint: No arguments
---

# Install project-md-generator skill (per-project)

Copy the bundled `project-md-generator` skill into `<project>/.claude/skills/project-md-generator/` so it activates only in this project.

**Purpose**: Generate or audit CLAUDE.md / AGENTS.md for a project

## Step 1 — Locate marketplace

```bash
MARKETPLACE=$(find ~/.claude/plugins/marketplaces -maxdepth 2 -type d -name "context-engineering-kit-scm" 2>/dev/null | head -1)
```

If empty, tell the user the marketplace is not installed and stop.

## Step 2 — Check for existing install

If `<project>/.claude/skills/project-md-generator/` already exists, ask: *"Already installed. Overwrite? (y/n)"* — stop on `n`.

## Step 3 — Copy

```bash
mkdir -p .claude/skills
cp -r "$MARKETPLACE/plugins/skills/sources/project-md-generator" .claude/skills/
```

## Step 4 — Verify

```bash
ls -la .claude/skills/project-md-generator/
head -5 .claude/skills/project-md-generator/SKILL.md
```

## Step 5 — Report

- Source path used.
- Target path written.
- Files installed (count + any `references/` subdir).
- Note: *"Restart Claude Code (or run `/plugin marketplace update`) so the skill is discovered. Once active, the agent can invoke it in this project."*
