---
description: Install ASP.NET Core 8 best practices as a project rule (fixed globs for .NET files)
argument-hint: No arguments
---

# Setup ASP.NET Core 8 Best Practices Rule

Install `.claude/rules/dotnet8-bp.md` from bundled source. Globs are fixed to `["**/*.cs", "**/*.csproj", "**/*.cshtml", "**/*.razor"]` — no stack inference.

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

## 4. Register pointer in CLAUDE.md

Follow POINTER_REGISTRATION from `$MARKETPLACE/plugins/rules/STACK_DETECTION.md` with:
- `<slug>` = `dotnet8-bp`
- `<rule description>` = `ASP.NET Core 8 performance & reliability`
- `<SUMMARY>` = `**/*.cs`

## 5. Report

Detected target framework · file written · CLAUDE.md action.
