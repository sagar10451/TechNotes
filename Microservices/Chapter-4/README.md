# Chapter-4: Service Discovery

## Contents

1. Why: services have dynamic IPs (containers, auto-scaling)
2. Client-side discovery: service queries registry, picks an instance (Eureka + Spring Cloud LoadBalancer)
3. Server-side discovery: load balancer queries registry (AWS ALB, Kubernetes Service)
4. Service registry: Eureka, Consul, Zookeeper
5. Kubernetes DNS-based discovery (service name resolves to cluster IP — no separate registry needed)
6. Health checks and deregistration of unhealthy instances

---

## 1. Why: services have dynamic IPs (containers, auto-scaling)

Service Discovery — how services find each other when IPs are constantly changing.

### The problem

- In microservices, services run in containers, auto-scale up/down, restart, move across nodes
- IPs change every time — you can't hardcode `http://192.168.1.50:8080`
- 3 instances of Order Service today, 7 tomorrow, different IPs each time

Hardcoded (breaks):

```
Payment Service → http://192.168.1.50:8080/orders  ← IP changed, 💥
```

Service Discovery (works):

```
Payment Service → "ORDER-SERVICE" → registry lookup → http://10.0.3.12:8080 ✓
```

### How it works

- Service starts → registers itself with a registry ("I'm Order Service, I'm at 10.0.3.12:8080")
- Service stops → deregisters (or registry detects it's gone via health checks)
- Caller needs Order Service → asks registry → gets current IP(s)

```
Order Service (instance 1) → registers → Service Registry
Order Service (instance 2) → registers →     ↑
Order Service (instance 3) → registers →     |
                                             |
Payment Service → "where is ORDER-SERVICE?" → Registry returns:
  [10.0.3.12:8080, 10.0.3.13:8080, 10.0.3.14:8080]
  → pick one (load balance)
```

### Two patterns

| Pattern | How it works | Who picks the instance |
|---------|-------------|----------------------|
| Client-side discovery | Client queries registry, gets list, picks one | Client (e.g., Ribbon/Spring Cloud LoadBalancer) |
| Server-side discovery | Client calls a load balancer/gateway, it queries registry and routes | Load balancer / gateway |

Client-side:

```
Payment Service → Registry → [IP1, IP2, IP3] → picks IP2 → calls directly
```

Server-side:

```
Payment Service → Load Balancer → Registry → [IP1, IP2, IP3] → LB picks IP2 → forwards
```

### Health checks

- Registry pings services periodically ("are you alive?")
- Service doesn't respond → removed from registry
- Prevents routing traffic to dead instances

### Common tools

| Tool | Type | Environment |
|------|------|-------------|
| Eureka (Netflix) | Dedicated registry | Spring Cloud |
| Consul (HashiCorp) | Registry + config + health checks | Any |
| Kubernetes DNS/Services | Built-in, DNS-based | Kubernetes |
| AWS Cloud Map | Managed service discovery | AWS |
| Zookeeper | Coordination service (older) | Kafka, Hadoop ecosystem |

### Kubernetes approach

- No separate registry needed — K8s does it natively
- Each Service gets a stable DNS name (`order-service.default.svc.cluster.local`)
- K8s tracks pods behind the service, routes traffic to healthy ones
- This is why many teams skip Eureka/Consul when on K8s

### Spring Cloud + Eureka (quick picture)

```
Order Service → @EnableEurekaClient → registers with Eureka Server
Payment Service → @FeignClient("ORDER-SERVICE") → Eureka resolves → load balanced call
```

No hardcoded URLs anywhere. Services only know logical names.

> Bottom line: dynamic infrastructure needs dynamic discovery. The registry is the phone book — services register their number, callers look it up.

---

## 2. Client-side discovery: service queries registry, picks an instance (Eureka + Spring Cloud LoadBalancer)

Client-Side Discovery — the calling service itself queries the registry, gets a list of instances, and picks one. No middleman.

```
Payment Service → Eureka: "where is ORDER-SERVICE?"
  ← [10.0.3.12:8080, 10.0.3.13:8080, 10.0.3.14:8080]
  → picks 10.0.3.13 (round-robin) → calls directly
```

The client does the load balancing — no separate load balancer in between.

### How it works with Eureka + Spring Cloud LoadBalancer

- Eureka Server runs as a separate service (the registry)
- Each service registers on startup, sends heartbeats to stay registered
- Calling service fetches the instance list from Eureka, caches it locally
- Spring Cloud LoadBalancer picks an instance from the cached list

```
┌── Order Service (instance 1) ──→ registers
Eureka Server ◄────┼── Order Service (instance 2) ──→ registers
                   └── Order Service (instance 3) ──→ registers
                                                        ↑
Payment Service ──→ fetches list from Eureka
                ──→ caches locally
                ──→ LoadBalancer picks instance 2
                ──→ calls instance 2 directly
```

### Spring setup

Eureka Server:

```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApp { }
```

```yaml
# Eureka Server application.yml
server:
  port: 8761
eureka:
  client:
    register-with-eureka: false   # it IS the registry
    fetch-registry: false
```

Service (client):

```yaml
# Order Service application.yml
spring:
  application:
    name: ORDER-SERVICE
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
```

Calling service (with Feign or RestClient):

```java
@FeignClient(name = "ORDER-SERVICE")  // logical name, not URL
public interface OrderClient {
    @GetMapping("/orders/{id}")
    Order getOrder(@PathVariable int id);
}
```

Spring Cloud LoadBalancer automatically intercepts `ORDER-SERVICE`, looks up Eureka, picks an instance.

### Load balancing strategies

| Strategy | How |
|----------|-----|
| Round Robin (default) | Cycles through instances: 1 → 2 → 3 → 1 → ... |
| Random | Picks a random instance |
| Custom | Implement `ReactorServiceInstanceLoadBalancer` |

> Note: Ribbon was the old client-side load balancer (Netflix). It's deprecated — Spring Cloud LoadBalancer is the replacement.

### Pros and cons

| Pro | Con |
|-----|-----|
| No extra hop (direct call) | Client is more complex (knows about discovery + LB) |
| Client caches registry — works briefly if Eureka is down | Stale cache can route to dead instances |
| Simple infrastructure | Every service needs Eureka client dependency |

> Client-side discovery = the caller is smart. It knows where everyone is and picks who to talk to.

---

## 3. Server-side discovery: load balancer queries registry (AWS ALB, Kubernetes Service)

Server-Side Discovery — the caller sends the request to a load balancer/proxy, and that middleman queries the registry and routes to a healthy instance. Client stays dumb.

```
Payment Service → Load Balancer → Registry lookup → picks instance → forwards
                                                        ↓
                                              Order Service (instance 2)
```

Client doesn't know about the registry, doesn't cache instances, doesn't pick. It just calls one address.

### How it works

```
Payment Service → http://order-service-lb:8080/orders/123
  ↓
Load Balancer
  ├── knows instances via registry/health checks
  ├── picks healthy instance (round-robin, least connections)
  └── forwards request → Order Service (instance 2)
  ← response back through LB
```

### AWS ALB (Application Load Balancer)

- ALB sits in front of a target group (set of instances/containers)
- ECS/EC2 instances register with the target group automatically
- ALB does health checks, removes unhealthy targets
- No separate registry needed — ALB + target group IS the discovery

```
Client → ALB (order-service.example.com)
  ├── Target Group: [ECS task 1, ECS task 2, ECS task 3]
  ├── Health check: GET /health every 30s
  └── Routes to healthy instance
```

- Auto-scaling adds/removes instances → target group updates automatically
- Caller just knows the ALB DNS name — never individual IPs

### Kubernetes Service

- K8s Service = built-in server-side discovery + load balancing
- Each Service gets a stable DNS name (`order-service.default.svc.cluster.local`)
- K8s tracks which pods are behind the service via label selectors
- kube-proxy routes traffic to healthy pods

```
Payment Pod → http://order-service:8080/orders/123
  ↓
K8s Service (order-service)
  ├── Pod 1 (10.0.0.5) ✓ healthy
  ├── Pod 2 (10.0.0.6) ✓ healthy
  └── Pod 3 (10.0.0.7) ✗ not ready (excluded)
```

- Pods scale up/down → K8s updates endpoints automatically
- No Eureka, no Consul — K8s handles it natively
- This is why most K8s teams don't need a separate service registry

### Client-side vs Server-side

| Aspect | Client-side (Eureka) | Server-side (ALB / K8s) |
|--------|---------------------|------------------------|
| Who picks instance | Client | Load balancer / proxy |
| Client complexity | Smart (knows registry, caches, LB logic) | Dumb (just calls one address) |
| Extra hop | No (direct call) | Yes (through LB) |
| Language agnostic | No (needs client library) | Yes (any language, just HTTP) |
| Infrastructure | Need registry (Eureka) | Need LB (ALB) or platform (K8s) |
| Stale routing risk | Yes (cached list) | No (LB always checks) |

When to use:

- On AWS → ALB + ECS/EKS (natural fit, zero custom code)
- On Kubernetes → K8s Services (built-in, no extra tools)
- Polyglot services (different languages) → server-side is easier, no client library needed

> Server-side discovery = the caller is dumb, the infrastructure is smart. Caller calls one stable address, the platform handles the rest.

---

## 4. Service registry: Eureka, Consul, Zookeeper

Service Registry — a database of available service instances. Services register on startup, deregister on shutdown, and callers query it to find who's alive and where.

All registries do the same core job:

- Service starts → registers (name, IP, port)
- Registry health-checks → removes dead instances
- Caller queries → gets list of healthy instances

### Eureka (Netflix)

- Built for Spring Cloud — first-class integration
- AP system (CAP theorem) — prioritizes availability over consistency
- Peer-to-peer replication (Eureka servers sync with each other)
- Clients cache the registry locally → still works if Eureka goes down temporarily
- Self-preservation mode: if too many instances miss heartbeats at once, Eureka assumes network issue and keeps them registered (avoids mass deregistration)

```
Service → heartbeat every 30s → Eureka Server
Eureka Server ←→ Eureka Server (peer replication)
Caller → fetches registry → cached locally, refreshed every 30s
```

- Best for: Spring Cloud / Java microservices
- Limitation: Java-centric, no built-in config management, no multi-datacenter support out of the box

### Consul (HashiCorp)

- More than a registry — service discovery + config management + health checking + service mesh
- CP system (CAP theorem) — prioritizes consistency over availability
- Uses Raft consensus for leader election and data replication
- Rich health checks: HTTP, TCP, gRPC, script-based, TTL
- Key-Value store built in — use for config, feature flags
- Multi-datacenter support natively
- DNS interface — services discoverable via DNS (`order-service.service.consul`)

```
Service → registers via Consul agent (sidecar on each node)
Consul Agents → report to Consul Servers (Raft cluster)
Caller → DNS query or HTTP API → gets healthy instances
```

- Best for: polyglot environments, multi-DC, when you need config + discovery in one tool
- Limitation: more operational complexity than Eureka

### Zookeeper (Apache)

- Originally built for Hadoop coordination, not specifically for service discovery
- CP system — strong consistency via ZAB protocol (similar to Raft)
- General-purpose coordination: leader election, distributed locks, config management
- Services create ephemeral nodes — node disappears when service disconnects
- Used by Kafka (older versions) for broker coordination

```
Service → creates ephemeral znode: /services/order-service/instance-1
Service dies → znode auto-deleted → watchers notified
Caller → watches /services/order-service → gets notified of changes
```

- Best for: systems already using Zookeeper (Kafka, Hadoop)
- Limitation: complex to operate, not designed for service discovery (bolted on), being phased out (Kafka removed Zookeeper dependency in KRaft mode)

### Quick comparison

| Aspect | Eureka | Consul | Zookeeper |
|--------|--------|--------|-----------|
| CAP | AP (available) | CP (consistent) | CP (consistent) |
| Health checks | Heartbeat only | HTTP, TCP, gRPC, script | Session/ephemeral nodes |
| Config management | No | Yes (KV store) | Yes (znodes) |
| Multi-datacenter | No (needs workaround) | Yes (native) | No |
| DNS support | No | Yes | No |
| Best with | Spring Cloud | Any language, multi-DC | Kafka, Hadoop (legacy) |
| Complexity | Low | Medium | High |

### Which to pick

- Spring Cloud shop → Eureka (simplest integration)
- Polyglot, multi-DC, need config too → Consul
- Already running Zookeeper for Kafka → can reuse, but consider migrating
- On Kubernetes → skip all three, use K8s native service discovery

---

## 5. Kubernetes DNS-based discovery (service name resolves to cluster IP — no separate registry needed)

Kubernetes DNS-Based Discovery — K8s has service discovery built in. No Eureka, no Consul, no extra tools. Just DNS.

### How it works

- You create a K8s Service → it gets a stable DNS name + a virtual IP (ClusterIP)
- Any pod in the cluster can call the service by name → DNS resolves to ClusterIP → kube-proxy routes to a healthy pod

```
Payment Pod → http://order-service:8080/orders/123
  ↓
DNS resolves "order-service" → ClusterIP 10.96.0.15
  ↓
kube-proxy → routes to one of:
  Pod 1 (10.0.0.5) ✓
  Pod 2 (10.0.0.6) ✓
  Pod 3 (10.0.0.7) ✓
```

No hardcoded IPs. No registry client library. Just use the service name as a hostname.

### DNS naming format

```
<service-name>.<namespace>.svc.cluster.local
```

| Call from | How to call |
|-----------|-------------|
| Same namespace | `http://order-service:8080` |
| Different namespace | `http://order-service.production.svc.cluster.local:8080` |

Within the same namespace, just the service name works. K8s DNS handles the rest.

### What happens under the hood

1. You deploy pods with labels: `app: order-service`
2. You create a Service with selector: `app: order-service`
3. K8s finds all pods matching the selector → creates Endpoints list
4. CoreDNS (K8s DNS server) maps service name → ClusterIP
5. kube-proxy maintains iptables/IPVS rules to route ClusterIP → actual pod IPs
6. Pods scale up/down → Endpoints update automatically → routing adjusts

```yaml
apiVersion: v1
kind: Service
metadata:
  name: order-service
spec:
  selector:
    app: order-service    # find pods with this label
  ports:
    - port: 8080          # service port
      targetPort: 8080    # pod port
```

That's it. No registration code, no heartbeats, no client library.

### Service types

| Type | What it does |
|------|-------------|
| ClusterIP (default) | Internal only — accessible within the cluster |
| NodePort | Exposes on each node's IP at a static port |
| LoadBalancer | Provisions external LB (cloud provider — ALB, NLB) |
| Headless (`clusterIP: None`) | No ClusterIP — DNS returns individual pod IPs directly (useful for stateful apps like Kafka, databases) |

Headless Service — when you need to talk to specific pods, not load-balanced:

```
Normal Service:    DNS → ClusterIP → kube-proxy picks a pod
Headless Service:  DNS → [Pod1 IP, Pod2 IP, Pod3 IP] (caller decides)
```

Used for StatefulSets (Kafka brokers, DB replicas) where each pod has identity.

### Why no separate registry on K8s

| Eureka/Consul | K8s DNS |
|---------------|---------|
| Service registers itself | K8s auto-registers via labels/selectors |
| Heartbeat to stay registered | Liveness/readiness probes handle health |
| Client fetches + caches list | DNS resolves, kube-proxy routes |
| Extra infra to run | Built into every K8s cluster |

Running Eureka on K8s = redundant. K8s already does everything Eureka does, natively.

> Bottom line: on Kubernetes, service discovery is free. Create a Service, call it by name. Done.

---

## 6. Health checks and deregistration of unhealthy instances

Health Checks & Deregistration — how the system detects dead/sick instances and stops sending traffic to them.

### The problem

- Service crashes, hangs, or runs out of memory → it's still registered
- Traffic keeps routing to it → requests fail
- Health checks detect this and remove it from the pool

### How it works

```
Registry / LB → pings service periodically ("are you alive?")
  ← 200 OK → healthy, keep routing ✓
  ← no response / 500 → unhealthy, stop routing ✗
  ← still unhealthy after X checks → deregister / remove
```

### Two types of health checks

| Type | How | Used by |
|------|-----|---------|
| Active (pull) | Registry/LB pings the service at intervals | ALB, Consul, K8s |
| Passive (push) | Service sends heartbeats to registry | Eureka |

Active: "I'll check on you every 30s"

Passive: "You tell me you're alive every 30s — if you stop, I assume you're dead"

### Kubernetes — Liveness & Readiness Probes

| Probe | Question | If fails |
|-------|----------|----------|
| Liveness | "Is the process alive?" | K8s restarts the pod |
| Readiness | "Can you handle traffic?" | K8s removes pod from Service endpoints (no traffic) |
| Startup | "Have you finished starting?" | Delays liveness/readiness checks for slow-starting apps |

```yaml
livenessProbe:
  httpGet:
    path: /health/live
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 15
  failureThreshold: 3      # 3 consecutive failures → restart

readinessProbe:
  httpGet:
    path: /health/ready
    port: 8080
  periodSeconds: 10
  failureThreshold: 2      # 2 failures → stop sending traffic
```

Key distinction:

- Liveness fails → pod killed and restarted (it's broken)
- Readiness fails → pod stays alive but gets no traffic (it's busy — DB connection warming up, loading cache)

### Eureka

- Service sends heartbeat every 30s (default)
- Eureka waits 90s without heartbeat → marks as expired → deregisters
- Self-preservation: if >15% of instances miss heartbeats simultaneously, Eureka assumes network partition and keeps them registered (avoids mass deregistration)

```
Service → heartbeat → Eureka ✓
Service → ... silence 90s ... → Eureka removes it ✗
```

### Consul

- Supports HTTP, TCP, gRPC, script-based, TTL checks
- Configurable interval and deregister timeout

```json
{
  "check": {
    "http": "http://localhost:8080/health",
    "interval": "10s",
    "timeout": "2s",
    "deregister_critical_service_after": "60s"
  }
}
```

Fails for 60s → fully deregistered from Consul.

### ALB (AWS)

- Health check: GET /health every 30s
- Healthy threshold: 3 consecutive successes → mark healthy
- Unhealthy threshold: 2 consecutive failures → mark unhealthy → stop routing
- Unhealthy target stays in target group but gets zero traffic. If it recovers, ALB routes again.

### Spring Boot Actuator — the health endpoint

```
GET /actuator/health → { "status": "UP" }
```

- Checks DB connection, disk space, custom components
- Returns UP (200) or DOWN (503)
- This is what registries/LBs hit to check health

### Quick summary

| Platform | Health mechanism | On failure |
|----------|-----------------|------------|
| Eureka | Heartbeat (passive) | Deregisters after 90s silence |
| Consul | Active checks (HTTP/TCP/gRPC) | Deregisters after configurable timeout |
| Kubernetes | Liveness + Readiness probes | Restart (liveness) or remove from traffic (readiness) |
| AWS ALB | HTTP health check | Stops routing, keeps in target group |

> Bottom line: health checks are the garbage collector of service discovery — they clean up dead instances so traffic only goes to services that can actually handle it.
