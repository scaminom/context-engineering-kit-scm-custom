---
description: Install the api-design skill into this project's .claude/skills/ directory
argument-hint: No arguments
---

# Install api-design skill (per-project)

Copy the bundled `api-design` skill into `<project>/.claude/skills/api-design/` so it activates only in this project.

**Purpose**: REST API design patterns (resource naming, status codes, pagination, errors, versioning, rate limiting)

## Step 1 — Locate marketplace

```bash
MARKETPLACE=$(find ~/.claude/plugins/marketplaces -maxdepth 2 -type d -name "context-engineering-kit-scm" 2>/dev/null | head -1)
```

If empty, tell the user the marketplace is not installed and stop.

## Step 2 — Check for existing install

If `<project>/.claude/skills/api-design/` already exists, ask: *"Already installed. Overwrite? (y/n)"* — stop on `n`.

## Step 3 — Copy

```bash
mkdir -p .claude/skills
cp -r "$MARKETPLACE/plugins/skills/sources/api-design" .claude/skills/
```

## Step 4 — Verify

```bash
ls -la .claude/skills/api-design/
head -5 .claude/skills/api-design/SKILL.md
```

## Step 5 — Report

- Source path used.
- Target path written.
- Files installed (count + any `references/` subdir).
- Note: *"Restart Claude Code (or run `/plugin marketplace update`) so the skill is discovered. Once active, the agent can invoke it in this project."*
