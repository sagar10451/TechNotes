# Distributed Tracing

Distributed Tracing — follow a single request as it flows through multiple services. See exactly where time is spent and where things break.

---

## 1. Trace and Span

- Trace = the entire journey of one request (unique traceId)
- Span = one step/operation within that journey (unique spanId)

```
Trace: abc-123
├── Span 1: API Gateway (5ms)
├── Span 2: Order Service (45ms)
│   ├── Span 3: DB query (15ms)
│   └── Span 4: Payment Service call (25ms)
│       └── Span 5: Payment DB query (10ms)
└── Span 6: Inventory Service (20ms)

Total trace duration: 95ms
```

Each span records: service name, operation, start time, duration, status (success/error), parent span.

Spans are nested — you see the full call tree and exactly which service/operation took how long.

---

## 2. Micrometer Tracing / OpenTelemetry

### Micrometer Tracing (Spring Boot 3)

- Built into Spring Boot 3 — replaces Spring Cloud Sleuth
- Auto-instruments REST calls, DB queries, Kafka, etc.
- Bridges to any tracing backend (Zipkin, Jaeger, OTLP)

```xml
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-tracing-bridge-otel</artifactId>  <!-- OpenTelemetry bridge -->
</dependency>
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-exporter-zipkin</artifactId>
</dependency>
```

```yaml
management:
  tracing:
    sampling:
      probability: 1.0    # 1.0 = trace 100%, 0.1 = sample 10%
zipkin:
  tracing:
    endpoint: http://zipkin:9411/api/v2/spans
```

Zero code changes — Spring auto-adds traceId/spanId to every request, DB call, Kafka message.

### OpenTelemetry (OTel)

- Vendor-neutral standard for traces, metrics, and logs
- One SDK, export to any backend (Zipkin, Jaeger, Datadog, AWS X-Ray)
- Becoming the industry standard — if starting fresh, use OTel

```
App → OTel SDK → OTel Collector → Jaeger / Zipkin / Datadog / X-Ray
```

---

## 3. Tracing Backends

| Tool | Type | Best for |
|------|------|----------|
| Zipkin | Open-source, simple | Spring ecosystem, getting started |
| Jaeger | Open-source (CNCF) | Kubernetes, production-grade |
| AWS X-Ray | Managed | AWS-native apps |
| Google Cloud Trace | Managed | GCP apps |
| Datadog APM | SaaS | Full observability platform (paid) |
| Tempo (Grafana) | Open-source | Pairs with Grafana + Loki + Prometheus |

Zipkin/Jaeger are free and self-hosted. Cloud-native options are zero-ops but vendor-locked.

---

## 4. Trace Context Propagation

For tracing to work across services, the traceId must be passed along in HTTP headers.

W3C Trace Context (standard):

```
traceparent: 00-abc123trace-def456span-01
```

```
Client → API Gateway
  Header: traceparent: 00-abc123-span1-01
  → Order Service (reads header, creates child span)
    Header: traceparent: 00-abc123-span2-01
    → Payment Service (reads header, creates child span)
      Header: traceparent: 00-abc123-span3-01
```

Same traceId (abc123) flows through all services. Each creates its own spanId.

Micrometer/OTel auto-propagates — you don't manually pass headers. It instruments RestClient, WebClient, Feign, Kafka, etc. automatically.

For Kafka: traceId is propagated in message headers, not HTTP headers. Same concept.

---

## 5. Identify Bottlenecks

Open a trace in Zipkin/Jaeger → see a waterfall/timeline view:

```
API Gateway     |████|                                    5ms
Order Service   |    |████████████████████|               45ms
  DB Query      |    |██████|                             15ms
  Payment Call  |          |████████████████|              25ms
    Payment DB  |          |████████|                      10ms
Inventory Svc   |                        |████████|       20ms
                0ms                                       95ms
```

Instantly see:

- Payment Service took 25ms — is that normal?
- DB query took 15ms — index missing?
- Are calls sequential when they could be parallel?
- Which span has errors?

Without tracing: "the request is slow" — where? no idea. With tracing: "the Payment DB query in span 5 took 800ms because of a missing index" — precise.

### Sampling

In production, tracing 100% of requests is expensive. Sample instead:

- `probability: 0.1` → trace 10% of requests
- Always trace errors (even if not sampled)
- Increase sampling temporarily for debugging

> Bottom line: logs tell you what happened in one service. Metrics tell you how things are trending. Traces tell you the full story of one request across all services. You need all three.
