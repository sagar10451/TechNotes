# Metrics

Metrics — numbers that tell you how your system is behaving right now. Logs tell you what happened, metrics tell you how things are going.

---

## 1. RED Method (for services)

| Metric | What | Why |
|--------|------|-----|
| Rate | Requests per second | Is traffic normal? Spike? Drop? |
| Errors | Failed requests per second (or error %) | Are things breaking? |
| Duration | How long requests take (latency) | Is it slow? |

If you monitor only three things per service, monitor RED. Covers the user-facing health.

---

## 2. USE Method (for resources — CPU, memory, disk, connections)

| Metric | What | Why |
|--------|------|-----|
| Utilization | % of resource being used | CPU at 90%? Memory full? |
| Saturation | Work waiting in queue | Thread pool queue growing? Requests queuing? |
| Errors | Resource errors | Disk I/O errors, connection refused |

RED = how is the service doing for users. USE = how are the underlying resources doing.

---

## 3. Micrometer + Prometheus + Grafana

The standard observability stack for Java/Spring:

```
Spring Boot App
  → Micrometer (metrics library — like SLF4J but for metrics)
  → /actuator/prometheus (exposes metrics in Prometheus format)
  → Prometheus (scrapes, stores time-series data)
  → Grafana (dashboards, graphs, alerts)
```

Spring Boot auto-configures Micrometer. Add one dependency:

```xml
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
```

```yaml
management:
  endpoints:
    web:
      exposure:
        include: prometheus, health
```

Hit `/actuator/prometheus` → get all metrics in Prometheus format. Prometheus scrapes this every 15s.

---

## 4. Key Metrics to Monitor

| Category | Metric | What it tells you |
|----------|--------|-------------------|
| Traffic | Request rate (req/sec) | Load on the service |
| Errors | Error rate (5xx/sec, error %) | Things breaking |
| Latency | p50, p95, p99 response time | User experience |
| Thread pool | Active threads, queue size | Approaching exhaustion? |
| Connection pool | Active/idle/max connections (HikariCP) | DB connection pressure |
| JVM | Heap used/max, GC pause time, GC frequency | Memory pressure, GC storms |

### Percentile latencies — why p99 matters

```
p50 = 50ms   → half of requests are faster than 50ms (median)
p95 = 200ms  → 95% of requests are under 200ms
p99 = 1500ms → 1% of requests take over 1.5s ← this is where problems hide
```

Average hides outliers. p99 shows the worst experience your users actually have.

```
Average: 100ms (looks fine)
p99: 5000ms (1 in 100 users waits 5 seconds — not fine)
```

---

## 5. Custom Business Metrics

Beyond technical metrics — track what matters to the business.

```java
// Counter — things that only go up
Counter orderCounter = meterRegistry.counter("orders.placed", "region", "us-east");
orderCounter.increment();

// Gauge — current value, can go up or down
Gauge.builder("orders.pending", orderQueue, Queue::size)
     .register(meterRegistry);

// Timer — measure duration
Timer.builder("payment.duration")
     .register(meterRegistry)
     .record(() -> paymentClient.charge(request));
```

| Business metric | Type | Why |
|----------------|------|-----|
| Orders per minute | Counter | Revenue indicator |
| Payment success rate | Counter (success/total) | Payment provider health |
| Cart abandonment rate | Counter | UX issue indicator |
| Active users | Gauge | Current load |
| Signup conversion | Counter | Business KPI |

---

## 6. Alerting on SLOs

SLO (Service Level Objective) = target you set for your service.

| Term | What |
|------|------|
| SLI (Indicator) | The actual metric (e.g., p99 latency = 200ms) |
| SLO (Objective) | The target (e.g., p99 latency < 500ms, 99.9% of the time) |
| SLA (Agreement) | Contract with customers (e.g., 99.9% uptime or we pay penalty) |

```
SLI: p99 latency is currently 450ms
SLO: p99 latency must be < 500ms
→ close to breaching — alert ⚠️

SLI: error rate is 2%
SLO: error rate must be < 1%
→ breached — page on-call 🚨
```

Alert on SLO burn rate, not raw thresholds:

- "Error budget is burning 10x faster than normal" → alert
- vs "Error rate > 1% for 5 minutes" → too noisy, too many false alarms

Error budget = allowed failures. SLO of 99.9% uptime = 0.1% error budget = ~43 min/month of downtime allowed. If you burn through it in 2 days, alert.

Grafana alerting or Prometheus Alertmanager handles this — define rules, route alerts to Slack/PagerDuty.
