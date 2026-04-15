---
description: Install the graphify skill into this project's .claude/skills/ directory
argument-hint: No arguments
---

# Install graphify skill (per-project)

Copy the bundled `graphify` skill into `<project>/.claude/skills/graphify/` so it activates only in this project.

**Purpose**: Turn any input (code, docs, papers, images) into a clustered knowledge graph

## Step 1 — Locate marketplace

```bash
MARKETPLACE=$(find ~/.claude/plugins/marketplaces -maxdepth 2 -type d -name "context-engineering-kit-scm" 2>/dev/null | head -1)
```

If empty, tell the user the marketplace is not installed and stop.

## Step 2 — Check for existing install

If `<project>/.claude/skills/graphify/` already exists, ask: *"Already installed. Overwrite? (y/n)"* — stop on `n`.

## Step 3 — Copy

```bash
mkdir -p .claude/skills
cp -r "$MARKETPLACE/plugins/skills/sources/graphify" .claude/skills/
```

## Step 4 — Verify

```bash
ls -la .claude/skills/graphify/
head -5 .claude/skills/graphify/SKILL.md
```

## Step 5 — Report

- Source path used.
- Target path written.
- Files installed (count + any `references/` subdir).
- Note: *"Restart Claude Code (or run `/plugin marketplace update`) so the skill is discovered. Once active, the agent can invoke it in this project."*
