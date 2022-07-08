# Dependency Injection
Created: 2022-07-02 15:08

**Core principle:** separation of behavior from dependency resolution

**Purpose:** dependent object lets injector choosing concrete class implementations at run-time

**Definition**
Involves at least three elements:
- A dependent consumer: defines what component it depends on to do its work
- A declaration of a component's dependencies: defined as interface contract
- A injector (provider or container): creates instances of classes that implement a given dependency interface

**Benefits:**
- Reduction of boilerplate code: all works to initialize dependencies are handle by a provider component.
- Flexibility: alternative implementations of given interface can be used without recompiling code ==> useful in testing



## References
1.

---
tags:

#design-pattern
