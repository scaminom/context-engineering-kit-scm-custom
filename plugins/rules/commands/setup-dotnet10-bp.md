---
description: Install ASP.NET Core 10 best practices as a project rule (fixed globs for .NET files)
argument-hint: No arguments
---

# Setup ASP.NET Core 10 Best Practices Rule

Install `.claude/rules/dotnet10-bp.md` from bundled source. Globs are fixed to `["**/*.cs", "**/*.csproj", "**/*.cshtml", "**/*.razor"]` — no stack inference.

## 1. Locate marketplace

```bash
MARKETPLACE=$(find ~/.claude/plugins/marketplaces -maxdepth 2 -type d -name "context-engineering-kit-scm" 2>/dev/null | head -1)
```

Empty → stop.

## 2. Validate .NET stack

Glob for `*.csproj`, `global.json`, or `*.sln`. If none found, ask: *"No .NET markers detected. Install anyway? (y/n)"* — stop on `n`.

Optionally: if a `*.csproj` is found, read its `<TargetFramework>` element. If the target is NOT .NET 10 (e.g. net6.0, net8.0), warn the user: *"Detected target `<value>`, but this rule is specific to .NET 10. Consider `/rules:setup-dotnet8-bp` instead. Proceed anyway? (y/n)"*.

## 3. Copy source

```bash
mkdir -p .claude/rules
cp "$MARKETPLACE/plugins/rules/sources/rule_body/dotnet10-bp.md" .claude/rules/dotnet10-bp.md
```

## 4. Register pointer in CLAUDE.md

Follow POINTER_REGISTRATION from `$MARKETPLACE/plugins/rules/STACK_DETECTION.md` with:
- `<slug>` = `dotnet10-bp`
- `<rule description>` = `ASP.NET Core 10 performance & reliability`
- `<SUMMARY>` = `**/*.cs`

## 5. Report

Detected target framework · file written · CLAUDE.md action.
