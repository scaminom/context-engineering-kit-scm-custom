---
description: DRY — Don't Repeat Yourself. Every piece of knowledge has a single, authoritative representation.
paths:
__PATHS__
---

# DRY — Don't Repeat Yourself

Duplication of **knowledge** is the real sin — not duplication of syntax. Two functions that look identical but answer different questions should stay separate; two pieces of code that encode the same business rule must converge.

## Rules

- **Do** extract when two call sites encode the same rule, formula, or decision.
- **Do** name the extracted concept after the domain meaning, not the shape of the code.
- **Do not** deduplicate code that merely looks similar but serves different concerns — that's coincidence, not duplication.
- **Do not** extract after the first occurrence — wait for the second real case so the shape is clear.

## Rule of three

1. First time — write it inline.
2. Second time — tolerate the duplication, note the pattern.
3. Third time — extract. Now you know the true variation points.

## Anti-patterns

| Symptom | Likely cause | Action |
|---|---|---|
| Two utils look the same but change for different reasons | Coincidental duplication | Leave them alone |
| Bug fix applied in one place, missed in two others | Real duplication | Extract |
| Helper with 6 boolean flags to cover every caller | Premature DRY | Split it back apart |
| Shared module imported by two unrelated modules for one tiny helper | Wrong extraction | Inline back or move down |

## DRY vs YAGNI

They tension. YAGNI says "don't generalize yet." DRY says "converge when knowledge repeats." The resolution: **wait for the knowledge to prove repeated, then converge**. Never extract on the first occurrence.
