---
name: ddd:setup-code-formating
description: Emit .claude/rules/code-formatting.md with code formatting rules and register pointer in CLAUDE.md
argument-hint: None required - creates standard formatting configuration
---

# Setup Code Formatting Rules

Your job: create a scoped rules file under `.claude/rules/` and register a pointer in `CLAUDE.md`. Do NOT dump the rules content into `CLAUDE.md` itself.

## Step 1 — Write the rules file

Use the Write tool to create `.claude/rules/code-formatting.md` with the following content, strictly as-is:

```markdown
---
description: Code formatting and style rules (auto-loads on source files)
globs: ["**/*.ts", "**/*.tsx", "**/*.js", "**/*.jsx", "**/*.mjs", "**/*.cjs"]
---

# Code Formatting

## Rules

- No semicolons (enforced)
- Single quotes (enforced)
- No unnecessary curly braces (enforced)
- 2-space indentation
- Import order: external → internal → types
```

## Step 2 — Register pointer in CLAUDE.md

Use the Read tool to check if `CLAUDE.md` exists in project root.

- If `CLAUDE.md` does NOT exist: create it with this minimal content:

  ```markdown
  # Project

  ## Agent docs

  - `.claude/rules/code-formatting.md` — code formatting rules (auto-loads on JS/TS files)
  ```

- If `CLAUDE.md` EXISTS:
  - If it already contains an `## Agent docs` section: use Edit tool to append the pointer line under that section, unless the exact same line already exists.
  - If no `## Agent docs` section: use Edit tool to append a new section at the end:

    ```markdown

    ## Agent docs

    - `.claude/rules/code-formatting.md` — code formatting rules (auto-loads on JS/TS files)
    ```

Do not modify any other part of `CLAUDE.md`.

## Step 3 — Confirm

Report back:
- Path of rules file written.
- Whether `CLAUDE.md` was created or updated.
- The pointer line added.
