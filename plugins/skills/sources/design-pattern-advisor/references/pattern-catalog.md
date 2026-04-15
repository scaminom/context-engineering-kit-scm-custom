# Pattern Catalog Reference

Source: https://github.com/kamranahmedse/design-patterns-for-humans  
All 23 GoF patterns — intent, when to use, when NOT to use, key signals.

---

## Table of Contents

**Creational:** Simple Factory · Factory Method · Abstract Factory · Builder · Prototype · Singleton  
**Structural:** Adapter · Bridge · Composite · Decorator · Facade · Flyweight · Proxy  
**Behavioral:** Chain of Responsibility · Command · Iterator · Mediator · Memento · Observer · Visitor · Strategy · State · Template Method

---

# CREATIONAL PATTERNS
> Focused on how objects are instantiated.

---

## Simple Factory
**Intent:** Centralize object creation logic behind a static method. Not an official GoF pattern but widely used.

**Use when:**
- Creating an object requires logic beyond `new ClassName()`
- You want to avoid repeating construction logic in multiple places
- The client doesn't care which concrete class it gets

**Don't use when:**
- Creation is trivial (just `new`)
- You need the creation method to be overridable — use Factory Method instead

**Key signal:** "I keep writing the same `if/switch` to decide which class to instantiate"

---

## Factory Method
**Intent:** Define an interface for creating an object, but let subclasses decide which class to instantiate.

**Use when:**
- A class can't anticipate the type of objects it needs to create
- Subclasses should control object creation
- You want to provide a hook for subclasses to extend

**Don't use when:**
- You don't have (and don't need) a class hierarchy — Simple Factory is enough
- The variation is runtime data, not subclass behavior — use Strategy or Abstract Factory

**Key signal:** "I have a base class doing most of the work, but the *type* of object it creates varies by subclass"

---

## Abstract Factory
**Intent:** Create families of related objects without specifying their concrete classes.

**Use when:**
- The system needs to be independent of how its products are created
- You need to enforce that objects from the same "family" are used together
- You're switching between product families (e.g., UI themes, DB drivers per environment)

**Don't use when:**
- Products are unrelated — just use multiple independent factories
- You only have one product type — Factory Method is simpler

**Key signal:** "I need a `WoodenDoor` with a `Carpenter`, or an `IronDoor` with a `Welder` — the combination must be consistent"

---

## Builder
**Intent:** Construct complex objects step by step, separating construction from representation.

**Use when:**
- An object requires many optional or ordered configuration steps
- You want to avoid telescoping constructors (`new Foo(a, b, null, null, true, false, ...)`)
- The same construction process should produce different representations

**Don't use when:**
- The object is simple — a constructor or factory is cleaner
- All parameters are required — Builder adds ceremony without benefit

**Key signal:** "My constructor has 6+ parameters, half of them optional"

---

## Prototype
**Intent:** Create new objects by cloning an existing prototype.

**Use when:**
- Object creation is expensive (DB calls, heavy computation) and copies are cheaper
- You need objects similar to existing ones with minor modifications
- Classes to instantiate are specified at runtime

**Don't use when:**
- Objects have circular references (cloning gets complex)
- Creation cost is negligible

**Key signal:** "I need a new object that's almost identical to this existing one"

---

## Singleton
**Intent:** Ensure a class has only one instance and provide a global access point.

**Use when:**
- Exactly one object is needed to coordinate actions (logger, config manager, connection pool)
- Global state is genuinely required and intentional

**Don't use when (⚠️ anti-pattern risk):**
- You just want easy global access — use dependency injection instead
- It makes testing hard (mocking singletons is painful)
- Multiple instances are needed in tests but not in prod

**Key signal:** "There must be one and only one instance of X in the entire system"  
**Warning:** If the real reason is "it's convenient to access from anywhere," that's a design smell.

---

# STRUCTURAL PATTERNS
> Focused on composing classes and objects into larger structures.

---

## Adapter
**Intent:** Make an incompatible interface compatible with what the client expects, without changing either.

**Use when:**
- You want to use an existing class but its interface doesn't match
- You're integrating third-party or legacy code
- You need to reuse classes that don't share a common interface

**Don't use when:**
- You control both sides — just change one of them
- The interfaces are so different that adapting creates a misleading abstraction

**Key signal:** "I have a `WildDog` with `.bark()` but my system expects `.roar()`"

---

## Bridge
**Intent:** Decouple an abstraction from its implementation so both can vary independently.

**Use when:**
- You want to avoid a permanent binding between abstraction and implementation
- Both abstraction and implementation should be extensible via subclassing
- Changes in implementation should not affect clients

**Don't use when:**
- You only have one implementation — adds unnecessary indirection
- The abstraction and implementation don't actually need to vary independently

**Key signal:** "I have M pages × N themes = M×N subclasses. I need to separate page from theme."

---

## Composite
**Intent:** Compose objects into tree structures to represent part-whole hierarchies. Treat individual objects and compositions uniformly.

**Use when:**
- You have a tree structure (file system, org chart, UI components, menus)
- Clients should treat leaf nodes and containers the same way

**Don't use when:**
- Your hierarchy is simple and uniform treatment isn't needed
- Type safety matters more than uniformity (composite sacrifices some type safety)

**Key signal:** "I need to treat a single item and a group of items the same way"

---

## Decorator
**Intent:** Attach additional responsibilities to an object dynamically without changing its class.

**Use when:**
- You need to add behavior to individual objects, not an entire class
- Subclassing would lead to a class explosion
- Behaviors need to be added/removed at runtime

**Don't use when:**
- The behavior is always present — put it in the base class
- The order of decorators matters in unpredictable ways (can become confusing)

**Key signal:** "I want `SimpleCoffee` + milk + whip + vanilla, combinable in any order"

---

## Facade
**Intent:** Provide a simplified interface to a complex subsystem.

**Use when:**
- A subsystem is complex and clients only need a subset of its functionality
- You want to layer your subsystem (provide entry points per layer)
- Reduce dependencies between clients and subsystem internals

**Don't use when:**
- The simplified interface hides too much, making the facade a bottleneck
- Clients legitimately need full access to the subsystem

**Key signal:** "My clients have to call 7 different methods in the right order — I want a single `turnOn()`"

---

## Flyweight
**Intent:** Use sharing to efficiently support a large number of fine-grained objects.

**Use when:**
- You need a huge number of similar objects (thousands+)
- Object state can be split into intrinsic (shared) and extrinsic (per-instance)
- Memory is a genuine concern

**Don't use when:**
- You don't have a large number of objects
- Objects have mostly unique state — sharing doesn't help

**Key signal:** "I'm creating 10,000 particle objects and they're killing my memory"

---

## Proxy
**Intent:** Provide a surrogate or placeholder for another object to control access to it.

**Use when:**
- Lazy initialization (virtual proxy): create expensive objects only when needed
- Access control (protection proxy): check permissions before delegating
- Logging / caching (smart reference): add behavior around method calls
- Remote proxy: represent an object in a different address space

**Don't use when:**
- The overhead of indirection isn't justified
- Decorator fits better (Decorator adds behavior; Proxy controls access)

**Key signal:** "I want to add logging/caching/auth checks around an object without changing the object itself"

---

# BEHAVIORAL PATTERNS
> Focused on communication and responsibility between objects.

---

## Chain of Responsibility
**Intent:** Pass a request along a chain of handlers until one of them handles it.

**Use when:**
- More than one object may handle a request, and the handler isn't known a priori
- You want to issue a request to one of several objects without specifying the receiver explicitly
- The set of objects that can handle a request should be specified dynamically

**Don't use when:**
- Every request must be handled — use a more deterministic approach
- The chain is rarely more than one or two links

**Key signal:** "Try payment with Bank → if insufficient, try PayPal → if insufficient, try Bitcoin"

---

## Command
**Intent:** Encapsulate a request as an object, allowing parameterization, queuing, logging, and undo.

**Use when:**
- You need undo/redo functionality
- You need to queue, log, or schedule operations
- You want to decouple the object that invokes an operation from the object that performs it
- You need to support transactional behavior

**Don't use when:**
- The operation is simple and undo isn't needed — just call the method
- The indirection adds complexity without real benefit

**Key signal:** "I need to support undo, or I need to queue/schedule operations for later execution"

---

## Iterator
**Intent:** Provide a way to sequentially access elements of a collection without exposing its internal structure.

**Use when:**
- You want a standard way to traverse different types of collections
- You want to hide the complexity of traversal logic from the client

**Don't use when:**
- You're using a modern language with built-in iteration — the pattern is already baked in
- The collection is trivially simple

**Key signal:** "I have a custom data structure and I want `for...of` / `foreach` to work on it"

---

## Mediator
**Intent:** Define an object that encapsulates how a set of objects interact, reducing direct dependencies between them.

**Use when:**
- A set of objects communicate in well-defined but complex ways
- Reusing an object is difficult because it has too many references to other objects
- You want to customize behavior distributed across multiple classes without subclassing

**Don't use when:**
- The mediator itself becomes a "god object" that knows too much
- Objects only communicate in simple pairs

**Key signal:** "My chat users know about each other directly. I want them to only talk through a ChatRoom."

---

## Memento
**Intent:** Capture and externalize an object's internal state so it can be restored later, without violating encapsulation.

**Use when:**
- You need undo/redo or snapshot functionality
- Direct access to the object's state would expose implementation details

**Don't use when:**
- Saving state is expensive and undo depth is deep — consider delta-based approaches
- The state is already externalized (e.g., persisted to DB)

**Key signal:** "I need a 'save point' I can roll back to — like Ctrl+Z in a text editor"

---

## Observer
**Intent:** Define a one-to-many dependency so that when one object changes state, all dependents are notified automatically.

**Use when:**
- A change to one object requires changing others, and you don't know how many
- An object should be able to notify others without knowing who they are
- You're implementing an event system, pub/sub, or reactive updates

**Don't use when:**
- Observers are tightly coupled to the subject in practice
- Notification chains become hard to trace (cascade updates)
- Performance is critical and notification overhead matters

**Key signal:** "When a job is posted, all subscribed job seekers should be notified automatically"

---

## Visitor
**Intent:** Add new operations to existing object structures without modifying the classes of those elements.

**Use when:**
- You need to perform many distinct and unrelated operations on an object structure
- The object structure classes rarely change, but you often add new operations
- You want to gather related operations and separate them from the objects

**Don't use when:**
- The object structure changes frequently — adding a new element type requires updating every visitor
- The operations are simple enough to put in the classes themselves

**Key signal:** "I have a stable set of Animal classes and I keep adding new operations (speak, jump, eat) — I don't want to touch Animal every time"

---

## Strategy
**Intent:** Define a family of algorithms, encapsulate each one, and make them interchangeable.

**Use when:**
- You need to switch between different algorithms or behaviors at runtime
- You have multiple versions of an algorithm and want to avoid conditionals
- A class has many behaviors that appear as multiple conditional statements

**Don't use when:**
- You only have one algorithm — no need to abstract it
- The algorithm never changes at runtime — just hard-code it
- Clients don't need to be aware of different strategies

**Key signal:** "I use bubble sort for small datasets and quick sort for large ones — I want to swap them cleanly"

---

## State
**Intent:** Allow an object to alter its behavior when its internal state changes, appearing to change its class.

**Use when:**
- An object's behavior depends on its state and must change at runtime
- Operations contain large conditional statements that depend on the object's state

**Don't use when:**
- There are only two states and the logic is simple — use a boolean flag
- States and transitions are highly dynamic and don't have a fixed structure

**Key signal:** "My `Phone` object behaves completely differently depending on whether it's idle, ringing, or in a call"

**State vs. Strategy:** Strategy changes the algorithm *from outside* (client picks the strategy). State changes behavior *from within* (the object transitions itself).

---

## Template Method
**Intent:** Define the skeleton of an algorithm in a base class, deferring some steps to subclasses.

**Use when:**
- You have invariant parts of an algorithm that should not change
- Subclasses should be able to customize specific steps without changing the overall structure
- You want to avoid code duplication across subclasses that share a common flow

**Don't use when:**
- The algorithm has too many variation points — Strategy gives more flexibility
- Inheritance is being avoided in your architecture (Strategy uses composition instead)

**Key signal:** "All build pipelines run `test → lint → assemble → deploy`, but each platform implements the steps differently"

**Template Method vs. Strategy:** Template Method uses *inheritance* to vary steps; Strategy uses *composition* to swap whole algorithms.
