# Architecture Decision Records

> Each entry answers one question: **why did I decide this, and what would make me change my mind?**
> The *what* lives in the code. This file captures the *thinking*.

---

## ADR Template

```
## ADR-XXX — [Short title of the decision]
**Date:** YYYY-MM-DD
**Phase:** Phase N — Name
**Status:** Decided | Revisited | Superseded by ADR-XXX

### Context
What situation or problem forced this decision? What constraints were at play?

### Why I decided this
The core reasoning. What tipped the balance?

### What I seriously considered instead
Alternatives that were real contenders, and why they lost.

### What would make me change my mind
Conditions or new information that would invalidate this decision.
```

---

## ADR-001 — Start with a monolith before splitting into microservices
**Date:** <!-- date -->
**Phase:** Phase 1 — Monolith First
**Status:** Decided

### Context
The natural instinct when building a "microservices project" is to start with separate services from day one. But the domain boundaries aren't fully understood yet, and premature splitting locks in wrong decisions early.

### Why I decided this
Splitting a monolith is easier than merging two wrongly-separated services. Starting as a monolith forces real understanding of where domains couple naturally — that pain is information. The cost of a wrong service boundary compounds over time.

### What I seriously considered instead
**Starting with microservices immediately** — tempting because it's the end goal, and some tutorials start this way. Rejected because it encourages defining boundaries by assumption rather than by evidence.

### What would make me change my mind
If the domain model were already well understood from prior experience with this type of system, starting split would be reasonable.

---

<!-- Add new ADRs below as decisions are made throughout the project -->
