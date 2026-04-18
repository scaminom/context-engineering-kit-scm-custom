---
name: ddd:setup-code-formating
description: Emit .claude/rules/code-formatting.md with code formatting rules. Rules auto-load via paths — no CLAUDE.md pointer needed.
argument-hint: None required - creates standard formatting configuration
---

# Setup Code Formatting Rules

Your job: create a scoped rules file under `.claude/rules/`. Rules auto-load from that directory per the [official Claude Code memory docs](https://code.claude.com/docs/en/memory#organize-rules-with-claude/rules/) — do NOT write to `CLAUDE.md`.

## Step 1 — Write the rules file

Use the Write tool to create `.claude/rules/code-formatting.md` with the following content, strictly as-is:

```markdown
---
description: Code formatting and style rules (auto-loads on source files)
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.js"
  - "**/*.jsx"
  - "**/*.mjs"
  - "**/*.cjs"
---

# Code Formatting

## Rules

- No semicolons (enforced)
- Single quotes (enforced)
- No unnecessary curly braces (enforced)
- 2-space indentation
- Import order: external → internal → types
```

## Step 2 — Confirm

Report back:
- Path of rules file written.
- Note that the rule will auto-load when Claude reads any matching source file — no CLAUDE.md edit required.
