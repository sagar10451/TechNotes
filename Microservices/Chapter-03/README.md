# Chapter-3: API Gateway

## Contents

1. What it does: routing, authentication, rate limiting, load balancing, request transformation, SSL termination
2. Spring Cloud Gateway (routes, predicates, filters)
3. Kong, NGINX, AWS API Gateway, Envoy (know at least one)
4. BFF pattern (Backend for Frontend — separate gateway per client type: web, mobile, internal)
5. API composition (gateway aggregates responses from multiple services)
6. Rate limiting at gateway level (token bucket, sliding window)

---

## 1. What it does: routing, authentication, rate limiting, load balancing, request transformation, SSL termination

### API Gateway

An API Gateway is a single entry point that sits between clients and your backend services. Instead of clients calling each microservice directly, everything goes through the gateway.

### What it does

- Routing — receives a request like `GET /orders/123` and forwards it to the correct internal service (Order Service). Clients don't need to know where services live or how many there are.
- Authentication — verifies identity (JWT, OAuth token, API key) at the gateway level so individual services don't each have to implement auth. Request gets rejected before it even reaches your services if the token is invalid.
- Rate Limiting — controls how many requests a client can make in a time window (e.g., 100 requests/minute). Protects backend services from being overwhelmed by a single client or a DDoS attack.
- Load Balancing — distributes incoming requests across multiple instances of a service. If Order Service has 3 replicas, the gateway spreads traffic evenly (round-robin, least connections, etc.).
- Request Transformation — modifies requests/responses on the fly. Can add/remove headers, change payload format, merge responses from multiple services into one, or translate between protocols (e.g., REST externally → gRPC internally).
- SSL Termination — handles HTTPS (TLS encryption/decryption) at the gateway. Internal service-to-service communication can then use plain HTTP, reducing the CPU overhead of encryption on every service. The "secure boundary" is at the edge.

### How it fits in

```
Client (browser/mobile)
  → HTTPS → API Gateway
    ├── auth check (reject if invalid)
    ├── rate limit check (reject if exceeded)
    ├── route to correct service
    ├── load balance across instances
    └── transform request if needed
  → HTTP → Order Service (instance 1, 2, or 3)
  → HTTP → User Service
  → gRPC → Inventory Service
```

Without a gateway, every service would need to handle auth, rate limiting, SSL, etc. on its own — duplicated logic everywhere. The gateway centralizes these cross-cutting concerns.

### Common implementations

| Tool | Type |
|------|------|
| AWS API Gateway | Fully managed, serverless |
| Kong | Open-source, plugin-based |
| NGINX | Lightweight, high-performance reverse proxy |
| Spring Cloud Gateway | Java/Spring ecosystem, reactive |
| Envoy | Service mesh sidecar, used with Istio |
| Zuul | Netflix's gateway (older, mostly replaced by Spring Cloud Gateway) |

> Think of it as the bouncer, receptionist, and traffic cop for your microservices — all in one.

---

## 2. Spring Cloud Gateway (routes, predicates, filters)

Spring Cloud Gateway — the Spring ecosystem's API gateway. Built on WebFlux (reactive, non-blocking). Replaced Zuul.

### Three core concepts

Routes — map incoming request to a destination service. Each route has an ID, destination URI, predicates, and filters.

```yaml
- id: order-service
  uri: lb://ORDER-SERVICE    # lb:// = load balanced via Eureka
  predicates:
    - Path=/orders/**
  filters:
    - StripPrefix=1
```

Predicates — conditions to match a route. All must be true (AND logic).

| Predicate | Matches |
|-----------|---------|
| `Path=/orders/**` | URL path |
| `Method=GET,POST` | HTTP method |
| `Header=X-Key, \d+` | Header + regex |
| `Query=category, electronics` | Query param |
| `Host=**.example.com` | Hostname |
| `After/Before/Between=...` | Time-based |
| `Cookie=session, abc.*` | Cookie + regex |
| `RemoteAddr=192.168.1.0/24` | Client IP |

Filters — modify request/response before or after forwarding.

Pre-filters (before service):

- `AddRequestHeader`, `AddRequestParameter`
- `StripPrefix=1` — removes path prefix (`/api/orders` → `/orders`)
- `RewritePath` — regex path rewrite
- `RequestRateLimiter` — rate limiting via Redis

Post-filters (after service):

- `AddResponseHeader`, `RemoveResponseHeader`, `SetStatus`

Special:

- `CircuitBreaker=myBreaker` — Resilience4j integration
- `Retry` — retry failed requests

Global Filters — apply to all routes. Implement `GlobalFilter` in Java. Used for logging, auth, correlation IDs.

```
Client → Predicate matches route → Pre-filters run → Load balance to service → Post-filters run → Response
```

> Predicates = WHERE it goes. Filters = WHAT happens along the way.

---

## 3. Kong, NGINX, AWS API Gateway, Envoy (know at least one)

Quick overview of all four, then a deeper look at AWS API Gateway (most likely to encounter in production):

| Gateway | Type | Built on | Best for |
|---------|------|----------|----------|
| Kong | Open-source, plugin-based | NGINX + Lua | Self-managed, plugin ecosystem |
| NGINX | Reverse proxy / LB | C | High-performance routing, static content, SSL |
| AWS API Gateway | Fully managed, serverless | AWS infra | AWS-native apps, zero ops |
| Envoy | Service mesh sidecar proxy | C++ | Kubernetes, Istio, service-to-service traffic |

- Kong — wraps NGINX, adds API management (auth, rate limiting, logging) via plugins. You configure routes and plugins via REST API or declarative YAML. Enterprise version adds GUI, RBAC, analytics.
- NGINX — not an API gateway by design, but widely used as one. Handles routing, load balancing, SSL termination, caching. Config is file-based (`nginx.conf`). Lightweight, extremely fast. NGINX Plus (paid) adds service discovery, health checks.
- Envoy — designed for service mesh (sidecar pattern). Runs alongside each service, handles traffic between services. Used by Istio as its data plane. Does load balancing, retries, circuit breaking, observability (distributed tracing). Not typically used as an edge gateway alone.

### AWS API Gateway (deeper)

Three types:

| Type | Protocol | Use case |
|------|----------|----------|
| REST API | REST | Full-featured, caching, WAF, usage plans |
| HTTP API | REST | Simpler, cheaper, faster — good enough for most cases |
| WebSocket API | WebSocket | Real-time (chat, live updates) |

What it handles:

- Routing → Lambda, ECS, EC2, any HTTP endpoint
- Auth → Cognito, Lambda authorizers, IAM
- Rate limiting → usage plans + API keys (throttle per client)
- Request/response transformation → mapping templates (VTL)
- SSL termination → automatic via ACM certificates
- Caching → built-in response cache (REST API type)
- Monitoring → CloudWatch metrics, access logs, X-Ray tracing

```
Client → API Gateway
  ├── /orders → Lambda (serverless)
  ├── /users → ECS Service (containers)
  └── /legacy → EC2 instance (HTTP proxy)
```

Pricing: pay per request + data transfer. No servers to manage.

Quick pick guide:

- On AWS, serverless? → AWS API Gateway
- Self-hosted, need plugins? → Kong
- Just need a fast reverse proxy? → NGINX
- Kubernetes / service mesh? → Envoy

---

## 4. BFF pattern (Backend for Frontend — separate gateway per client type: web, mobile, internal)

BFF (Backend for Frontend) — instead of one generic API gateway for all clients, you build a separate backend/gateway tailored to each client type.

### The problem with one gateway

- Mobile needs minimal data (bandwidth matters), web needs rich data, internal tools need admin-level data
- One API tries to serve all → over-fetching for mobile, under-fetching for web, awkward compromises everywhere
- Different clients evolve at different speeds — mobile release cycles ≠ web deploy cycles

### The solution

```
Mobile App  → Mobile BFF  ──→ Order Service
Web App     → Web BFF     ──→ User Service
Admin Tool  → Admin BFF   ──→ Inventory Service
                           ──→ Analytics Service
```

Each BFF:

- Aggregates data from multiple backend services
- Returns exactly what its client needs (no more, no less)
- Handles client-specific logic (pagination style, auth flow, response format)
- Can evolve independently without breaking other clients

### Example — GET /orders/123

| Client | What it needs | BFF returns |
|--------|--------------|-------------|
| Mobile | Order status + total | `{ status, total }` |
| Web | Full order + items + customer | `{ status, total, items[], customer{} }` |
| Admin | Order + audit log + internal flags | `{ status, total, items[], auditLog[], internalNotes }` |

Same backend services, different BFFs shaping the response.

### Who owns the BFF?

- Ideally the frontend team owns its BFF — they know what data they need
- Mobile team owns Mobile BFF, web team owns Web BFF
- Aligns with Conway's Law — team structure matches architecture

### BFF vs API Gateway

| Aspect | API Gateway | BFF |
|--------|-------------|-----|
| How many | One for all clients | One per client type |
| Logic | Generic routing, auth, rate limiting | Client-specific aggregation, transformation |
| Owned by | Platform/infra team | Frontend/client team |
| Complexity | Centralized | More services to maintain |

When to use:

- Multiple client types with different data needs
- Frontend teams want autonomy over their API shape
- You're doing a lot of response aggregation/transformation per client

When to skip:

- Only one client type (just a web app) → single gateway is fine
- All clients need the same data → BFF adds unnecessary duplication

> BFF is often implemented as a thin service or a GraphQL layer per client type.

---

## 5. API composition (gateway aggregates responses from multiple services)

API Composition — the gateway (or a composer service) calls multiple backend services, combines their responses, and returns one unified response to the client.

### The problem

In microservices, data is split across services. Client needs data from 3 services → without composition, client makes 3 calls:

```
Client → GET /orders/123     → Order Service
Client → GET /customers/456  → Customer Service
Client → GET /products/789   → Product Service
```

3 round trips, client does the merging — slow, wasteful, couples frontend to backend structure.

### With API Composition

```
Client → GET /order-summary/123 → API Gateway / Composer
  ├── Order Service    → { orderId, total, status }
  ├── Customer Service → { name, email }
  └── Product Service  → { items[] }
  ← merges into one response

Client ← { orderId, total, status, customer: { name, email }, items: [...] }
```

One call from client. Gateway handles the fan-out and merge.

### Who does the composing?

| Option | When |
|--------|------|
| API Gateway | Simple aggregation, few services |
| BFF layer | Client-specific composition |
| Dedicated Composer Service | Complex logic, reusable across clients |

### How calls happen internally

Parallel where possible — call Order + Customer + Product simultaneously, merge when all return

Sequential when dependent — get orderId first, then use it to fetch items

```
Parallel:
  Order Service ──┐
  Customer Service ├── wait for all → merge → respond
  Product Service ─┘

Sequential:
  Order Service → get customerId → Customer Service → merge → respond
```

### Trade-offs

| Pro | Con |
|-----|-----|
| Client gets one clean response | Gateway becomes more complex |
| Reduces client round trips | Latency = slowest downstream service |
| Hides internal service structure | Failure in one service can fail the whole response |
| Frontend stays simple | Harder to cache (composite response) |

### Handling partial failures

One service is down — do you fail the whole request or return partial data?

Common approach: return what you can + nulls/defaults for the failed part

Use timeouts + circuit breakers on each internal call

```json
{
  "order": { "id": 123, "total": 50.0 },
  "customer": null,  // Customer Service timed out
  "items": [...]
}
```

### API Composition vs CQRS

- Composition = query-time aggregation (call services on every request)
- CQRS = pre-built read model (data already aggregated in a read DB)
- Composition is simpler to implement, CQRS is faster at query time but more complex to maintain

> Use API Composition for straightforward aggregation. Move to CQRS when composition becomes a performance bottleneck or the query patterns get complex.

---

## 6. Rate limiting at gateway level (token bucket, sliding window)

Rate Limiting — control how many requests a client can make in a time window. Done at the gateway so backend services are protected before traffic even reaches them.

### Why at the gateway

- Single enforcement point — no need to implement in every service
- Stops abuse early (DDoS, misbehaving clients, scraping)
- Protects backend from overload
- Returns `429 Too Many Requests` when limit exceeded

### Two main algorithms

### 1. Token Bucket

- Bucket holds tokens (e.g., max 10 tokens)
- Each request consumes 1 token
- Tokens refill at a fixed rate (e.g., 2 tokens/second)
- If bucket is empty → reject (429)

```
Bucket capacity: 10, Refill rate: 2/sec

Request 1:  10 tokens → take 1 → 9 left ✓
Request 2:  9 tokens  → take 1 → 8 left ✓
...
Request 10: 1 token   → take 1 → 0 left ✓
Request 11: 0 tokens  → rejected 429 ✗
(wait 0.5s, 1 token refilled)
Request 12: 1 token   → take 1 → 0 left ✓
```

- Allows bursts (client can use all 10 tokens at once)
- Smooths out over time (refill rate is the sustained limit)
- Used by: AWS API Gateway, Spring Cloud Gateway (Redis-based)

### 2. Sliding Window

- Tracks requests in a rolling time window (e.g., last 60 seconds)
- If count exceeds limit in that window → reject

Two variants:

Sliding Window Counter:

```
Limit: 100 requests / 60 seconds
Window: [now - 60s ←──────────→ now]
Requests in this window: 98 → allowed ✓
Requests in this window: 100 → next one rejected 429 ✗
```

- Window slides with time — no fixed reset point
- More accurate than fixed windows (no boundary burst problem)

Fixed Window (simpler but flawed):

```
Window: 12:00:00 - 12:01:00 → limit 100
Client sends 99 at 12:00:59, 99 at 12:01:01
→ 198 requests in 2 seconds, both windows say "under limit" 💥
```

Sliding window fixes this by looking at the actual rolling period.

### Comparison

| Aspect | Token Bucket | Sliding Window |
|--------|-------------|----------------|
| Bursts | Allows (up to bucket size) | Strict limit, no bursts |
| Smoothness | Naturally smooths via refill | Hard cap per window |
| Implementation | Counter + timestamp | Sorted set or counters per sub-window |
| Memory | Low (just token count + last refill time) | Higher (track individual requests or sub-windows) |
| Used by | AWS, Spring Cloud Gateway, Stripe | Cloudflare, Redis-based custom solutions |

### Rate limit by what?

- Per API key / client ID (most common)
- Per IP address
- Per user / JWT subject
- Per endpoint (e.g., `/login` gets stricter limits)

### Response when limited

```
HTTP 429 Too Many Requests

Headers:
  X-RateLimit-Limit: 100
  X-RateLimit-Remaining: 0
  X-RateLimit-Reset: 1672531260  (unix timestamp when limit resets)
  Retry-After: 30  (seconds)
```

> Token bucket for most cases — simple, allows healthy bursts. Sliding window when you need strict, even enforcement with no burst tolerance.
