---
name: tech-stack:add-typescript-best-practices
description: Emit .claude/rules/typescript-best-practices.md with TypeScript code style rules and register pointer in CLAUDE.md
argument-hint: Optional argument which practices to add or avoid
---

# Setup TypeScript Best Practices

Your job: create a scoped rules file under `.claude/rules/` and register a pointer in `CLAUDE.md`. Do NOT dump the rules content into `CLAUDE.md` itself.

## Step 1 — Write the rules file

Use the Write tool to create `.claude/rules/typescript-best-practices.md` with the following content, strictly as-is:

```markdown
---
description: TypeScript code style and best practices (auto-loads on TS files)
globs: ["**/*.ts", "**/*.tsx"]
---

# TypeScript Best Practices

## General Principles

- **TypeScript**: All code must be strictly typed, leverage TypeScript's type safety features

## Code style rules

- Interfaces over types - use interfaces for object types
- Use enum for constant values, prefer them over string literals
- Export all types by default
- Use type guards instead of type assertions

## Best Practices

### Library-First Approach

- Common areas where libraries should be preferred:
  - Date/time manipulation → date-fns, dayjs
  - Form validation → joi, yup, zod
  - HTTP requests → axios, got
  - State management → Redux, MobX, Zustand
  - Utility functions → lodash, ramda

### Code Quality

- Use destructuring of objects where possible:
  - Instead of `const name = user.name` use `const { name } = user`
  - Instead of `const result = await getUser(userId)` use `const { data: user } = await getUser(userId)`
  - Instead of `const parseData = (data) => data.name` use `const parseData = ({ name }) => name`
- Use `ms` package for time related configuration and environment variables, instead of multiplying numbers by 1000
```

## Step 2 — Register pointer in CLAUDE.md

Use the Read tool to check if `CLAUDE.md` exists in project root.

- If `CLAUDE.md` does NOT exist: create it with this minimal content:

  ```markdown
  # Project

  ## Agent docs

  - `.claude/rules/typescript-best-practices.md` — TypeScript code style (auto-loads on `**/*.ts`, `**/*.tsx`)
  ```

- If `CLAUDE.md` EXISTS:
  - If it already contains an `## Agent docs` section: use Edit tool to append the pointer line under that section, unless the exact same line already exists.
  - If no `## Agent docs` section: use Edit tool to append a new section at the end:

    ```markdown

    ## Agent docs

    - `.claude/rules/typescript-best-practices.md` — TypeScript code style (auto-loads on `**/*.ts`, `**/*.tsx`)
    ```

Do not modify any other part of `CLAUDE.md`.

## Step 3 — Confirm

Report back:
- Path of rules file written.
- Whether `CLAUDE.md` was created or updated.
- The pointer line added.
