# Microservices

---

## Fundamentals — Why Microservices

1. Monolith vs Microservices vs SOA (trade-offs, not just "microservices good")
2. When NOT to use microservices (small team, early-stage product, unclear domain boundaries)
3. Domain-Driven Design (DDD) — bounded contexts, ubiquitous language, aggregates, entities, value objects
4. How to decompose a monolith (by business capability, by subdomain, strangler fig pattern)
5. Service boundaries — how to decide what's one service vs two
6. Conway's Law (system design mirrors org structure — and you should use it intentionally)

---

## Inter-Service Communication

### Synchronous

1. REST (most common, HTTP/JSON, simple but coupling)
2. gRPC (Protocol Buffers, binary, fast, strongly typed, streaming support, great for internal service-to-service)
3. GraphQL (client-driven queries, BFF pattern, not ideal for service-to-service)
4. RestClient / WebClient / OpenFeign (Java/Spring options)
5. Timeouts, retries, circuit breakers on every sync call (non-negotiable)

### Asynchronous

1. Message queues (Kafka, RabbitMQ, SQS)
2. Event-driven architecture (events vs commands)
3. Pub/Sub pattern (fan-out, decoupling producers from consumers)
4. Kafka deep dive:
   - Topics, partitions, consumer groups, offsets
   - Ordering guarantees (per partition only)
   - At-least-once vs at-most-once vs exactly-once semantics
   - Compacted topics (for event sourcing / state)
   - Schema registry (Avro/Protobuf for contract enforcement)
5. When to use sync vs async:
   - Sync: need immediate response (user-facing, query)
   - Async: fire-and-forget, eventual consistency OK (notifications, analytics, order processing)

---

## API Gateway

1. What it does: routing, authentication, rate limiting, load balancing, request transformation, SSL termination
2. Spring Cloud Gateway (routes, predicates, filters)
3. Kong, NGINX, AWS API Gateway, Envoy (know at least one)
4. BFF pattern (Backend for Frontend — separate gateway per client type: web, mobile, internal)
5. API composition (gateway aggregates responses from multiple services)
6. Rate limiting at gateway level (token bucket, sliding window)

---

## Service Discovery

1. Why: services have dynamic IPs (containers, auto-scaling)
2. Client-side discovery: service queries registry, picks an instance (Eureka + Spring Cloud LoadBalancer)
3. Server-side discovery: load balancer queries registry (AWS ALB, Kubernetes Service)
4. Service registry: Eureka, Consul, Zookeeper
5. Kubernetes DNS-based discovery (service name resolves to cluster IP — no separate registry needed)
6. Health checks and deregistration of unhealthy instances

---

## Resilience Patterns (Critical for SDE-3)

### Circuit Breaker

1. States: CLOSED → OPEN → HALF_OPEN
2. Prevents cascading failures (stop calling a failing service)
3. Resilience4j: `@CircuitBreaker`, failure rate threshold, wait duration, permitted calls in half-open
4. Fallback methods (return cached data, default response, degraded experience)

### Retry

1. Exponential backoff with jitter (don't thundering-herd a recovering service)
2. Max retries, retry on specific exceptions only
3. Idempotency required for safe retries

### Bulkhead

1. Isolate resources per dependency (separate thread pools / semaphores)
2. One slow service doesn't exhaust all threads
3. Thread pool bulkhead vs semaphore bulkhead

### Rate Limiter

1. Protect services from being overwhelmed
2. Token bucket, sliding window algorithms
3. Per-client, per-API, global limits

### Timeout

1. Every external call needs a timeout (HTTP, DB, cache, message broker)
2. No timeout = thread held forever = thread pool exhaustion = cascading failure
3. Connection timeout vs read timeout (configure both)

### Fallback

1. Graceful degradation (show cached data, partial response, friendly error)
2. Circuit breaker + fallback = resilient system

---

## Data Management

### Database per Service

1. Each service owns its data (no shared database — this is the rule)
2. Other services access data only through APIs/events
3. Polyglot persistence (service A uses PostgreSQL, service B uses MongoDB, service C uses Redis)

### Distributed Transactions

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

### Eventual Consistency

1. Accept that data across services won't be immediately consistent
2. Design for it: idempotent consumers, reconciliation jobs, conflict resolution
3. CQRS (Command Query Responsibility Segregation): separate write model from read model, sync via events

### Configuration Management

1. Externalized config (12-factor app principle)
2. Spring Cloud Config Server (Git-backed, centralized)
3. Kubernetes ConfigMaps and Secrets
4. Environment-specific config (dev, staging, prod)
5. Feature flags (LaunchDarkly, Unleash, or simple DB-backed flags)
6. Secret management (Vault, AWS Secrets Manager — never in code/config files)

---

## Observability (The Three Pillars)

### Logging

1. Structured logging (JSON format — machine-parseable)
2. Correlation ID / Request ID (propagate across services via headers)
3. MDC (Mapped Diagnostic Context) in Java for per-request context
4. Centralized logging: ELK stack (Elasticsearch + Logstash + Kibana) or Loki + Grafana
5. Log levels: ERROR (action needed), WARN (potential issue), INFO (business events), DEBUG (development)

### Metrics

1. RED method: Rate, Errors, Duration (for services)
2. USE method: Utilization, Saturation, Errors (for resources)
3. Micrometer + Prometheus + Grafana (standard Java/Spring stack)
4. Key metrics: request rate, error rate, p50/p95/p99 latency, thread pool usage, connection pool usage, JVM heap, GC pauses
5. Custom business metrics (orders per minute, payment success rate)
6. Alerting on SLOs (Service Level Objectives)

### Distributed Tracing

1. Trace = end-to-end request across services. Span = one operation within a trace.
2. Micrometer Tracing (Spring Boot 3) / OpenTelemetry
3. Zipkin, Jaeger, or cloud-native (AWS X-Ray, Google Cloud Trace)
4. Trace context propagation (W3C Trace Context header)
5. Identify bottlenecks: which service is slow in a 10-service call chain?

---

## Deployment & Infrastructure

### Containerization

1. Docker: multi-stage builds, layered JARs, minimal base images (eclipse-temurin, distroless)
2. Image size optimization (don't ship the entire JDK + build tools)
3. Docker Compose for local development (all services + infra)

### Kubernetes (must-know at SDE-3)

1. Pods, Deployments, Services, Ingress
2. ConfigMaps, Secrets
3. Readiness probe vs Liveness probe vs Startup probe
4. Horizontal Pod Autoscaler (HPA)
5. Resource requests and limits (CPU, memory)
6. Rolling updates, blue-green, canary deployments
7. Service mesh (Istio/Linkerd — know the concept: sidecar proxy, mTLS, traffic management, observability)

### CI/CD

1. Pipeline: build → test → static analysis → Docker build → push → deploy
2. GitOps (ArgoCD — desired state in Git, auto-sync to cluster)
3. Trunk-based development vs feature branches
4. Canary releases (route 5% traffic to new version, monitor, gradually increase)

---

## Security

1. Authentication: OAuth 2.0 + OpenID Connect (Keycloak, Auth0, Okta)
2. Token propagation (JWT passed between services, validated at gateway or each service)
3. Service-to-service auth: mTLS (mutual TLS), service accounts, API keys
4. Zero trust: every service authenticates and authorizes, even internal calls
5. Secrets management (Vault, K8s Secrets, AWS Secrets Manager)
6. HTTPS everywhere (TLS termination at gateway or service mesh)
7. Input validation at every service boundary (don't trust upstream services)

---

## Testing Microservices

1. Unit tests: business logic, no Spring context
2. Integration tests: one service + real DB (TestContainers)
3. Contract tests: Pact or Spring Cloud Contract (verify API contracts between producer and consumer without running both)
4. Component tests: one service in isolation, mock external dependencies
5. End-to-end tests: full system (expensive, flaky — keep minimal)
6. Chaos testing: kill services, inject latency, simulate network partitions (Chaos Monkey, Litmus)
7. Test pyramid: many unit → some integration → few contract → minimal E2E

---

## Design Patterns Summary

| Pattern | Problem It Solves |
|---------|-------------------|
| API Gateway | Single entry point, cross-cutting concerns |
| Circuit Breaker | Cascading failures |
| Saga | Distributed transactions |
| Outbox | Reliable event publishing |
| CQRS | Separate read/write optimization |
| Event Sourcing | Audit trail, temporal queries |
| Strangler Fig | Incremental monolith migration |
| Sidecar | Cross-cutting concerns without code changes (service mesh) |
| BFF | Client-specific API aggregation |
| Bulkhead | Resource isolation between dependencies |
| Retry + Backoff | Transient failure recovery |
| Idempotent Consumer | Safe message reprocessing |
| CDC | Database-to-event stream bridge |

---

## Common Interview Deep-Dives for SDE-3

1. How would you decompose [monolith X] into microservices?
2. Service A calls B calls C — C is slow. What happens? How do you prevent cascading failure?
3. How do you handle a distributed transaction across order, payment, and inventory services?
4. How do you ensure exactly-once processing with Kafka?
5. Your service is getting 10x traffic spike. Walk through what happens and how your system handles it.
6. How do you debug a request that's failing intermittently across 5 services?
7. How do you handle schema evolution (API versioning, event versioning)?
8. Monolith vs microservices for a startup with 5 engineers — what do you recommend and why?

---

## Microservices Patterns

- The API Gateway Pattern for managing requests.
- The Saga Pattern for handling distributed transactions.
- The Service Registry Pattern for service discovery.
- The CQRS Pattern for separating reads and writes.
- The Database Per Microservice Pattern for independent persistence.
- The Event Sourcing Pattern for reliable state management.
- And the Strangler Fig Pattern for migrating legacy systems step by step.

---

## Chapters

- Chapter-7: Logging
- Chapter-8: Metrics
- Chapter-9: Distributed Tracing
- Chapter-10: Containerization
- Chapter-11: Kubernetes
- Chapter-12: CI/CD
- Chapter-13: Security
- Chapter-14: Testing Microservices
- Chapter-15: Microservices Patterns
- Chapter-16: Design Patterns Summary
