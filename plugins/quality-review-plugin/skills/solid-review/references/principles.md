# SOLID Principles — Detection Heuristics

Reference this file during analysis. For each principle, the heuristics describe what to look for
in source code to determine compliance, partial compliance, or violation.

---

## S — Single Responsibility Principle (SRP)

**Definition:** A class should have only one reason to change. It should encapsulate one cohesive
responsibility and delegate everything else.

### Signs of VIOLATION ❌
- A class name contains "And", "Manager", "Handler", "Util", "Helper", "Service" and does 5+ things
- Methods in the same class touch: database, HTTP, file I/O, business logic, and UI formatting
- More than ~200–300 lines in a single class (strong smell, not absolute)
- Constructor receives 5+ unrelated dependencies
- A single method does validation + persistence + email sending + logging
- God objects that know about the entire system

### Signs of PARTIAL COMPLIANCE ⚠️
- Class has one primary responsibility but also handles logging or minor formatting directly
- 2–3 responsibilities that are loosely related

### Signs of COMPLIANCE ✅
- Class name clearly expresses one concept (e.g., `InvoiceCalculator`, `EmailSender`, `UserRepository`)
- Methods are cohesive — they all operate on the same data/concept
- Dependencies in constructor are all related to the same concern

### What to quote
Quote the class declaration and its method signatures to show the breadth of responsibility.

---

## O — Open/Closed Principle (OCP)

**Definition:** Software entities should be open for extension but closed for modification.
New behavior should be addable without editing existing, working code.

### Signs of VIOLATION ❌
- `if/else` or `switch/match` chains that must grow every time a new type/variant is added:
  ```python
  if shape == "circle": ...
  elif shape == "square": ...
  elif shape == "triangle": ...  # new shapes require editing this method
  ```
- Type-checking with `isinstance`, `typeof`, or casting inside business logic
- Hard-coded lists of variants that need to be updated for each new case
- A method that must be changed every time a new payment method / report type / export format is added

### Signs of PARTIAL COMPLIANCE ⚠️
- Uses polymorphism in some places but still has conditional branches for special cases
- Abstract base class or interface exists but not all variants use it

### Signs of COMPLIANCE ✅
- Behavior is extended via inheritance or composition (strategy, template method, decorator pattern)
- New variants only require adding a new class, not editing existing ones
- Use of interfaces/abstract classes with polymorphic dispatch

### What to quote
Quote the if/else or switch block showing the pattern that would need to be edited for new cases.

---

## L — Liskov Substitution Principle (LSP)

**Definition:** Objects of a subclass must be substitutable for objects of the superclass without
breaking the program. A subclass must honor the contract of its parent.

### Signs of VIOLATION ❌
- Subclass overrides a method and throws `NotImplementedError` / `UnsupportedOperationException`
- Subclass removes or ignores a parameter that the parent uses
- Subclass strengthens preconditions (requires more from callers than parent does)
- Subclass weakens postconditions (returns less / does less than parent guarantees)
- Classic example: `Penguin extends Bird` where `Bird.fly()` exists but penguins can't fly
- Code in caller checks `instanceof` / `typeof` to decide how to call a method — signals LSP break
- Subclass method returns `None`/`null` where parent guarantees a value

### Signs of PARTIAL COMPLIANCE ⚠️
- Most of the contract is honored but one edge case is handled differently in the subclass
- Subclass adds behavior but still fulfills the parent contract

### Signs of COMPLIANCE ✅
- Every subclass can be used wherever the parent is used, with no special casing
- No `NotImplementedError` in overrides
- Inherited methods behave consistently with parent expectations

### What to quote
Quote the parent method signature and the overriding subclass method to show the contract break.

---

## I — Interface Segregation Principle (ISP)

**Definition:** Clients should not be forced to depend on interfaces they do not use.
Prefer many small, specific interfaces over one large general-purpose one.

### Signs of VIOLATION ❌
- Interface/abstract class with 7+ methods, not all of which are used by every implementor
- Implementors that define a method body as `pass`, `return None`, or `throw new NotImplementedError`
  just to satisfy the interface contract
- A single interface mixes concerns: reading + writing + formatting + authentication
- Class that depends on an interface but only calls 1–2 of its 10 methods

### Signs of PARTIAL COMPLIANCE ⚠️
- Interface is slightly too broad (2–3 unused methods per implementor) but not extreme
- Some stub implementations but not pervasive

### Signs of COMPLIANCE ✅
- Interfaces are narrow and role-specific (e.g., `Readable`, `Writable`, `Serializable` as separate)
- Every method in the interface is used by every implementor
- Clients only depend on the interface(s) they actually need

### What to quote
Quote the interface definition and an implementor that has empty/stub methods.

---

## D — Dependency Inversion Principle (DIP)

**Definition:** High-level modules should not depend on low-level modules. Both should depend on
abstractions. Abstractions should not depend on details; details should depend on abstractions.

### Signs of VIOLATION ❌
- `new ConcreteClass()` instantiated directly inside a high-level class method or constructor
- High-level class imports and depends on a specific DB driver, HTTP client, or third-party SDK
  directly (e.g., `import MySQLConnector`, `import requests` inside a domain class)
- No dependency injection: dependencies are created inside the class, not passed in
- Hard-coded configuration (URLs, credentials, class names) inside business logic
- `UserService` directly instantiates `MySQLUserRepository` instead of depending on `IUserRepository`

### Signs of PARTIAL COMPLIANCE ⚠️
- Some dependencies are injected but others are hard-coded inside the class
- Abstractions exist but are defined in the low-level module (wrong direction)

### Signs of COMPLIANCE ✅
- Constructor / method receives interfaces or abstract types, not concrete classes
- Concrete implementations are wired up in a composition root / factory / DI container
- High-level classes have no import of infrastructure-layer concrete modules
- Interfaces are defined by the high-level module or in a shared abstractions layer

### What to quote
Quote the constructor or method where the concrete dependency is instantiated directly.