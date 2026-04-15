---
description: Install REST API design rule (naming, status codes, pagination, errors, versioning). Optionally references the api-design skill (prompts user).
argument-hint: No arguments
---

# Setup API Design Rule

Install `.claude/rules/api-design.md` using the shared routine.

Read `$MARKETPLACE/plugins/rules/STACK_DETECTION.md` and follow its **Standard command flow** with:

- `<slug>` = `api-design`
- `<rule description>` = `REST API design`
- Skill reference: **yes**
  - `<skill-name>` = `api-design`
  - `<activity>` = "design new API surfaces (endpoints, pagination schemes, error contracts, versioning strategy)"
  - If user accepts, remind at report: *"Install the skill per-project with `/skills:setup-api-design`."*
