# Project Progress

> Last updated: <!-- date -->
> Current phase: Phase 1 — Monolith First

---

## Phase 1 — Monolith First
- [ ] Define domain boundaries on paper before any code
- [ ] Scaffold Spring Boot monolith with package-per-domain structure
- [ ] Data model: tenants, users, products, orders
- [ ] Basic CRUD per domain (no business logic yet)
- [ ] Identify coupling hotspots — where do domains want to bleed into each other?

## Phase 2 — Microservices Split
- [ ] Extract `tenant-service`
- [ ] Extract `identity-service`
- [ ] Extract `catalog-service`
- [ ] Extract `inventory-service`
- [ ] Extract `order-service`
- [ ] Extract `payment-service`
- [ ] Extract `notification-service`
- [ ] Enforce DB-per-service (no cross-service DB access)
- [ ] Implement `X-Tenant-ID` header propagation

## Phase 3 — API Gateway
- [ ] Set up Spring Cloud Gateway
- [ ] Centralize JWT validation in gateway filter
- [ ] Implement tenant resolution from subdomain
- [ ] Add per-tenant rate limiting
- [ ] Route by path prefix to downstream services

## Phase 4 — Event-Driven with Kafka
- [ ] Set up Kafka locally (docker-compose)
- [ ] Implement `OrderPlaced` event flow
- [ ] Implement `PaymentConfirmed` / `PaymentFailed` flows
- [ ] Handle consumer failure and idempotency
- [ ] Explore the Outbox Pattern

## Phase 5 — DevOps & Hosting
- [ ] Dockerize all services
- [ ] Write `docker-compose.yml` for full local environment
- [ ] Write Kubernetes manifests (Deployment, Service, Ingress)
- [ ] Deploy to local minikube
- [ ] Deploy to cloud cluster
- [ ] Set up GitHub Actions CI/CD pipeline per service
- [ ] Introduce Helm charts
