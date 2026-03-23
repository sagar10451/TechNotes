# Logging

Logging — in microservices, logs are scattered across dozens of services. Without structure and centralization, debugging is impossible.

---

## 1. Structured Logging (JSON)

✗ Unstructured:

```
2025-03-20 10:15:32 INFO Order created for user 456, orderId=123
```

✓ Structured (JSON):

```json
{
  "timestamp": "2025-03-20T10:15:32Z",
  "level": "INFO",
  "service": "order-service",
  "message": "Order created",
  "orderId": 123,
  "userId": 456,
  "correlationId": "abc-789",
  "duration": 45
}
```

Why JSON: machines can parse, index, search, filter, and alert on specific fields. Try searching "all errors for orderId=123 in the last hour" with unstructured text — painful. With JSON fields — trivial.

Spring Boot (Logback + JSON encoder):

```xml
<encoder class="net.logstash.logback.encoder.LogstashEncoder"/>
```

---

## 2. Correlation ID / Request ID

One user request hits 5 services. How do you trace it? Pass a unique ID through every service.

```
Client → API Gateway (generates correlationId: abc-789)
  → Order Service (header: X-Correlation-Id: abc-789) → logs with abc-789
  → Payment Service (passes header along) → logs with abc-789
  → Inventory Service (passes header along) → logs with abc-789
```

Search logs for `correlationId=abc-789` → see the entire request flow across all services.

Propagated via HTTP header (`X-Correlation-Id` or `X-Request-Id`). If no ID exists, first service (or gateway) generates one (UUID).

---

## 3. MDC (Mapped Diagnostic Context)

Java/SLF4J feature — attach per-request context to every log line automatically without passing it to every method.

```java
// Filter — runs on every request
public class CorrelationFilter implements Filter {
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) {
        String correlationId = ((HttpServletRequest) req).getHeader("X-Correlation-Id");
        if (correlationId == null) correlationId = UUID.randomUUID().toString();
        MDC.put("correlationId", correlationId);
        try {
            chain.doFilter(req, res);
        } finally {
            MDC.clear();  // clean up after request
        }
    }
}
```

```xml
<!-- logback pattern — %X{correlationId} pulls from MDC -->
<pattern>%d{ISO8601} [%X{correlationId}] %-5level %logger - %msg%n</pattern>
```

Now every `log.info("Order created")` automatically includes the correlationId. No need to pass it through every method call.

Common MDC fields: correlationId, userId, serviceNam, requestPath.

---

## 4. Centralized Logging

All services ship logs to one place. Search, filter, visualize in one dashboard.

### ELK Stack

```
Services → Logstash (collect, parse, transform)
         → Elasticsearch (store, index, search)
         → Kibana (visualize, dashboards, alerts)
```

Alternative: services → Filebeat (lightweight shipper) → Logstash → Elasticsearch → Kibana

### Loki + Grafana (lighter alternative)

```
Services → Promtail (log shipper)
         → Loki (store, index labels only — cheaper than Elasticsearch)
         → Grafana (visualize, query with LogQL)
```

| Stack | Strength | Trade-off |
|-------|----------|-----------|
| ELK | Full-text search, powerful queries | Heavy, expensive (Elasticsearch is resource-hungry) |
| Loki + Grafana | Lightweight, cheap, integrates with Prometheus | Less powerful search (indexes labels, not full text) |
| AWS CloudWatch Logs | Managed, zero ops on AWS | Vendor lock-in, querying is limited |

---

## 5. Log Levels

| Level | When to use | Example |
|-------|-------------|---------|
| ERROR | Something broke, needs immediate attention | DB connection failed, payment processing error |
| WARN | Potential issue, not broken yet | Retry attempt, cache miss, approaching rate limit |
| INFO | Business events, normal flow milestones | Order created, user logged in, payment processed |
| DEBUG | Development details, not for production | Request payload, SQL queries, variable values |
| TRACE | Very fine-grained (rarely used) | Entering/exiting methods, loop iterations |

### Rules

- Production runs at INFO (or WARN for noisy services)
- DEBUG only enabled temporarily for troubleshooting
- ERROR should be actionable — if no one needs to act on it, it's not an ERROR
- Don't log sensitive data (passwords, tokens, PII) at any level

```yaml
# application.yml
logging:
  level:
    root: INFO
    com.myapp.order: DEBUG    # temporarily debug one package
    org.hibernate.SQL: WARN   # quiet down noisy libraries
```

> Bottom line: structured JSON + correlation IDs + MDC + centralized logging = you can trace any request across any number of services in seconds. Without this, debugging microservices is guesswork.
