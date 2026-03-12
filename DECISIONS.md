# Architecture Decision Records

> Each entry answers one question: **why did I decide this, and what would make me change my mind?**
> The _what_ lives in the code. This file captures the _thinking_.

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

## ADR-000 — Start with a monolith before splitting into microservices

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

## ADR-001 — Define domain boundaries

**Date:** 2026-03-01
**Phase:** Phase 1 — Monolith First
**Status:** Decided

### Context

Define domain boundaries for the multi-tenant e-commerce platform. These boundaries will drive the package-per-domain structure of the Spring Boot monolith.

| Domain | Responsibility | Key Note |
|---|---|---|
| tenant | Shop creation, config, subscription | Every other domain stores `tenant_id` locally — no runtime dependency on this service |
| identity | Authentication, authorization, user management | Cross-cutting concern; not a business domain — but needs a module |
| catalog | Product info, descriptions, pricing | Does NOT own stock. Change driver: shop owner edits |
| inventory | Stock levels, availability | Does NOT own product info. Change driver: orders and restocks |
| cart | Shopping cart, item selection | Different lifecycle from orders — can be abandoned, ephemeral |
| order | Order lifecycle (placed → fulfilled) | Starts after cart checkout. Triggers inventory reservation + payment |
| payment | Payment processing, refunds | Integrates with external payment providers |
| notification | Email/SMS for order events | Reacts to events from order, payment domains |
| reporting | Sales stats, financial tracking | Read-heavy; aggregates data from other domains |

Key decisions:

1. Catalog and Inventory are separate domains
2. Cart is separate from Order
3. Every domain stores `tenant_id` locally

### Why I decided this

1. Catalog and inventory have different change drivers: catalog changes when a shop owner edits product info; inventory changes when orders are placed.
2. Cart and order have different lifecycles and different failure tolerances — a lost cart is acceptable, a lost order is not.
3. Other domains depend on tenant context but should not call the Tenant service at runtime, keeping them functional if Tenant goes down.

### What I seriously considered instead

1. **Catalog and inventory as one domain** — a shop owner manages products, prices, and stock in one place. Rejected because stock is updated by orders, not just by owner edits.
2. **Cart and order as one domain** — they share similar data. Rejected because an order is a distinct entity with its own lifecycle that begins only after cart checkout.
3. **Letting other services call Tenant at runtime** — simpler to implement. Rejected because it creates a runtime dependency that breaks availability when Tenant is down.

### What would make me change my mind

1. If keeping catalog and inventory in sync becomes a significant maintenance burden — for example, if shop owners primarily do manual stock adjustments rather than order-driven updates.
2. If the platform constrains buyers to single-shop carts, the lifecycle difference shrinks enough that combining them may be simpler.
3. If a suspended tenant must be stopped immediately across all in-flight transactions, local `tenant_id` storage without runtime validation becomes a liability.

---

## ADR-002 — Single tenant per checkout
**Date:** 2026-03-12
**Phase:** Phase 1 — Monolith First
**Status:** Decided

### Context
This platform follows the Shopify model: each tenant operates an independent shop, and buyers purchase from one shop at a time. The cart is therefore scoped to a single tenant.

### Why I decided this
Supporting multi-tenant carts would require handling cross-shop packaging, shipping, and order tracking — or enforcing a checkout-per-shop constraint at the application level. Both add significant complexity. Single-tenant carts keep the checkout flow simple and unambiguous.

### What I seriously considered instead
Allowing buyers to add products from multiple shops into one cart and checkout across tenants — similar to Shopee or marketplace-style platforms.

### What would make me change my mind
Clear user demand for cross-shop purchasing — for example, consistent feedback or data showing buyers abandoning carts because they can't consolidate orders across shops.
