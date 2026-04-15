---
name: project-rules-generator
description: Generate, expand, or audit the .claude/rules/ directory for a project. Use this skill whenever the user wants to create a new rule file, add more rules to an existing setup, restructure their rules directory, or audit which rules should be path-scoped vs. always-loaded. Triggers on phrases like "create a rule for X", "add rules for my API files", "set up .claude/rules/", "scope Claude instructions to specific files", "my CLAUDE.md is too long", "split my CLAUDE.md into rules", or any time the user wants to add targeted instructions for specific file types or domains. Also use when iterating on existing rules or debugging why a rule isn't loading.
---

# Claude Rules Generator

Generate and iterate on `.claude/rules/` files — the modular, path-scoped instruction system that keeps CLAUDE.md lean and context-relevant.

## Mental model

Rules are **not** a dumping ground for everything that didn't fit in CLAUDE.md. They're scoped contracts:

- **Always-loaded rules** (no `paths:` frontmatter): team-wide standards that apply regardless of what's being worked on
- **Path-scoped rules** (`paths:` frontmatter): domain-specific guidance that only loads when Claude touches matching files

Path-scoped rules load when Claude **reads** a matching file — not when it edits or creates one. This is a known limitation. Safety-critical rules must not rely on path-scoping.

Read `references/known-limitations.md` before writing any path-scoped rules for file creation workflows.

---

## Step 1: Audit what exists (or start fresh)

If a project already has `.claude/rules/`, read the existing files before suggesting anything new. Look for:
- Overlap between rules files
- Instructions that belong in CLAUDE.md instead
- Instructions that belong in `agent_docs/` instead
- Path patterns that are too broad or too narrow

If starting fresh, ask the user:
1. What tech stack / file types exist in the project?
2. Are there any domains with clearly different conventions? (API, frontend, DB, tests)
3. Any instructions that currently live in CLAUDE.md that only apply to specific file types?

---

## Step 2: Decide the rule type

For each candidate rule, apply this decision tree:

```
Is it universally applicable to every session?
├── Yes → CLAUDE.md (not a rule)
│
└── No → Does it apply to all files?
    ├── Yes → Always-loaded rule (no paths:)
    │         Examples: security checklist, commit message format,
    │                   PR description standards
    │
    └── No → Path-scoped rule (with paths:)
              Examples: API patterns for *.ts under src/api/,
                        migration safety for prisma/migrations/**,
                        component patterns for **/*.tsx
```

---

## Step 3: Write the rule file

### Frontmatter

Always-loaded (no frontmatter needed — just omit it entirely):
```markdown
# Security Rules
- Never log request bodies in production
- Always validate and sanitize user input at the boundary
```

Path-scoped (YAML frontmatter required):
```markdown
---
paths:
  - "src/api/**/*.ts"
---

# API Development Rules
- All endpoints must validate input with Zod
- Return consistent error shapes: `{ error: string, code: number }`
- Log all requests with a correlation ID
```

### Critical: glob patterns must be quoted

```yaml
# ❌ WRONG — invalid YAML, breaks parsing
paths:
  - **/*.ts
  - {src,lib}/**/*.ts

# ✅ CORRECT
paths:
  - "**/*.ts"
  - "{src,lib}/**/*.ts"
```

### Common path patterns

```yaml
# TypeScript files
paths:
  - "src/**/*.{ts,tsx}"

# Test files anywhere
paths:
  - "**/*.test.ts"
  - "**/*.spec.ts"
  - "**/__tests__/**/*.ts"

# Database / migrations
paths:
  - "prisma/**"
  - "src/db/**/*"
  - "migrations/**"

# Frontend components
paths:
  - "src/components/**/*.tsx"
  - "src/pages/**/*.tsx"

# Multiple dirs, same extension
paths:
  - "{src,lib,packages}/**/*.ts"
```

---

## Step 4: Recommended starting structure

For a full-stack TypeScript project, this is a solid baseline:

```
.claude/rules/
├── security.md         # No paths — always loaded (critical)
├── code-style.md       # No paths — always loaded (universal)
├── api-patterns.md     # paths: src/api/**/*.ts
├── testing.md          # paths: **/*.{test,spec}.ts
├── database.md         # paths: prisma/**, src/db/**/*
└── frontend/
    ├── react.md        # paths: src/components/**/*.tsx
    └── styling.md      # paths: **/*.{css,scss}
```

For a Python project:
```
.claude/rules/
├── security.md         # No paths — always loaded
├── api-patterns.md     # paths: src/api/**/*.py, app/routes/**/*.py
├── testing.md          # paths: tests/**/*.py, **/test_*.py
└── models.md           # paths: src/models/**/*.py
```

---

## Step 5: Rules that should NOT use paths:

These are always-loaded regardless of project type — never path-scope them:

- Security requirements ("never log PII", "always sanitize input")
- Destructive operation guards ("never drop tables without backup")
- Commit / PR standards
- Code review checklist items

---

## Step 6: Iterate

When the user wants to add a new rule:

1. Ask: "What files does this apply to? Always, or only specific types?"
2. Check for conflicts with existing rules
3. Write the file with a single focused topic
4. Recommend where to place it in the directory structure

When the user says "Claude ignored my rule":
1. Check if the rule has `paths:` — if so, verify Claude read the file first (not just edited)
2. Check glob syntax — are patterns quoted?
3. Suggest moving to always-loaded if it's safety-critical
4. See `references/known-limitations.md` for the Write-without-Read edge case and its workaround

---

## Delivering rules

For each rule file, output:
1. The file path (e.g., `.claude/rules/api-patterns.md`)
2. The complete file content, ready to copy-paste
3. One-line explanation of when it loads

After delivering, always ask: "Do you have other domains or file types that need their own rules? Common ones people add later: GraphQL resolvers, serverless functions, CI/CD config, infrastructure as code."
