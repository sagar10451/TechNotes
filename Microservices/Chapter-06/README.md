# Chapter-6: Data Management

## Contents

1. Database per Service
2. Distributed Transactions
3. Eventual Consistency
4. Configuration Management

---

## Database per Service

1. Each service owns its data (no shared database — this is the rule)
2. Other services access data only through APIs/events
3. Polyglot persistence (service A uses PostgreSQL, service B uses MongoDB, service C uses Redis)

Database per Service — each microservice owns its own database. No other service touches it directly. This is the foundational data rule in microservices.

### 1. Each Service Owns Its Data

Shared DB (monolith style — breaks microservices):

```
Order Service   ──→ ┌──────────┐
Payment Service ──→ │ Shared DB │ ← tight coupling, schema changes break everyone
User Service    ──→ └──────────┘
```

Database per Service (correct):

```
Order Service   → Order DB (PostgreSQL)
Payment Service → Payment DB (PostgreSQL)
User Service    → User DB (MongoDB)
```

Why no shared DB:

- Schema change in one service breaks others
- Can't deploy independently — DB migration couples all services
- Can't scale independently — one DB becomes the bottleneck
- No clear ownership — who's responsible for the orders table?
- Tight coupling disguised as microservices (distributed monolith)

### 2. Access Data Only Through APIs/Events

Need order data from Order Service? You don't query its DB. You call its API or listen to its events.

✗ Wrong:

```
Payment Service → SELECT * FROM orders WHERE id=123  (directly hitting Order DB)
```

✓ Right (API):

```
Payment Service → GET /orders/123 → Order Service → queries its own DB → returns response
```

✓ Right (Event):

```
Order Service → publishes "OrderPlaced" event → Kafka
Payment Service → consumes event → stores what it needs in its own DB
```

| Method | When |
|--------|------|
| Sync API call | Need data now, real-time |
| Async event | Can tolerate slight delay, want decoupling |
| Data duplication | Service stores a local copy of data it frequently needs (via events) |

Data duplication is OK and expected — Payment Service might store orderId + amount locally instead of calling Order Service every time. Events keep it in sync (eventual consistency).

### 3. Polyglot Persistence

Each service picks the best database for its specific needs. No one-size-fits-all.

| Service | DB | Why |
|---------|-----|-----|
| Order Service | PostgreSQL | Relational data, transactions, ACID |
| Product Catalog | MongoDB | Flexible schema, nested documents, varied product attributes |
| Session / Cache | Redis | Fast in-memory reads, TTL-based expiry |
| Search Service | Elasticsearch | Full-text search, fuzzy matching |
| Analytics | ClickHouse / Cassandra | High write throughput, time-series, columnar |
| User Relationships | Neo4j | Graph data, friend-of-friend queries |

```
Order Service     → PostgreSQL  (relational, ACID)
Product Service   → MongoDB     (flexible schema)
Search Service    → Elasticsearch (full-text search)
Session Service   → Redis       (fast, ephemeral)
Analytics Service → ClickHouse  (columnar, aggregations)
```

Each team picks what fits. No committee deciding "we're a PostgreSQL shop."

### Trade-offs of database per service

| Pro | Con |
|-----|-----|
| Independent deploy, scale, schema changes | Cross-service queries are hard (no JOINs) |
| Right DB for the right job | Data consistency is eventual, not immediate |
| Clear ownership | Data duplication across services |
| Fault isolation — one DB down ≠ all down | More databases to operate and monitor |

Cross-service queries without shared DB:

- API Composition — gateway calls multiple services, merges responses
- CQRS — build a read-optimized view from events
- Events + local copy — each service stores what it needs

> The rule is simple: your service, your database, your schema. Everyone else talks to you through your API or listens to your events. No exceptions.

---

## Distributed Transactions

1. Why 2PC (two-phase commit) doesn't work in microservices (tight coupling, single point of failure, latency)
2. Saga pattern:
   - Choreography: each service publishes events, next service reacts (simple, but hard to track flow)
   - Orchestration: central orchestrator coordinates steps (easier to understand, single point of control)
   - Compensating transactions (undo previous steps on failure — e.g., refund payment if shipping fails)
3. Outbox pattern:
   - Write to DB + outbox table in same local transaction
   - Separate process reads outbox and publishes events
   - Guarantees at-least-once delivery without distributed transactions
4. Change Data Capture (CDC): Debezium reads DB transaction log → publishes events to Kafka

Distributed Transactions — how to maintain data consistency across multiple services, each with its own database.

### 1. Why 2PC Doesn't Work in Microservices

Two-Phase Commit: a coordinator asks all participants to prepare, then commit.

```
Coordinator → "Prepare" → Order DB ✓, Payment DB ✓, Inventory DB ✓
Coordinator → "Commit"  → all commit together
```

Problems:

| Issue | Why it kills microservices |
|-------|--------------------------|
| Tight coupling | All services must be available simultaneously — one down = all blocked |
| Single point of failure | Coordinator dies mid-transaction = all participants stuck holding locks |
| Latency | All DBs locked until slowest participant responds |
| Doesn't scale | Holding locks across network = terrible throughput |
| Breaks autonomy | Services can't deploy/scale independently if they're in a 2PC |

2PC works within a single database or tightly coupled systems. Not across independent microservices.

### 2. Saga Pattern

Replace one big transaction with a sequence of local transactions + compensating actions on failure.

Each service does its own local transaction. If a step fails, previous steps are undone via compensation.

Happy path:

```
Order Service: create order → Payment Service: charge → Inventory: reserve → ✓ done
```

Failure + compensation:

```
Order Service: create order → Payment Service: charge → Inventory: reserve FAILS
  → compensate: Payment Service: refund → Order Service: cancel order
```

#### Choreography (event-driven, no coordinator)

Each service publishes an event, next service reacts.

```
Order Service → "OrderCreated" →
Payment Service → charges → "PaymentCompleted" →
Inventory Service → reserves → "InventoryReserved" →
done ✓
```

Failure:

```
Inventory Service → "ReservationFailed" →
Payment Service → refund → "PaymentRefunded" →
Order Service → cancel
```

| Pro | Con |
|-----|-----|
| Fully decoupled, no coordinator | Hard to track the full flow |
| Simple for 2-3 steps | Complex with many steps (spaghetti events) |
| No single point of failure | Debugging is painful — trace events across services |

#### Orchestration (central coordinator)

An orchestrator service tells each service what to do and handles failures.

```
Order Saga Orchestrator:
  1. → Order Service: "create order" → ✓
  2. → Payment Service: "charge" → ✓
  3. → Inventory Service: "reserve" → ✗ FAILED
  4. → Payment Service: "refund" (compensate)
  5. → Order Service: "cancel" (compensate)
```

| Pro | Con |
|-----|-----|
| Easy to understand — flow is in one place | Orchestrator is a single point of control |
| Easy to add/change steps | Can become a god service if not careful |
| Better for complex flows (5+ steps) | Slightly more coupling (orchestrator knows services) |

#### Compensating Transactions

The "undo" for each step. Not a rollback — a new action that reverses the effect.

| Step | Action | Compensation |
|------|--------|-------------|
| Create order | INSERT order | Cancel order (set status=CANCELLED) |
| Charge payment | Debit $50 | Refund $50 |
| Reserve inventory | Decrement stock | Increment stock back |
| Send email | Send confirmation | Send cancellation email |

Not all actions are perfectly reversible (can't unsend an email). Design compensations to handle this (send a follow-up cancellation).

### 3. Outbox Pattern

Problem: service updates its DB and publishes an event — but what if DB commits and event publish fails? Or event publishes but DB rolls back? Data is inconsistent.

✗ Broken:

```
1. UPDATE orders SET status='CONFIRMED'  → ✓ committed
2. publish "OrderConfirmed" to Kafka     → ✗ failed (Kafka down)
→ DB updated but no event published — other services never know
```

Solution: write the event to an outbox table in the same DB transaction. A separate process reads the outbox and publishes to Kafka.

✓ Outbox:

```
BEGIN TRANSACTION
  1. UPDATE orders SET status='CONFIRMED'
  2. INSERT INTO outbox (event_type, payload) VALUES ('OrderConfirmed', '{...}')
COMMIT
→ both succeed or both fail (local ACID transaction)
```

Separate process (poller or CDC):

```
→ reads outbox table → publishes to Kafka → marks as published
```

```
Orders Table          Outbox Table
┌─────────────┐      ┌──────────────────────────┐
│ id: 123     │      │ id: 1                    │
│ status: OK  │      │ event: OrderConfirmed    │
└─────────────┘      │ payload: { orderId: 123 }│
 (same transaction)  │ published: false          │
                     └──────────────────────────┘
                       ↓ poller reads
                       ↓ publishes to Kafka
                       ↓ marks published: true
```

Guarantees at-least-once delivery. Consumers must be idempotent (same event processed twice = no side effect).

### 4. Change Data Capture (CDC)

Instead of polling the outbox table, read the database's transaction log directly and stream changes to Kafka.

```
Service writes to DB → DB transaction log (WAL/binlog) → Debezium → Kafka topic
```

Debezium (most popular CDC tool) watches the DB log and publishes every INSERT/UPDATE/DELETE as an event to Kafka. No polling, no outbox table needed (though outbox + CDC is a common combo).

```
PostgreSQL WAL / MySQL binlog
  → Debezium connector
  → Kafka topic: "db.orders"
  → { "op": "u", "before": {...}, "after": { "status": "CONFIRMED" } }
```

| Approach | How | Trade-off |
|----------|-----|-----------|
| Outbox + poller | Poll outbox table periodically | Simple, slight delay, DB load from polling |
| Outbox + CDC | Debezium reads outbox table from DB log | No polling overhead, near real-time |
| CDC directly | Debezium streams all DB changes | No code changes needed, but exposes internal schema |

CDC is also used for syncing data to search indexes (DB → Kafka → Elasticsearch), building read models, and data lake ingestion.

---

## Eventual Consistency

1. Accept that data across services won't be immediately consistent
2. Design for it: idempotent consumers, reconciliation jobs, conflict resolution
3. CQRS (Command Query Responsibility Segregation): separate write model from read model, sync via events

Eventual Consistency — in microservices with separate databases, data won't be in sync instantly. It will be consistent eventually, after events propagate.

### 1. Accept It — Immediate Consistency Isn't Possible Across Services

Monolith (single DB):

```
BEGIN → update order → update inventory → COMMIT → instantly consistent ✓
```

Microservices (separate DBs):

```
Order Service → updates Order DB → publishes "OrderPlaced"
Inventory Service → receives event (50ms-5s later) → updates Inventory DB
→ for a brief window, order exists but inventory isn't reserved yet
```

This gap is normal and expected. You design around it, not against it.

| Consistency model | Where |
|-------------------|-------|
| Strong (immediate) | Within a single service/database |
| Eventual | Across services |

Trying to force strong consistency across services = 2PC = tight coupling = defeats the purpose of microservices.

### 2. Design for It

#### Idempotent consumers

Events may be delivered more than once (at-least-once delivery). Processing the same event twice must have no side effect.

```
Event: "OrderPlaced { orderId: 123 }" delivered twice
✗ Not idempotent: INSERT INTO reservations → creates 2 reservations
✓ Idempotent: INSERT ... ON CONFLICT (orderId) DO NOTHING → safe
```

Techniques: unique constraint on event ID, check-before-write, upsert.

#### Reconciliation jobs

Scheduled jobs that compare data across services and fix inconsistencies.

```
Every hour:
  Compare Order DB orders (status=CONFIRMED) vs Inventory DB reservations
  Missing reservation? → create it
  Orphaned reservation? → release it
```

Safety net for when events are lost, delayed, or processed out of order. Not the primary mechanism — just a backup.

#### Conflict resolution

When two services update related data concurrently, conflicts can happen.

| Strategy | How |
|----------|-----|
| Last-write-wins | Latest timestamp wins (simple, can lose data) |
| Version vectors | Track versions per service, detect conflicts |
| Merge logic | Business rules decide (e.g., higher bid wins) |
| Manual resolution | Flag for human review (rare, critical cases) |

### 3. CQRS (Command Query Responsibility Segregation)

Separate the write model (commands) from the read model (queries). Sync them via events.

Without CQRS:

- Same DB handles writes AND reads
- Complex queries slow down writes, writes lock reads

With CQRS:

```
Write side → Command → Order Service → Order DB (normalized, optimized for writes)
  ↓ publishes events
Read side → Query → Read Service → Read DB (denormalized, optimized for queries)
```

User places order (command):

```
→ Order Service → INSERT into Order DB → publish "OrderPlaced"
```

User views order summary (query):

```
→ Read Service → SELECT from Read DB (pre-joined, denormalized, fast)
```

Event sync:

```
"OrderPlaced" → Read Service → updates Read DB with denormalized view
"PaymentCompleted" → Read Service → updates order status in Read DB
"InventoryReserved" → Read Service → adds item details to Read DB
```

| Aspect | Write Model | Read Model |
|--------|-------------|------------|
| Optimized for | Writes, validation, business rules | Fast reads, complex queries |
| Schema | Normalized | Denormalized (pre-joined) |
| DB choice | PostgreSQL (ACID) | Elasticsearch, MongoDB, Redis |
| Updated by | Commands (user actions) | Events (async from write side) |
| Consistency | Immediate (within itself) | Eventual (lags behind write side) |

When to use CQRS:

- Read and write patterns are very different
- Read-heavy system needing fast, complex queries
- Multiple services' data needs to be queried together (build a combined read model from events)

When to skip:

- Simple CRUD — CQRS adds unnecessary complexity
- Read/write patterns are similar
- Team is small and doesn't need the operational overhead

> CQRS + Event Sourcing is a common combo: events are the source of truth (write side), read models are projections built from those events. Powerful but complex — use only when the problem demands it.

---

## Configuration Management

1. Externalized config (12-factor app principle)
2. Spring Cloud Config Server (Git-backed, centralized)
3. Kubernetes ConfigMaps and Secrets
4. Environment-specific config (dev, staging, prod)
5. Feature flags (LaunchDarkly, Unleash, or simple DB-backed flags)
6. Secret management (Vault, AWS Secrets Manager — never in code/config files)

Configuration Management — how to manage config across multiple services and environments without hardcoding or redeploying.

### 1. Externalized Config (12-Factor App)

Config (DB URLs, API keys, feature flags, timeouts) lives outside the code. Same artifact runs in dev, staging, prod — only config changes.

✗ Hardcoded:

```java
String dbUrl = "jdbc:postgresql://prod-db:5432/orders";  // in code, needs rebuild to change
```

✓ Externalized:

```java
String dbUrl = env.getProperty("DB_URL");  // injected at runtime
```

12-factor rule: strict separation of config from code. Config varies between deploys, code doesn't.

Sources (in order of precedence in Spring):

```
Command line args > Environment variables > application-{profile}.yml > application.yml > Config Server
```

### 2. Spring Cloud Config Server

Centralized config server backed by a Git repo. All services fetch config from one place.

```
Git Repo (config files):
├── order-service.yml
├── order-service-prod.yml
├── payment-service.yml
└── payment-service-prod.yml
  ↓
Config Server (http://config-server:8888)
  ↓
Order Service → fetches config on startup
Payment Service → fetches config on startup
```

- Change config in Git → commit → services pick up changes (with `@RefreshScope` + `/actuator/refresh` or Spring Cloud Bus for auto-refresh)
- Version history via Git — who changed what, when, rollback easily
- Encryption support for sensitive values

```yaml
# bootstrap.yml in each service
spring:
  cloud:
    config:
      uri: http://config-server:8888
  application:
    name: order-service
  profiles:
    active: prod
# fetches: config-server/order-service/prod
```

### 3. Kubernetes ConfigMaps and Secrets

On K8s, config is managed natively — no need for Spring Cloud Config Server.

ConfigMap — non-sensitive config:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: order-config
data:
  DB_HOST: "order-db.default.svc.cluster.local"
  CACHE_TTL: "300"
  LOG_LEVEL: "INFO"
```

Secret — sensitive data (base64 encoded, encrypted at rest):

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: order-secrets
type: Opaque
data:
  DB_PASSWORD: cGFzc3dvcmQxMjM=    # base64
```

Injected as env vars or mounted as files:

```yaml
containers:
  - name: order-service
    envFrom:
      - configMapRef:
          name: order-config
      - secretRef:
          name: order-secrets
```

### 4. Environment-Specific Config

Different values per environment, same application code.

Spring profiles:

```
application.yml          → shared defaults
application-dev.yml      → dev overrides (local DB, debug logging)
application-staging.yml  → staging overrides
application-prod.yml     → prod overrides (prod DB, INFO logging, real API keys)
```

Activate: `SPRING_PROFILES_ACTIVE=prod`

K8s approach: different ConfigMaps/Secrets per namespace.

```
Namespace: dev     → ConfigMap with dev values
Namespace: staging → ConfigMap with staging values
Namespace: prod    → ConfigMap with prod values
```

### 5. Feature Flags

Toggle features on/off without deploying. Decouple deployment from release.

```java
if (featureFlags.isEnabled("new-checkout-flow")) {
    return newCheckoutFlow(order);
} else {
    return legacyCheckoutFlow(order);
}
```

| Tool | Type |
|------|------|
| LaunchDarkly | SaaS, full-featured, targeting, A/B testing |
| Unleash | Open-source, self-hosted |
| DB-backed flags | Simple table: feature_name, enabled, percentage |
| Spring Cloud Config | Config property toggle (basic) |

Use cases: canary releases, A/B testing, kill switch for broken features, gradual rollout (enable for 10% → 50% → 100%).

### 6. Secret Management

Secrets (DB passwords, API keys, tokens) must never be in code, Git, or plain config files.

- ✗ Wrong: `application.yml` → `db.password=mysecretpassword` (committed to Git)
- ✗ Wrong: environment variable in Dockerfile
- ✓ Right: fetched at runtime from a secret manager

| Tool | Environment |
|------|-------------|
| HashiCorp Vault | Any (self-hosted, most powerful) |
| AWS Secrets Manager | AWS |
| AWS Parameter Store | AWS (simpler, cheaper) |
| K8s Secrets | Kubernetes (basic, base64 not encryption) |
| Azure Key Vault | Azure |
| GCP Secret Manager | GCP |

Vault example flow:

```
Service starts → authenticates with Vault (via K8s service account / IAM role)
  → requests secret: "database/creds/order-service"
  → Vault returns dynamic credentials (auto-rotated)
  → service uses them, credentials expire after TTL
```

K8s Secrets are base64 encoded, not encrypted by default. For production, combine with Vault or cloud secret managers for encryption, rotation, and audit logging.
