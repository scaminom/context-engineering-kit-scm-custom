---
name: design-pattern-advisor
description: >
  Analyzes a specific software problem or design challenge and recommends the most appropriate
  design pattern(s) from the GoF catalog. Use this skill whenever the user describes a coding
  problem, architectural challenge, or design dilemma — even if they don't mention "design patterns"
  explicitly. Triggers include: "how should I structure this?", "I have a class that...", "I need
  multiple implementations of...", "objects need to communicate without...", "I want to avoid
  duplicating...", "how do I add behavior without modifying...", or any description of a recurring
  software design problem. Always use this skill before recommending a pattern from memory — it
  ensures the recommendation is grounded in the problem context, not just pattern familiarity.
---

# Design Pattern Advisor

## Goal
Analyze the user's specific problem and recommend the **most appropriate design pattern(s)**, with a clear rationale grounded in their actual context — not a generic pattern overview.

## Workflow

### Step 1 — Understand the Problem
Extract from the user's description:
- **What** they're trying to build or fix
- **What pain** they're experiencing (duplication, tight coupling, rigidity, etc.)
- **What varies** vs. what stays the same
- **Runtime vs. compile-time** concerns
- Any **constraints**: language, existing architecture, team size

If critical information is missing, ask **one focused question** before proceeding.

### Step 2 — Identify Candidate Patterns
Map the problem to pattern categories using the decision heuristics in `references/pattern-catalog.md`. Consider:
- Is the problem about **object creation**? → Creational
- Is it about **composing objects/classes**? → Structural
- Is it about **communication or behavior between objects**? → Behavioral

Narrow to 1–3 candidates max.

### Step 3 — Recommend with Rationale
For each candidate (lead with the best one):

```
## Recommended: [Pattern Name]

**Why it fits your problem:**
[Specific mapping of their problem elements to the pattern's intent — not a copy-paste definition]

**What you gain:**
[Concrete benefits in their context]

**Watch out for:**
[Genuine tradeoffs or over-engineering risks for their specific case]

**Sketch:**
[Minimal pseudocode or class diagram tailored to their domain — not the generic textbook example]
```

If a second pattern is worth mentioning, add it briefly under **"Also consider"** with a one-sentence rationale.

### Step 4 — Anti-pattern Warning (when applicable)
If the user's description hints at a common mistake (e.g., reaching for Singleton out of habit, or using Inheritance where Composition fits better), call it out directly.

---

## Decision Heuristics (quick reference)

Read `references/pattern-catalog.md` for the full catalog. Use these shortcuts for fast triage:

| Signal in problem description | Likely pattern(s) |
|---|---|
| "I need to create objects but don't know the exact type until runtime" | Factory Method, Abstract Factory |
| "Construction is complex / many optional parameters" | Builder |
| "I need exactly one instance globally" | Singleton *(use with caution)* |
| "I need to copy objects cheaply" | Prototype |
| "Two incompatible interfaces need to work together" | Adapter |
| "I want to add behavior without subclassing" | Decorator |
| "I want to simplify a complex subsystem" | Facade |
| "Abstraction and implementation should vary independently" | Bridge |
| "Tree structures where leaves and composites are treated uniformly" | Composite |
| "Expensive objects shared across many contexts" | Flyweight |
| "Control access / add lazy init / logging to another object" | Proxy |
| "Switch algorithms at runtime" | Strategy |
| "Object behavior changes based on its own state" | State |
| "One-to-many dependency / event notification" | Observer |
| "Decouple request sender from receiver / support undo" | Command |
| "Reduce coupling between many communicating objects" | Mediator |
| "Save and restore object state / undo history" | Memento |
| "Add operations to objects without modifying them" | Visitor |
| "Fixed algorithm skeleton, variable steps" | Template Method |
| "Pass request along a chain until someone handles it" | Chain of Responsibility |
| "Traverse a collection without exposing its internals" | Iterator |

---

## Quality bar for your recommendation

A good recommendation:
- Uses the user's **own domain vocabulary** (their classes, not `ConcreteSubject`)
- Explains **why alternatives were NOT chosen**
- Acknowledges if the problem might be **too simple** to warrant a pattern
- Never recommends a pattern just because it's popular or "clean"

Read `references/pattern-catalog.md` before responding if you need the full "when to use / when NOT to use" detail for any specific pattern.
