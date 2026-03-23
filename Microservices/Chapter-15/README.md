# Microservices Patterns — Quick Reference

---

## 1. API Gateway Pattern

Single entry point for all clients. Routes requests to the right service.

```
Client → API Gateway → Order Service
                     → Payment Service
                     → User Service
```

Handles: routing, auth, rate limiting, SSL termination, request transformation, load balancing. Clients never call services directly.

Tools: Spring Cloud Gateway, Kong, AWS API Gateway, NGINX.

---

## 2. Saga Pattern

Manages distributed transactions across services without 2PC. Sequence of local transactions + compensating actions on failure.

```
Happy: Create Order → Charge Payment → Reserve Inventory → ✓
Fail:  Create Order → Charge Payment → Reserve FAILS
         → Refund Payment (compensate) → Cancel Order (compensate)
```

| Type | How | Best for |
|------|-----|----------|
| Choreography | Services publish events, next service reacts | Simple flows (2-3 steps) |
| Orchestration | Central orchestrator coordinates steps | Complex flows (5+ steps) |

---

## 3. Service Registry Pattern

Services register themselves, callers look up instances dynamically. Solves the "IPs change constantly" problem.

```
Service starts → registers with registry (name, IP, port)
Caller → queries registry → gets healthy instances → calls one
```

| Tool | Type |
|------|------|
| Eureka | AP, Spring Cloud native |
| Consul | CP, multi-DC, config + discovery |
| K8s DNS | Built-in, no extra tools |

---

## 4. CQRS Pattern (Command Query Responsibility Segregation)

Separate write model from read model. Different DBs optimized for each.

```
Command (write) → Order Service → Order DB (normalized, ACID)
  ↓ publishes events
Query (read) → Read Service → Read DB (denormalized, fast queries)
```

Write side: handles business logic, validation, consistency. Read side: pre-joined, denormalized views optimized for queries. Synced via events (eventual consistency).

Use when read and write patterns differ significantly. Skip for simple CRUD.

---

## 5. Database Per Microservice Pattern

Each service owns its database. No shared DB. Other services access data via APIs or events.

```
Order Service   → Order DB (PostgreSQL)
Payment Service → Payment DB (PostgreSQL)
Search Service  → Search DB (Elasticsearch)
```

- Enables independent deploy, scale, schema changes
- Enables polyglot persistence (right DB for the right job)
- Trade-off: no cross-service JOINs, eventual consistency between services

---

## 6. Event Sourcing Pattern

Store every state change as an immutable event. Current state = replay of all events.

Traditional: store current state

```
orders table: { id: 123, status: "SHIPPED", total: 50 }
```

Event sourcing: store events

```
OrderCreated { id: 123, total: 50 }
PaymentReceived { id: 123, amount: 50 }
OrderShipped { id: 123, trackingId: "XYZ" }
```

Current state = replay all events → `{ id: 123, status: "SHIPPED", total: 50 }`

| Benefit | Why |
|---------|-----|
| Full audit trail | Every change recorded, nothing lost |
| Replay | Rebuild state, fix bugs, add new projections |
| Temporal queries | "What was the order status at 3pm yesterday?" |
| Event-driven | Natural fit with Kafka, CQRS |

| Trade-off | Why |
|-----------|-----|
| Complexity | Rebuilding state from events is harder than reading a row |
| Storage | Events grow forever (snapshots help) |
| Eventual consistency | Read models lag behind writes |

Often paired with CQRS: events are the write side (source of truth), read models are projections built from events.

Use when: audit trail is critical, need replay/reprocessing, complex domain with many state transitions. Skip when: simple CRUD, no need for history.

---

## 7. Strangler Fig Pattern

Incrementally migrate from monolith to microservices. No big-bang rewrite.

```
Before:  Client → Monolith (everything)

During:  Client → API Gateway
           ├── /orders   → New Order Service ✓ (migrated)
           ├── /payments → New Payment Service ✓ (migrated)
           └── /rest     → Monolith (shrinking)

After:   Client → API Gateway → All Microservices (monolith gone)
```

Rules:

- New features → build as microservices, never add to monolith
- Migrate one module at a time (start with the most painful)
- Keep monolith working at every step
- Migrate data gradually (dual writes or CDC)

Named after a vine that slowly wraps around a tree and replaces it.

---

## All patterns together

```
Client → API Gateway (gateway pattern)
  → Service Registry (discovery pattern)
  → Order Service → Order DB (database per service)
    → events → Event Store (event sourcing)
    → events → Read Model (CQRS)
  → Payment Service → Payment DB
    → Saga (distributed transaction)
  Legacy Monolith (strangler fig — shrinking)
```

> These patterns aren't used in isolation — they complement each other. Pick the ones that solve your specific problems.
