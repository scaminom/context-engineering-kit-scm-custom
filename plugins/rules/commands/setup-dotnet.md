---
description: Install .NET coding guidance as a project rule. Optionally references the dotnet-expert skill (prompts user).
argument-hint: No arguments
---

# Setup .NET Rule

Install `.claude/rules/dotnet.md` using the shared routine.

Read `$MARKETPLACE/plugins/rules/STACK_DETECTION.md` and follow its **Standard command flow** with:

- `<slug>` = `dotnet`
- `<rule description>` = `.NET coding guidance`
- **Stack validation override**: before step 2, confirm .NET markers exist (`*.csproj`, `global.json`, `*.sln`). If none found, ask the user *"No .NET markers detected. Install anyway? (y/n)"* — stop on `n`. Then force globs to `["**/*.cs", "**/*.csproj"]` regardless of detection table.
- Skill reference: **yes**
  - `<skill-name>` = `dotnet-expert`
  - `<activity>` = "write .NET code (minimal APIs, CQRS, EF Core, auth, caching, cloud-native)"
  - If user accepts, also tell them at report time: *"Install the skill per-project with `/skills:setup-dotnet-expert`."*
