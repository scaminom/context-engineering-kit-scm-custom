---
description: Install design pattern guidance as a project rule. Optionally references the design-pattern-advisor skill (prompts user).
argument-hint: No arguments
---

# Setup Design Patterns Rule

Install `.claude/rules/design-patterns.md` using the shared routine.

Read `$MARKETPLACE/plugins/rules/STACK_DETECTION.md` and follow its **Standard command flow** with:

- `<slug>` = `design-patterns`
- `<rule description>` = `Design pattern guidance`
- Skill reference: **yes**
  - `<skill-name>` = `design-pattern-advisor`
  - `<activity>` = "face a recurring design problem (class hierarchies, behavior variations, object creation, decoupling)"
  - If user accepts, remind at report: *"Install the skill per-project with `/skills:setup-design-pattern-advisor`."*
