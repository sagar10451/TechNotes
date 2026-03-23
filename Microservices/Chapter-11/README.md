# Kubernetes (K8s)

Kubernetes (K8s) — container orchestration. Manages deploying, scaling, networking, and healing your containers.

---

## 1. Pods, Deployments, Services, Ingress

| Resource | What | Analogy |
|----------|------|---------|
| Pod | Smallest unit — one or more containers running together | One instance of your app |
| Deployment | Manages pods — desired replicas, rolling updates, rollback | "I want 3 copies of Order Service running" |
| Service | Stable network endpoint for a set of pods (DNS + load balancing) | Internal phone number that never changes |
| Ingress | External HTTP routing — maps URLs to internal services | Front door with path-based routing |

```
Internet → Ingress (api.example.com)
  ├── /orders   → Order Service (ClusterIP) → Pod 1, Pod 2, Pod 3
  ├── /payments → Payment Service (ClusterIP) → Pod 1, Pod 2
  └── /users    → User Service (ClusterIP) → Pod 1
```

```yaml
# Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: order-service
  template:
    metadata:
      labels:
        app: order-service
    spec:
      containers:
        - name: order-service
          image: order-service:1.2.0
          ports:
            - containerPort: 8080
```

```yaml
# Service
apiVersion: v1
kind: Service
metadata:
  name: order-service
spec:
  selector:
    app: order-service
  ports:
    - port: 8080
```

```yaml
# Ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: api-ingress
spec:
  rules:
    - host: api.example.com
      http:
        paths:
          - path: /orders
            pathType: Prefix
            backend:
              service:
                name: order-service
                port:
                  number: 8080
```

---

## 2. ConfigMaps and Secrets

ConfigMap — non-sensitive config:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: order-config
data:
  LOG_LEVEL: "INFO"
  CACHE_TTL: "300"
```

Secret — sensitive data (base64):

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: order-secrets
data:
  DB_PASSWORD: cGFzc3dvcmQ=
```

Inject into pods:

```yaml
envFrom:
  - configMapRef:
      name: order-config
  - secretRef:
      name: order-secrets
```

---

## 3. Readiness vs Liveness vs Startup Probe

| Probe | Question | On failure |
|-------|----------|------------|
| Startup | "Have you finished starting?" | Keep waiting (delays other probes) |
| Liveness | "Are you alive?" | Kill and restart the pod |
| Readiness | "Can you handle traffic?" | Remove from Service (no traffic, but pod stays alive) |

```yaml
startupProbe:
  httpGet:
    path: /health/started
    port: 8080
  failureThreshold: 30
  periodSeconds: 2          # up to 60s to start

livenessProbe:
  httpGet:
    path: /health/live
    port: 8080
  periodSeconds: 15
  failureThreshold: 3       # 3 fails → restart

readinessProbe:
  httpGet:
    path: /health/ready
    port: 8080
  periodSeconds: 10
  failureThreshold: 2       # 2 fails → stop traffic
```

Startup probe protects slow-starting apps (Spring Boot loading context) from being killed by liveness probe too early.

---

## 4. Horizontal Pod Autoscaler (HPA)

Automatically scale pods based on metrics.

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: order-service-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: order-service
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70    # scale up when CPU > 70%
```

```
CPU at 40% → 2 pods (min)
CPU at 75% → HPA adds pods → 4 pods
CPU at 90% → more pods → 7 pods
Traffic drops → scale down → back to 2 pods
```

Can scale on CPU, memory, or custom metrics (requests/sec, queue depth).

---

## 5. Resource Requests and Limits

| Setting | What | Purpose |
|---------|------|---------|
| Request | Minimum guaranteed resources | Scheduler uses this to place pods on nodes |
| Limit | Maximum allowed resources | Pod gets killed/throttled if it exceeds this |

```yaml
resources:
  requests:
    cpu: "250m"       # 0.25 CPU cores guaranteed
    memory: "512Mi"   # 512MB guaranteed
  limits:
    cpu: "1000m"      # max 1 CPU core
    memory: "1Gi"     # max 1GB — OOMKilled if exceeded
```

- No requests set → pod can be scheduled anywhere, might starve
- No limits set → pod can consume entire node's resources
- Memory limit exceeded → OOMKilled (pod killed)
- CPU limit exceeded → throttled (slowed, not killed)

---

## 6. Deployment Strategies

### Rolling update (default)

```
v1 v1 v1 → v2 v1 v1 → v2 v2 v1 → v2 v2 v2
(gradually replace old pods with new ones)
```

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1          # 1 extra pod during update
    maxUnavailable: 0    # zero downtime
```

### Blue-green

```
Blue (v1): running, serving traffic
Green (v2): deployed, tested, idle
Switch: route all traffic from blue → green instantly
Rollback: switch back to blue
```

Two full environments. Instant switch. Expensive (double resources).

### Canary

```
v1 v1 v1 v1 (90% traffic)
v2 (10% traffic) ← test with real users
→ looks good → gradually shift: 25% → 50% → 100%
→ looks bad → route 100% back to v1
```

Gradual rollout. Catch issues early with minimal user impact. Needs traffic splitting (Istio, Ingress controller, or service mesh).

---

## 7. Service Mesh (Istio / Linkerd)

A dedicated infrastructure layer for service-to-service communication. Handles networking concerns so your application code doesn't have to.

### Sidecar proxy pattern

```
Pod:
┌─────────────────────────────┐
│ Order Service (your code)   │
│         ↕                   │
│ Envoy Proxy (sidecar)      │ ← injected automatically
└─────────────────────────────┘
```

Every pod gets a sidecar proxy. All traffic goes through it. Your app doesn't know it's there.

### What the mesh handles

| Feature | What |
|---------|------|
| mTLS | Automatic mutual TLS between all services — encrypted, authenticated, zero code |
| Traffic management | Canary routing, retries, timeouts, circuit breaking — configured in YAML, not code |
| Observability | Automatic metrics, traces, access logs for every service call |
| Traffic splitting | Route 10% to v2, 90% to v1 (canary) |
| Fault injection | Inject delays/errors to test resilience |

- Without mesh: each service implements retries, TLS, tracing, circuit breakers in code
- With mesh: sidecar handles all of it — app just makes plain HTTP calls

### Istio vs Linkerd

| Aspect | Istio | Linkerd |
|--------|-------|---------|
| Complexity | High (feature-rich) | Low (simpler, lighter) |
| Proxy | Envoy | Linkerd2-proxy (Rust, lightweight) |
| Features | Everything (traffic, security, observability, policy) | Core features, less config |
| Resource usage | Heavier | Lighter |
| Best for | Complex requirements, fine-grained control | Simplicity, quick setup |

Use a service mesh when you have many services and cross-cutting concerns (mTLS, observability, traffic control) are becoming painful to implement per-service. Overkill for <10 services.
