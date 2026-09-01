---
type: best-practice
category: architecture
tags: [clean-architecture, solid, separation-of-concerns]
created: 2026-09-01
last-reviewed: 2026-09-01
---

# Clean Architecture

## Summary

Separate system layers clearly, with business logic at the center, depending on abstractions rather than implementations.

## Problem

Large systems with tangled dependencies are hard to change and hard to test.

## Solution

Use concentric circles architecture:
1. **Entities** — business objects
2. **Use Cases** — application logic
3. **Interface Adapters** — controllers, gateways
4. **Frameworks & Drivers** — UI, database, external APIs

Rule: Code in inner layers does not know about outer layers.

## Example

```
src/
├── domain/           # Entities + Use Cases
│   ├── entities/
│   └── use-cases/
├── application/      # Interface Adapters
│   ├── controllers/
│   └── gateways/
└── infrastructure/   # Frameworks & Drivers
    ├── database/
    └── web/
```

## References

- [Clean Architecture by Robert C. Martin](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Clean Code Book](https://www.oreilly.com/library/view/clean-code/9780136083238/)

## Related

- [[SOLID Principles]]
- [[Separation of Concerns]]
- [[Domain-Driven Design]]
