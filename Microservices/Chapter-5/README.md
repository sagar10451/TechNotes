# Chapter-5: Resilience Patterns

## Resilience Patterns (Critical for SDE-3)

1. Circuit Breaker
2. Retry
3. Bulkhead
4. Rate Limiter
5. Timeout
6. Fallback

---

## 1. Circuit Breaker

1. States: CLOSED → OPEN → HALF_OPEN
2. Prevents cascading failures (stop calling a failing service)
3. Resilience4j: `@CircuitBreaker`, failure rate threshold, wait duration, permitted calls in half-open
4. Fallback methods (return cached data, default response, degraded experience)

Circuit Breaker — stop calling a service that's clearly failing. Prevent one broken service from taking down the whole system.

### 1. Three States

```
CLOSED (normal — all calls go through)
  ↓ failure rate exceeds threshold (e.g., 50% of last 10 calls failed)
OPEN (tripped — all calls rejected immediately, no network call)
  ↓ wait duration expires (e.g., 30s)
HALF-OPEN (testing — allow a few calls through to check if service recovered)
  ↓ calls succeed → back to CLOSED ✓
  ↓ calls fail → back to OPEN ✗
```

| State     | What happens                                  | Why                                              |
|-----------|-----------------------------------------------|--------------------------------------------------|
| CLOSED    | All requests pass through normally            | Service is healthy                               |
| OPEN      | All requests fail immediately (no network call)| Service is down, don't waste time/threads        |
| HALF-OPEN | Limited requests allowed through              | Test if service has recovered                    |

### 2. Prevents Cascading Failures

Without circuit breaker:

```
Order Service → Payment Service (down)
  → thread waits... timeout... retry... timeout...
  → threads exhausted → Order Service dies
  → API Gateway → Order Service (dead) → Gateway overwhelmed
  → entire system down 💥
```

With circuit breaker:

```
Order Service → Payment Service (down)
  → 5 failures → circuit OPENS
  → next 1000 requests fail instantly (no network call, no thread blocked)
  → Order Service stays healthy, returns fallback
  → 30s later → HALF-OPEN → tries 3 calls → Payment Service is back → CLOSED ✓
```

### 3. Resilience4j Configuration

```java
@CircuitBreaker(name = "paymentService", fallbackMethod = "paymentFallback")
public PaymentResponse processPayment(PaymentRequest request) {
    return paymentClient.pay(request);
}
```

```yaml
resilience4j:
  circuitbreaker:
    instances:
      paymentService:
        failure-rate-threshold: 50          # open if 50% of calls fail
        sliding-window-size: 10             # evaluate last 10 calls
        sliding-window-type: COUNT_BASED    # or TIME_BASED
        minimum-number-of-calls: 5          # need at least 5 calls before evaluating
        wait-duration-in-open-state: 30s    # stay open for 30s before trying half-open
        permitted-number-of-calls-in-half-open-state: 3  # allow 3 test calls
        automatic-transition-from-open-to-half-open-enabled: true
```

| Config | What it means |
|--------|---------------|
| `failure-rate-threshold: 50` | Open circuit if ≥50% of calls fail |
| `sliding-window-size: 10` | Look at the last 10 calls to calculate failure rate |
| `wait-duration-in-open-state: 30s` | Wait 30s before testing recovery |
| `permitted-number-of-calls-in-half-open-state: 3` | Allow 3 test calls in half-open |
| `minimum-number-of-calls: 5` | Don't evaluate until at least 5 calls recorded |

Sliding window types:

- `COUNT_BASED` — last N calls
- `TIME_BASED` — calls in last N seconds

### 4. Fallback Methods

When circuit is open (or call fails), return something useful instead of an error.

```java
public PaymentResponse paymentFallback(PaymentRequest request, Throwable t) {
    // Option 1: cached data
    return cache.getLastKnownPaymentStatus(request.getOrderId());

    // Option 2: default response
    return new PaymentResponse("PENDING", "Payment service temporarily unavailable");

    // Option 3: degraded experience
    return new PaymentResponse("QUEUED", "Payment will be processed shortly");
}
```

| Fallback strategy | When to use |
|-------------------|-------------|
| Cached data | Read operations — return last known good data |
| Default response | When a safe default exists (empty list, "unavailable") |
| Degraded experience | Queue the work for later, show partial data |
| Throw friendly error | When no fallback makes sense — at least fail gracefully |

The fallback method must have the same return type + a Throwable parameter.

> Key point: circuit breaker isn't about fixing the failing service — it's about protecting everything else from it. Fail fast, return a fallback, let the broken service recover on its own.

---

## 2. Retry

1. Exponential backoff with jitter (don't thundering-herd a recovering service)
2. Max retries, retry on specific exceptions only
3. Idempotency required for safe retries

Retry — if a call fails, try again. Many failures are transient (network blip, momentary overload) and succeed on the next attempt.

### 1. Exponential Backoff with Jitter

Don't retry immediately — you'll hammer a struggling service and make it worse.

Exponential backoff: wait longer between each retry.

```
Attempt 1: fail → wait 1s
Attempt 2: fail → wait 2s
Attempt 3: fail → wait 4s
Attempt 4: success ✓
```

Problem: if 500 clients all retry at 1s, 2s, 4s — they all hit the service at the exact same time (thundering herd).

Jitter: add randomness to the wait time so retries spread out.

```
Client A: fail → wait 1.2s → fail → wait 2.7s → success
Client B: fail → wait 0.8s → fail → wait 1.9s → success
Client C: fail → wait 1.5s → fail → wait 3.1s → success
```

No spike. Retries are staggered. Service has breathing room to recover.

Resilience4j config:

```yaml
resilience4j:
  retry:
    instances:
      paymentService:
        max-attempts: 3
        wait-duration: 1s
        enable-exponential-backoff: true
        exponential-backoff-multiplier: 2    # 1s, 2s, 4s
        enable-randomized-wait: true         # adds jitter
```

### 2. Max Retries + Retry on Specific Exceptions Only

Don't retry forever — set a cap (e.g., 3 attempts)

Only retry on transient errors — retrying a permanent error is pointless

| Retry ✓ (transient) | Don't retry ✗ (permanent) |
|----------------------|---------------------------|
| 503 Service Unavailable | 400 Bad Request |
| 504 Gateway Timeout | 401 Unauthorized |
| Connection timeout | 404 Not Found |
| Network error | 422 Validation Error |

```java
@Retry(name = "paymentService", fallbackMethod = "paymentFallback")
public PaymentResponse processPayment(PaymentRequest req) {
    return paymentClient.pay(req);
}
```

```yaml
resilience4j:
  retry:
    instances:
      paymentService:
        max-attempts: 3
        retry-exceptions:
          - java.io.IOException
          - java.util.concurrent.TimeoutException
          - org.springframework.web.client.HttpServerErrorException
        ignore-exceptions:
          - org.springframework.web.client.HttpClientErrorException  # 4xx
```

Retrying a 400 three times = getting the same 400 three times. Waste.

### 3. Idempotency Required for Safe Retries

Retry means the same request might be sent 2-3 times. If the operation isn't idempotent, you get duplicates.

Idempotent = doing it multiple times has the same effect as doing it once.

| Operation | Idempotent? | Safe to retry? |
|-----------|-------------|----------------|
| GET /orders/123 | Yes (read-only) | Yes ✓ |
| PUT /orders/123 (full replace) | Yes (same result) | Yes ✓ |
| DELETE /orders/123 | Yes (already gone) | Yes ✓ |
| POST /payments (create) | No (creates duplicate) | Dangerous ✗ |

The danger:

```
Client → POST /payments (charge $50) → timeout (but server processed it)
Client → retry POST /payments (charge $50) → server processes again
Customer charged $100 💥
```

Fix: use an idempotency key.

```
Client → POST /payments
  Header: Idempotency-Key: abc-123
  Body: { amount: 50 }

Server: "abc-123 already processed → return cached response, don't charge again"
```

Client generates a unique key per operation (UUID)

Server stores the key + result

On retry with same key → return stored result, skip processing

```
Attempt 1: Idempotency-Key: abc-123 → process → store result → return 200
Attempt 2: Idempotency-Key: abc-123 → already exists → return stored 200 (no reprocessing)
```

> Bottom line: retry without idempotency = data corruption waiting to happen. Always pair retries with idempotent operations or idempotency keys.

---

## 3. Bulkhead

1. Isolate resources per dependency (separate thread pools / semaphores)
2. One slow service doesn't exhaust all threads
3. Thread pool bulkhead vs semaphore bulkhead

Bulkhead — isolate resources per dependency so one slow/failing service can't consume all your resources and take down everything else. Named after ship bulkheads that contain flooding to one compartment.

### 1. Isolate Resources Per Dependency

Without bulkhead — shared thread pool:

```
Order Service (100 threads shared for everything)
  → Payment Service (slow, 90 threads stuck waiting)
  → Inventory Service (healthy, but only 10 threads left)
  → User Service (healthy, but no threads available)
  → entire Order Service is effectively down 💥
```

With bulkhead — isolated pools:

```
Order Service
  → Payment Service  [30 threads] → slow, 30 threads stuck, but contained
  → Inventory Service [30 threads] → healthy, full 30 threads available ✓
  → User Service     [30 threads] → healthy, full 30 threads available ✓
  → 10 threads reserved for other work
```

Payment being slow only affects its own pool. Everything else keeps working.

### 2. One Slow Service Doesn't Exhaust All Threads

The core idea: limit the concurrent calls to each dependency. If the limit is hit, reject immediately instead of queuing and waiting.

```
Bulkhead for Payment Service: max 30 concurrent calls
  Call 1-30: allowed through ✓
  Call 31: rejected immediately (BulkheadFullException) → fallback
```

Payment Service being slow only blocks 30 threads, not 100

### 3. Thread Pool Bulkhead vs Semaphore Bulkhead

| Aspect | Thread Pool Bulkhead | Semaphore Bulkhead |
|--------|---------------------|--------------------|
| How it works | Separate thread pool per dependency | Counter limiting concurrent calls on the caller's thread |
| Isolation | Full — runs on different threads | Partial — shares caller's thread pool |
| Timeout support | Yes (task can be timed out on the pool) | No (relies on HTTP client timeout) |
| Overhead | Higher (thread pool management, context switching) | Lower (just a counter) |
| Queue | Has a waiting queue (configurable) | No queue — reject immediately when full |
| Best for | Blocking/synchronous calls | Reactive/non-blocking or lightweight calls |

Thread Pool Bulkhead:

```java
@Bulkhead(name = "paymentService", type = Bulkhead.Type.THREADPOOL,
           fallbackMethod = "paymentFallback")
public CompletableFuture<PaymentResponse> pay(PaymentRequest req) {
    return CompletableFuture.supplyAsync(() -> paymentClient.pay(req));
}
```

```yaml
resilience4j:
  thread-pool-bulkhead:
    instances:
      paymentService:
        max-thread-pool-size: 30
        core-thread-pool-size: 10
        queue-capacity: 10          # 10 can wait in queue after 30 threads full
```

30 threads busy + 10 in queue = 40 max

Call 41 → rejected → fallback

Semaphore Bulkhead:

```java
@Bulkhead(name = "paymentService", fallbackMethod = "paymentFallback")
public PaymentResponse pay(PaymentRequest req) {
    return paymentClient.pay(req);
}
```

```yaml
resilience4j:
  bulkhead:
    instances:
      paymentService:
        max-concurrent-calls: 30
        max-wait-duration: 0        # 0 = reject immediately when full
```

30 concurrent calls allowed

Call 31 → rejected immediately → fallback

Quick pick:

- Synchronous/blocking calls (RestClient, Feign) → Thread Pool Bulkhead (true isolation)
- Reactive/non-blocking (WebClient) → Semaphore Bulkhead (no need for extra threads)
- Not sure → Semaphore is simpler and lighter, start there

Bulkhead + Circuit Breaker together:

```
Request → Bulkhead (limit concurrency — don't overwhelm)
        → Circuit Breaker (stop calling if it's dead)
        → Timeout (don't wait forever)
        → Retry (try again if transient)
        → Fallback (return something useful)
```

> Bulkhead limits the blast radius. Circuit breaker stops the bleeding. Together they keep one bad dependency from sinking the ship.

---

## 4. Rate Limiter

1. Protect services from being overwhelmed
2. Token bucket, sliding window algorithms
3. Per-client, per-API, global limits

Rate Limiter — control how many requests a service accepts in a time period. Reject excess traffic before it causes damage.

### 1. Protect Services from Being Overwhelmed

Without rate limiting:

```
Misbehaving client → 10,000 requests/sec → service overloaded → crashes → affects all users 💥
```

With rate limiting:

```
Misbehaving client → 10,000 requests/sec → rate limiter allows 100/sec → rejects rest with 429
Other clients → normal traffic → unaffected ✓
```

Protects against: DDoS, buggy clients in retry loops, scraping, uneven traffic spikes.

### 2. Token Bucket vs Sliding Window

Already covered in gateway rate limiting — same algorithms, now applied at the service level via Resilience4j.

Resilience4j uses sliding window:

```yaml
resilience4j:
  ratelimiter:
    instances:
      paymentService:
        limit-for-period: 50              # 50 calls allowed
        limit-refresh-period: 1s          # per 1 second
        timeout-duration: 0s              # 0 = reject immediately when limit hit
```

```java
@RateLimiter(name = "paymentService", fallbackMethod = "rateLimitFallback")
public PaymentResponse pay(PaymentRequest req) {
    return paymentClient.pay(req);
}

public PaymentResponse rateLimitFallback(PaymentRequest req, Throwable t) {
    return new PaymentResponse("THROTTLED", "Too many requests, try again shortly");
}
```

```
Requests 1-50 in 1s window → allowed ✓
Request 51 → rejected (429 / fallback) ✗
Next second → counter resets → 50 more allowed
```

### 3. Per-Client, Per-API, Global Limits

| Scope | What it limits | Example |
|-------|---------------|---------|
| Per-client | Each client gets its own limit | Client A: 100/min, Client B: 100/min (independent) |
| Per-API | Limit on a specific endpoint | POST /payments: 50/sec, GET /products: 500/sec |
| Global | Total limit across all clients | Service handles max 1000 req/sec total |

Per-client (most common at gateway):

```
API Key: client-A → 100 requests/min → exceeded → 429
API Key: client-B → 100 requests/min → still has quota → allowed ✓
```

Per-API (different endpoints have different costs):

```
GET  /products  → 500/sec (cheap read)
POST /payments  → 50/sec  (expensive write)
POST /reports   → 5/min   (heavy computation)
```

Global (protect the service itself):

```
Service can handle 1000 req/sec max
All clients combined exceed 1000 → reject overflow
Doesn't matter who's calling — total capacity is the limit
```

Where to apply:

| Layer | Tool | Scope |
|-------|------|-------|
| API Gateway | Kong, AWS API Gateway, Spring Cloud Gateway | Per-client, per-API (edge) |
| Service level | Resilience4j | Per-dependency, global (internal) |
| Infrastructure | NGINX, Envoy | Global, per-IP |

Gateway rate limiting protects your system from external abuse. Service-level rate limiting protects individual services from internal overload (e.g., one service hammering another).

### Rate Limiter vs Circuit Breaker vs Bulkhead

| Pattern | Protects against | How |
|---------|-----------------|-----|
| Rate Limiter | Too many requests coming in | Caps request rate |
| Circuit Breaker | Calling a failing dependency | Stops outgoing calls |
| Bulkhead | One dependency consuming all resources | Isolates resource pools |

> Rate limiter = incoming traffic control. Circuit breaker = outgoing call protection. Different directions, complementary.

---

## 5. Timeout

1. Every external call needs a timeout (HTTP, DB, cache, message broker)
2. No timeout = thread held forever = thread pool exhaustion = cascading failure
3. Connection timeout vs read timeout (configure both)

Timeout — set a max wait time on every external call. If it doesn't respond in time, fail fast and move on.

### 1. Every External Call Needs a Timeout

Not just HTTP calls — everything that goes over a network or touches an external resource:

| Call type | Needs timeout |
|-----------|---------------|
| REST / gRPC calls | Yes |
| Database queries | Yes |
| Redis / cache lookups | Yes |
| Kafka produce/consume | Yes |
| S3 / external storage | Yes |
| SMTP / email sending | Yes |

If it's not in your process memory, it can hang. If it can hang, it needs a timeout.

### 2. No Timeout = Cascading Failure

No timeout:

```
Order Service → Payment Service (frozen)
  → thread waits... 30s... 60s... 120s...
  → 200 requests pile up → 200 threads stuck
  → thread pool exhausted → Order Service can't handle ANY request
  → API Gateway → Order Service (unresponsive) → Gateway threads stuck
  → entire system down 💥
```

With timeout (3s):

```
Order Service → Payment Service (frozen)
  → 3s → timeout → thread freed → return fallback
  → next request gets a thread immediately ✓
  → Order Service stays healthy
```

One missing timeout can take down your entire microservice chain. It's the #1 cause of cascading failures.

### 3. Connection Timeout vs Read Timeout

| Type | What it measures | Typical value |
|------|-----------------|---------------|
| Connection timeout | Time to establish TCP connection | 1-3s |
| Read/response timeout | Time to receive data after connected | 3-10s |

```
Client ──[connection timeout]──→ TCP handshake ──[read timeout]──→ waiting for response
         "can I reach you?"                      "where's my data?"
```

Both can fail independently:

- Connection timeout hit → service is unreachable (down, wrong host, firewall)
- Read timeout hit → service is reachable but slow (overloaded, long query, deadlock)

Spring RestClient / RestTemplate:

```java
RestClient restClient = RestClient.builder()
    .requestFactory(new HttpComponentsClientHttpRequestFactory() {{
        setConnectTimeout(Duration.ofSeconds(2));    // 2s to connect
        setReadTimeout(Duration.ofSeconds(5));       // 5s to read
    }})
    .build();
```

OpenFeign:

```yaml
spring:
  cloud:
    openfeign:
      client:
        config:
          default:
            connect-timeout: 2000    # 2s
            read-timeout: 5000       # 5s
```

Resilience4j TimeLimiter (wraps any call):

```yaml
resilience4j:
  timelimiter:
    instances:
      paymentService:
        timeout-duration: 3s         # total time limit for the call
        cancel-running-future: true  # cancel the thread if timeout hit
```

```java
@TimeLimiter(name = "paymentService", fallbackMethod = "timeoutFallback")
@CircuitBreaker(name = "paymentService", fallbackMethod = "timeoutFallback")
public CompletableFuture<PaymentResponse> pay(PaymentRequest req) {
    return CompletableFuture.supplyAsync(() -> paymentClient.pay(req));
}
```

Database timeout:

```yaml
spring:
  datasource:
    hikari:
      connection-timeout: 2000       # 2s to get connection from pool
      max-lifetime: 30000            # 30s max connection lifetime
  jpa:
    properties:
      jakarta.persistence.query.timeout: 5000  # 5s per query
```

How timeout fits with other patterns:

```
Request → Timeout (don't wait forever)
        → Retry (try again if timed out — with backoff)
        → Circuit Breaker (stop trying if timeouts keep happening)
        → Bulkhead (limit how many threads can be stuck waiting)
        → Fallback (return something useful)
```

Rule of thumb for values:

- Connection timeout: 1-3s (if you can't connect in 3s, it's probably down)
- Read timeout: 3-10s (depends on expected response time)
- Total timeout < caller's timeout (if gateway times out at 30s, service calls should be well under that)

> No timeout = infinite wait = guaranteed cascading failure. Configure both connection and read timeouts on every single external call.

---

## 6. Fallback

1. Graceful degradation (show cached data, partial response, friendly error)
2. Circuit breaker + fallback = resilient system

Fallback — when a call fails, return something useful instead of an error. The system degrades gracefully instead of breaking completely.

### 1. Graceful Degradation

Instead of showing users a 500 error, give them the best you can:

| Strategy | When to use | Example |
|----------|-------------|---------|
| Cached data | Read operations, data doesn't change often | Show last known product price from cache |
| Partial response | Some services responded, some didn't | Show order details but "customer info unavailable" |
| Default value | Safe default exists | Empty recommendations list instead of personalized ones |
| Queued for later | Write operations | "Payment queued, we'll confirm shortly" |
| Friendly error | No fallback makes sense | "This feature is temporarily unavailable" instead of stack trace |

Examples:

- Netflix: recommendation service down → show trending/popular (cached) instead of personalized
- Amazon: review service down → show product page without reviews (partial)
- Uber: surge pricing service down → use default pricing (default value)
- E-commerce: payment service down → "order saved, payment will be retried" (queued)

### 2. Circuit Breaker + Fallback = Resilient System

Circuit breaker decides WHEN to stop calling. Fallback decides WHAT to return instead.

Normal flow:

```
Request → Payment Service → 200 OK → return response ✓
```

Service failing:

```
Request → Payment Service → timeout/500
  → retry → timeout/500
  → circuit breaker OPENS
  → all subsequent calls → skip network call → fallback immediately
  → return cached/default/queued response ✓
```

Service recovers:

```
Circuit → HALF-OPEN → test calls succeed → CLOSED → normal flow resumes
```

Without fallback: circuit opens → user gets error

With fallback: circuit opens → user gets degraded but functional experience

```java
@CircuitBreaker(name = "paymentService", fallbackMethod = "paymentFallback")
@Retry(name = "paymentService", fallbackMethod = "paymentFallback")
@TimeLimiter(name = "paymentService", fallbackMethod = "paymentFallback")
public CompletableFuture<PaymentResponse> pay(PaymentRequest req) {
    return CompletableFuture.supplyAsync(() -> paymentClient.pay(req));
}

// Fallback — same return type + Throwable
public CompletableFuture<PaymentResponse> paymentFallback(PaymentRequest req, Throwable t) {
    if (t instanceof CallNotPermittedException) {
        // Circuit is OPEN — service is known to be down
        return CompletableFuture.completedFuture(
            new PaymentResponse("QUEUED", "Payment will be processed shortly"));
    }
    if (t instanceof TimeoutException) {
        // Timed out — maybe return cached status
        return CompletableFuture.completedFuture(
            cache.getLastPaymentStatus(req.getOrderId()));
    }
    // Generic fallback
    return CompletableFuture.completedFuture(
        new PaymentResponse("UNAVAILABLE", "Please try again later"));
}
```

You can tailor the fallback based on the exception type — different failure, different response.

Fallback chain (cascading fallbacks):

```
Primary: call Payment Service (live)
  ↓ fails
Fallback 1: check local cache
  ↓ cache miss
Fallback 2: return default response
  ↓ not applicable
Fallback 3: friendly error message
```

The full resilience stack together:

```
Request
  → Rate Limiter (don't accept too many)
  → Bulkhead (isolate resources)
  → Circuit Breaker (stop if service is dead)
  → Timeout (don't wait forever)
  → Retry (try again on transient failure)
  → Fallback (return something useful on any failure)
```

Each layer catches what the previous one missed. Fallback is the last safety net — it ensures the user always gets a response, never a blank screen or cryptic error.

> Bottom line: a system without fallbacks is brittle. A system with fallbacks degrades gracefully — some features may be limited, but the app stays usable.
