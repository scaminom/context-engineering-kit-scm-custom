---
description: Install frontend UI quality rule (accessibility, performance, craft). Optionally references the frontend-design skill (prompts user).
argument-hint: No arguments
---

# Setup Frontend Rule

Install `.claude/rules/frontend.md` using the shared routine.

Read `$MARKETPLACE/plugins/rules/STACK_DETECTION.md` and follow its **Standard command flow** with:

- `<slug>` = `frontend`
- `<rule description>` = `Frontend UI quality`
- Skill reference: **yes**
  - `<skill-name>` = `frontend-design`
  - `<activity>` = "design or build non-trivial UI (new pages, component systems, polished interactions)"
  - If user accepts, remind at report: *"Install the skill per-project with `/skills:setup-frontend-design`."*
