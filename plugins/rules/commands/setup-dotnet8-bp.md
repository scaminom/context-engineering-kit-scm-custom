---
description: Install ASP.NET Core 8 best practices as a project rule (fixed paths for .NET files)
argument-hint: No arguments
---

# Setup ASP.NET Core 8 Best Practices Rule

Install `.claude/rules/dotnet8-bp.md` from bundled source. Paths are fixed to `**/*.cs`, `**/*.csproj`, `**/*.cshtml`, `**/*.razor` — no stack inference.

## 1. Locate marketplace

```bash
MARKETPLACE=$(find ~/.claude/plugins/marketplaces -maxdepth 2 -type d -name "context-engineering-kit-scm" 2>/dev/null | head -1)
```

Empty → stop.

## 2. Validate .NET stack

Glob for `*.csproj`, `global.json`, or `*.sln`. If none found, ask: *"No .NET markers detected. Install anyway? (y/n)"* — stop on `n`.

Optionally: if a `*.csproj` is found, read its `<TargetFramework>` element. If the target is NOT .NET 8 (e.g. net6.0, net10.0), warn the user: *"Detected target `<value>`, but this rule is specific to .NET 8. Consider `/rules:setup-dotnet10-bp` instead. Proceed anyway? (y/n)"*.

## 3. Copy source

```bash
mkdir -p .claude/rules
cp "$MARKETPLACE/plugins/rules/sources/rule_body/dotnet8-bp.md" .claude/rules/dotnet8-bp.md
```

The source file already has the `paths:` frontmatter baked in — no token replacement needed.

## 4. Report

Detected target framework · file written at `.claude/rules/dotnet8-bp.md`.
