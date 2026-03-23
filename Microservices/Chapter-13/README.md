# Security

Security — in microservices, the attack surface is larger. More services, more network calls, more endpoints to protect.

---

## 1. Authentication: OAuth 2.0 + OpenID Connect

| Protocol | What it does |
|----------|-------------|
| OAuth 2.0 | Authorization — "what are you allowed to do?" (issues access tokens) |
| OpenID Connect (OIDC) | Authentication layer on top of OAuth — "who are you?" (issues ID tokens) |

```
User → Login page (Keycloak/Auth0/Okta)
  → authenticates → gets JWT (access token + ID token)
  → sends JWT with every request: Authorization: Bearer <token>
  → API Gateway / Service validates token
```

| Provider | Type |
|----------|------|
| Keycloak | Open-source, self-hosted |
| Auth0 | SaaS, managed |
| Okta | SaaS, enterprise |
| AWS Cognito | AWS-managed |

JWT contains claims: userId, roles, scopes, expiry — services can read these without calling the auth server every time.

---

## 2. Token Propagation

JWT flows through the entire call chain. Each service can read it to know who the user is and what they're allowed to do.

```
Client → JWT → API Gateway (validates signature, expiry)
  → Order Service (reads userId, roles from JWT)
  → Payment Service (same JWT forwarded, reads roles)
  → Inventory Service (same JWT forwarded)
```

Two approaches:

| Approach | How | Trade-off |
|----------|-----|-----------|
| Validate at gateway only | Gateway checks JWT, internal services trust each other | Simpler, but internal services have no auth |
| Validate at every service | Each service validates JWT independently | More secure (zero trust), slight overhead |

Gateway validation is common. Zero trust (validate everywhere) is more secure — depends on your threat model.

---

## 3. Service-to-Service Auth

Users have JWTs. But what about services calling other services directly (no user context)?

| Method | How | When |
|--------|-----|------|
| mTLS | Both sides present TLS certificates — mutual authentication | Service mesh (Istio auto-configures), high security |
| Service accounts | Service gets its own JWT via client_credentials OAuth flow | Service-to-service with identity |
| API keys | Static key in header | Simple internal calls, less secure |

mTLS:

```
Order Service ←──mTLS──→ Payment Service
Both present certificates → both verified → encrypted channel
(Istio does this automatically for every service-to-service call)
```

Client credentials flow:

```
Order Service → Keycloak: "I'm order-service, here's my client_secret"
  ← JWT (service token, no user context)
  → Payment Service: Authorization: Bearer <service-token>
```

---

## 4. Zero Trust

Don't trust anything — not even internal traffic. Every call is authenticated and authorized.

Traditional (castle-and-moat):

```
Firewall protects the perimeter → everything inside is trusted
✗ If attacker gets inside, they move freely
```

Zero trust:

```
Every service verifies every request — even from internal services
✓ Attacker compromises one service → can't access others without valid credentials
```

Zero trust checklist:

- Every service validates JWT or mTLS — no exceptions
- Least privilege — service only has permissions it needs
- Network policies — pods can only talk to pods they need to
- No implicit trust based on being "internal"

```yaml
# K8s NetworkPolicy — order-service can only talk to order-db and payment-service
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
spec:
  podSelector:
    matchLabels:
      app: order-service
  egress:
    - to:
        - podSelector:
            matchLabels:
              app: order-db
        - podSelector:
            matchLabels:
              app: payment-service
```

---

## 5. Secrets Management

Never in code. Never in Git. Never in plain config files.

| Tool | Environment |
|------|-------------|
| HashiCorp Vault | Any (most powerful, dynamic secrets, rotation) |
| AWS Secrets Manager | AWS (managed, auto-rotation) |
| AWS Parameter Store | AWS (simpler, cheaper) |
| K8s Secrets | Kubernetes (base64, not encrypted by default) |

```
✗ application.yml: db.password=mysecret (committed to Git)
✗ ENV in Dockerfile: ENV DB_PASSWORD=mysecret
✓ Runtime: service → Vault/Secrets Manager → gets credentials → uses them
```

Vault dynamic secrets: Vault generates short-lived DB credentials per service, auto-rotates. Service never sees a long-lived password.

---

## 6. HTTPS Everywhere

All traffic encrypted — external and internal.

| Where | How |
|-------|-----|
| External (client → gateway) | TLS termination at gateway/LB (ACM certificate on ALB) |
| Internal (service → service) | mTLS via service mesh (Istio) or TLS at each service |

```
Client ──HTTPS──→ API Gateway (TLS terminated)
  ──mTLS──→ Order Service
  ──mTLS──→ Payment Service
```

- TLS termination at gateway: gateway decrypts, internal traffic is plain HTTP. Simpler but internal traffic is unencrypted.
- mTLS everywhere (via Istio): every hop is encrypted. More secure. Service mesh handles certificates automatically.

---

## 7. Input Validation at Every Service Boundary

Don't trust upstream services. Validate everything at your boundary.

```
✗ "API Gateway validated it, so I don't need to"
  → Gateway bug, or someone calls your service directly → injection, bad data

✓ Every service validates its own input — regardless of who's calling
```

```java
public Order createOrder(@Valid @RequestBody OrderRequest request) {
    // @Valid triggers Bean Validation
    // Even if Order Service is called by another internal service
}
```

What to validate:

- Required fields present
- Types correct (number is a number, not a string)
- Ranges (quantity > 0, price > 0)
- String length limits (prevent oversized payloads)
- No SQL injection, XSS (sanitize strings)
- Business rules (can't order negative quantity)

> Defense in depth: gateway validates → service validates → DB constraints validate. Multiple layers, each catches what the previous missed.
