# Multi-Tenant E-Commerce Platform
### Architecture Learning Project

---

## 🎯 Project Goal

Build a **Shopify-like multi-tenant e-commerce platform** where multiple independent stores run on shared infrastructure. Each tenant has their own storefront, products, and orders — but shares the underlying platform services.

This project is designed as a **learning journey toward software architecture**, covering microservices design, event-driven systems, API gateway patterns, and DevOps practices.

---

## 👤 Profile

- **Background:** Full-stack engineer (Java Spring Boot + ReactJS), some DevOps experience
- **Current focus:** Mostly frontend in day job
- **Goal:** Grow into a Software Architect role

---

## 🗺️ Architecture Overview

### Domain Services

| Service | Responsibility | Database |
|---|---|---|
| `tenant-service` | Tenant registration, configuration, subscription | PostgreSQL |
| `identity-service` | Users, authentication, authorization (JWT/OAuth2) | PostgreSQL |
| `catalog-service` | Products, categories, variants per tenant | PostgreSQL |
| `inventory-service` | Stock levels, reservations per tenant | PostgreSQL |
| `order-service` | Cart, checkout, order lifecycle | PostgreSQL |
| `payment-service` | Payment intent, confirmation, refunds | PostgreSQL |
| `notification-service` | Email/push delivery (event consumer, stateless) | — |
| `storefront-service` | ReactJS frontend with per-tenant routing | — |

### Infrastructure Components

| Component | Technology | Purpose |
|---|---|---|
| API Gateway | Spring Cloud Gateway | Single entry point, auth filter, routing, rate limiting |
| Message Broker | Apache Kafka | Async inter-service communication |
| Containers | Docker + Docker Compose | Local development environment |
| Orchestration | Kubernetes | Production deployment |
| CI/CD | GitHub Actions | Automated test, build, deploy pipelines |

---

## 🔑 Key Architectural Principles

- **Database-per-service** — no shared databases between services, ever
- **Gateway owns cross-cutting concerns** — auth, rate limiting, tenant resolution
- **Async by default for state changes** — REST for queries, Kafka events for mutations
- **Domain boundaries before code** — model the domains first, split second

---

## 📅 Phased Roadmap

### Phase 1 — Monolith First *(2–3 weeks)*

Start with a single Spring Boot application with clearly separated packages mirroring the future services.

**Goals:**
- Define domain boundaries before any splitting
- Build the core data model (tenants, users, products, orders)
- Understand where coupling naturally wants to form

**Key question to answer:** What is your criteria for deciding where one service ends and another begins?

---

### Phase 2 — Microservices Split *(3–4 weeks)*

Extract each domain package into its own independent Spring Boot service with its own database.

**Goals:**
- Enforce strict DB isolation (no cross-service DB queries)
- Decide sync vs async per interaction
- Implement tenant context propagation (`X-Tenant-ID` header)

**Key question to answer:** How does `order-service` know stock availability without tightly coupling to `inventory-service`?

---

### Phase 3 — API Gateway *(2 weeks)*

Introduce Spring Cloud Gateway as the single entry point for all client traffic.

**Goals:**
- Centralize JWT validation (remove it from individual services)
- Implement tenant resolution from subdomain (`store-a.yourplatform.com`)
- Add per-tenant rate limiting
- Route requests by path prefix to correct downstream service

**Key question to answer:** What is the right boundary between what the gateway handles vs. what each service handles?

---

### Phase 4 — Event-Driven Architecture with Kafka *(3–4 weeks)*

Replace synchronous service-to-service calls with domain events for non-immediate flows.

**Core event flows:**

```
OrderPlaced       → inventory-service  (reserve stock)
                  → notification-service (confirmation email)
                  → payment-service    (initiate charge)

PaymentConfirmed  → order-service      (update status → CONFIRMED)
                  → notification-service (receipt email)

PaymentFailed     → order-service      (update status → CANCELLED)
                  → inventory-service  (release stock reservation)
```

**Goals:**
- Implement the Saga pattern (choreography-based)
- Handle failure scenarios and partial completions
- Explore the Outbox Pattern to solve dual-write problems

**Key question to answer:** What happens if the Kafka consumer crashes halfway through processing `OrderPlaced`?

---

### Phase 5 — DevOps & Hosting *(3–4 weeks)*

**Containerization:**
- Dockerize every service
- Write `docker-compose.yml` for full local environment (services + Kafka + Postgres instances)

**Kubernetes:**
- Start with minikube locally
- Write `Deployment`, `Service`, and `Ingress` manifests per service
- Progress to Helm charts for reusable configuration
- Deploy to a cloud cluster (DigitalOcean / Hetzner for cost efficiency)

**CI/CD:**
- GitHub Actions pipeline per service: `test → build image → push to registry → deploy to K8s`
- Monorepo with path-based pipeline triggers (only rebuild changed services)

---

## 🧠 Learning Depth Guide

| Topic | Target Depth |
|---|---|
| Service boundary design | Deep — this is the core architectural skill |
| Saga / event-driven patterns | Deep — hands-on failure scenarios required |
| API Gateway patterns | Operational — configure and reason about tradeoffs |
| Kafka internals | Operational — enough to debug and tune |
| Kubernetes | Operational — deploy, scale, troubleshoot |
| Docker | Operational — comfortable, not expert |
| CI/CD pipelines | Conceptual + hands-on setup |

---

## 📚 Recommended Concepts to Study Alongside

- **Domain-Driven Design (DDD)** — Bounded Contexts map directly to your services
- **CAP Theorem** — foundational for understanding distributed system tradeoffs
- **Saga Pattern** — choreography vs orchestration
- **Outbox Pattern** — solving dual-write in event-driven systems
- **CQRS** — worth understanding even if not fully implemented here
- **12-Factor App** — principles your services should follow
