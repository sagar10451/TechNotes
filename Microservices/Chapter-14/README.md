# Testing Microservices

Testing Microservices — more services = more integration points = more ways things can break. Test at multiple levels.

---

## 1. Unit Tests

Test business logic in isolation. No Spring context, no DB, no network. Fast.

```java
// Pure logic — no @SpringBootTest, no dependencies
@Test
void shouldCalculateOrderTotal() {
    Order order = new Order(List.of(
        new Item("Widget", 10.0, 2),
        new Item("Gadget", 25.0, 1)
    ));
    assertEquals(45.0, order.calculateTotal());
}
```

- Mock external dependencies (Mockito)
- Runs in milliseconds
- Covers: validation logic, calculations, domain rules, mappers
- No Spring, no DB, no HTTP — just Java

---

## 2. Integration Tests (Testcontainers)

Test one service with real infrastructure (DB, Kafka, Redis) running in Docker containers.

```java
@SpringBootTest
@Testcontainers
class OrderRepositoryTest {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16");

    @Test
    void shouldSaveAndFindOrder() {
        Order saved = orderRepository.save(new Order("Widget", 10.0));
        Order found = orderRepository.findById(saved.getId()).orElseThrow();
        assertEquals("Widget", found.getProduct());
    }
}
```

- Real DB, not H2 — catches SQL dialect issues, constraint violations
- Testcontainers spins up Docker containers, tears them down after tests
- Covers: repository queries, DB migrations, Kafka producer/consumer, cache behavior

---

## 3. Contract Tests

Verify the API contract between producer (provider) and consumer without running both services together.

Problem: Order Service changes its API → Payment Service breaks. No one knew until production.

```
Consumer (Payment Service):
  "I expect GET /orders/123 to return { orderId, total, status }"
  → writes a contract (Pact file or Spring Cloud Contract)

Producer (Order Service):
  → runs the contract against its actual API
  → if it returns { orderId, total, status } → contract passes ✓
  → if it removes 'status' field → contract fails ✗ (caught before deploy)
```

| Tool | How |
|------|-----|
| Pact | Consumer writes contract, producer verifies. Language-agnostic. |
| Spring Cloud Contract | Producer writes contract, auto-generates consumer stubs. Spring-native. |

- Consumer and producer test independently — no need to run both
- Catches breaking API changes before deployment
- Runs in CI pipeline of both services

---

## 4. Component Tests

Test one service end-to-end in isolation. Real service running, but external dependencies are mocked/stubbed.

```
Order Service (real, running)
  → DB: real (Testcontainers)
  → Payment Service: mocked (WireMock)
  → Kafka: real (Testcontainers) or mocked
```

Test: POST /orders → Order Service creates order → calls mocked Payment → returns response

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class OrderServiceComponentTest {

    @Autowired TestRestTemplate restTemplate;

    // WireMock stubs Payment Service
    @Test
    void shouldCreateOrderAndCallPayment() {
        stubFor(post("/payments").willReturn(ok("{\"status\":\"PAID\"}")));

        ResponseEntity<Order> response = restTemplate.postForEntity(
            "/orders", new OrderRequest("Widget", 10.0), Order.class);

        assertEquals(HttpStatus.CREATED, response.getStatusCode());
    }
}
```

- Tests the full service (controllers → service → repository → mocked externals)
- Catches wiring issues, Spring config problems, serialization bugs

---

## 5. End-to-End Tests

Full system running — all services, all infrastructure. Test real user flows.

```
Test: "User places order"
  → API Gateway → Order Service → Payment Service → Inventory Service → Notification Service
  → verify: order created, payment charged, stock reserved, email sent
```

- Expensive to set up and maintain
- Slow (minutes, not seconds)
- Flaky (network issues, timing, data dependencies)
- Keep minimal — only critical business flows (happy path + key failure scenarios)

Rule: if you can test it at a lower level, don't test it E2E.

---

## 6. Chaos Testing

Intentionally break things in production (or staging) to verify resilience.

| Experiment | What it tests |
|------------|--------------|
| Kill a service instance | Does traffic reroute? Does circuit breaker work? |
| Inject 5s latency | Do timeouts trigger? Does the system degrade gracefully? |
| Network partition | Do services handle unreachable dependencies? |
| Fill disk / exhaust memory | Do alerts fire? Does auto-scaling kick in? |

| Tool | What |
|------|------|
| Chaos Monkey (Netflix) | Randomly kills instances in production |
| Litmus (CNCF) | K8s-native chaos experiments |
| Gremlin | SaaS chaos engineering platform |
| Toxiproxy | Simulate network conditions (latency, disconnect) |

"Everything fails all the time" — test for it before it happens in production.

---

## 7. Test Pyramid

```
      /  E2E  \          ← few (slow, expensive, flaky)
     / Contract \        ← some (verify service boundaries)
    / Integration \      ← some (real DB, Kafka via Testcontainers)
   /    Component   \    ← some (full service, mocked externals)
  /     Unit Tests    \  ← many (fast, cheap, isolated)
```

| Level | Count | Speed | Confidence |
|-------|-------|-------|------------|
| Unit | Hundreds | Milliseconds | Logic correctness |
| Integration | Dozens | Seconds | DB/infra works |
| Component | Dozens | Seconds | Service works end-to-end |
| Contract | Per API | Seconds | Services agree on API shape |
| E2E | Few (<10) | Minutes | Full system works |
| Chaos | Periodic | Varies | System survives failures |

> More tests at the bottom (fast, cheap). Fewer at the top (slow, expensive). Contract tests are the secret weapon — they catch integration bugs without the cost of E2E.
