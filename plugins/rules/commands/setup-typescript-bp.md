---
description: Install TypeScript best practices as a project rule (fixed paths ["**/*.ts", "**/*.tsx"])
argument-hint: No arguments
---

# Setup TypeScript Best Practices Rule

Install `.claude/rules/typescript-bp.md` from bundled source. Paths are fixed to `**/*.ts` and `**/*.tsx` — no stack inference.

## 1. Locate marketplace

```bash
MARKETPLACE=$(find ~/.claude/plugins/marketplaces -maxdepth 2 -type d -name "context-engineering-kit-scm" 2>/dev/null | head -1)
```

Empty → stop.

## 2. Validate TS stack

Glob for `tsconfig.json` or any `*.ts` / `*.tsx` file. If none found, ask: *"No TypeScript markers detected. Install anyway? (y/n)"* — stop on `n`.

## 3. Copy source

```bash
mkdir -p .claude/rules
cp "$MARKETPLACE/plugins/rules/sources/rule_body/typescript-bp.md" .claude/rules/typescript-bp.md
```

The source file already has the `paths:` frontmatter baked in — no token replacement needed.

## 4. Report

`tsconfig.json` found? · file written at `.claude/rules/typescript-bp.md`.
