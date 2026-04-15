---
description: Install TypeScript best practices as a project rule (fixed globs ["**/*.ts", "**/*.tsx"])
argument-hint: No arguments
---

# Setup TypeScript Best Practices Rule

Install `.claude/rules/typescript-bp.md` from bundled source. Globs are fixed to `["**/*.ts", "**/*.tsx"]` — no stack inference.

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

No `__GLOBS__` replacement needed — the source file has fixed globs baked in.

## 4. Register pointer in CLAUDE.md

Follow the POINTER_REGISTRATION routine from `$MARKETPLACE/plugins/rules/STACK_DETECTION.md` with:
- `<slug>` = `typescript-bp`
- `<rule description>` = `TypeScript best practices`
- `<SUMMARY>` = `**/*.ts, **/*.tsx`

## 5. Report

`tsconfig.json` found? · file written · CLAUDE.md action.
