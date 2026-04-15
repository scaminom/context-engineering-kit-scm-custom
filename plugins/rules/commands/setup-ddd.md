---
description: Install the core DDD bundle — Clean Architecture, Separation of Concerns, Domain-specific naming.
argument-hint: No arguments
---

# Setup DDD Bundle

Install the three core DDD rules in one pass. For each rule below, follow the **Standard command flow** from `$MARKETPLACE/plugins/rules/STACK_DETECTION.md`.

Tell the user upfront: *"This will install 3 rules (clean-architecture-ddd, separation-of-concerns, domain-specific-naming). Proceed? (y/n)"* — stop on `n`.

Run for each, in order:

1. `<slug>` = `clean-architecture-ddd`, `<rule description>` = `Clean Architecture / DDD`, skill-ref: none.
2. `<slug>` = `separation-of-concerns`, `<rule description>` = `Separation of concerns`, skill-ref: none.
3. `<slug>` = `domain-specific-naming`, `<rule description>` = `Domain-specific naming`, skill-ref: none.

Detect the stack **once** at the start and reuse GLOBS / SUMMARY across all three — do not re-prompt or re-detect.

## Report

Summarize at the end: 3 rules written, CLAUDE.md pointer lines added (count new vs skipped), detected stack.
