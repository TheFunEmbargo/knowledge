* SOLID:
    * Single Responsibility Principle (SRP) – A class should have only one reason to change.
    * Open/Closed Principle (OCP) – Open for extension, closed for modification.
    * Liskov Substitution Principle (LSP) – Subtypes should be usable as their base type.
    * Interface Segregation Principle (ISP) – Don't force classes to implement unused methods.
    * Dependency Inversion Principle (DIP) – Depend on abstractions, not concrete implementations.
* DRY - Don't repeat yourself.
* KISS - Keep it simple stupid.
* Single Responsibility - Each class does one job and does it well.
* Separation of Concerns - MVC separate system architecture layers.
* Four Pillars:
    * Encapsulation - Make it a class.
    * Polymorphism - With a common interface (can be treated the same as other instances of the base class).
    * Inheritance - Reusing code.
    * Abstraction - Exposing only the required interface (hide internal methods).
* Law of Demeter - Decouple objects by only interacting with immediate dependencies (In class Car: `self.engine.get_fuel_system().inject_fuel()` ❌, `self.engine.start()`  ✅).
* Design Principals:
    ...
