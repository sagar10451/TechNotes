# Design Patterns Summary

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
