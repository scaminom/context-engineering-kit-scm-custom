---
description: Install the design-pattern-advisor skill into this project's .claude/skills/ directory
argument-hint: No arguments
---

# Install design-pattern-advisor skill (per-project)

Goal: copy the bundled `design-pattern-advisor` skill into `<project>/.claude/skills/design-pattern-advisor/` so it activates **only** in this project.

## Step 1 — Locate the plugin source

The skill source is bundled inside this marketplace. Find its absolute path:

Use Bash:
```bash
MARKETPLACE=$(find ~/.claude/plugins/marketplaces -maxdepth 2 -type d -name "context-engineering-kit-scm" 2>/dev/null | head -1)
echo "$MARKETPLACE"
```

If empty, tell the user: *"Could not locate the `context-engineering-kit-scm` marketplace under `~/.claude/plugins/marketplaces/`. Is it installed?"* — and stop.

Expected source path:
```
$MARKETPLACE/plugins/skills/sources/design-pattern-advisor/
```

## Step 2 — Confirm before overwriting

Check if `<project>/.claude/skills/design-pattern-advisor/` already exists.

- If yes, ask the user: *"`.claude/skills/design-pattern-advisor/` already exists. Overwrite? (y/n)"* — stop if `n`.

## Step 3 — Copy

Use Bash to create the target dir and copy the bundled skill:

```bash
mkdir -p .claude/skills
cp -r "$MARKETPLACE/plugins/skills/sources/design-pattern-advisor" .claude/skills/
```

## Step 4 — Verify

Use Bash to confirm:
```bash
ls -la .claude/skills/design-pattern-advisor/
cat .claude/skills/design-pattern-advisor/SKILL.md | head -5
```

Expected: `SKILL.md` present plus `references/` directory with `pattern-catalog.md`.

## Step 5 — Report

Tell the user:
- Source path used.
- Target path written.
- Files installed (count or list from `ls`).
- Note: *"Restart Claude Code (or run `/plugin marketplace update`) so the skill is discovered. Once active, the agent can invoke it when analyzing design problems in this project."*
