# Chapter-1: Fundamentals

## 1. Distributed Systems

- Any system where components run on multiple machines and communicate over a network
- Deals with problems like network failures, latency, data consistency, clock sync
- Microservices are inherently distributed (services on different nodes)
- A monolith can also be distributed — e.g., monolith + separate DB server, or multiple monolith replicas behind a load balancer

## 2. Event-Driven Systems

- A communication pattern where components react to events (something happened) rather than direct calls (do this)
- Components produce and consume events via a broker (Kafka, RabbitMQ, SNS/SQS)
- Enables async, loosely coupled communication
- Key patterns: event notification, event sourcing, CQRS

## 3. Monolith vs Microservices

### Monolith

- Single deployable unit, all features in one codebase
- Shared database, tightly coupled modules
- Simple to develop, test, and deploy initially
- Scaling means scaling the entire app (vertical scaling)
- One failure can bring down the whole system
- Tech stack is uniform across the app
- Good for small teams and simple apps

### Microservice

- App split into small, independent services by business capability
- Each service has its own database (database per service pattern)
- Services communicate via REST, gRPC, or message queues
- Deploy, scale, and update each service independently (horizontal scaling)
- Fault isolation — one service failing doesn't crash others
- Each service can use a different tech stack (polyglot)
- Adds complexity: service discovery, distributed tracing, eventual consistency
- Good for large teams and complex, evolving systems

### Key tradeoffs

| Aspect | Monolith | Microservice |
|---|---|---|
| Deployment | Single unit | Per service |
| Scaling | Vertical | Horizontal, granular |
| Complexity | Low initially | High (network, data consistency) |
| Team structure | One team | Multiple autonomous teams |
| Data management | Shared DB | Distributed (eventual consistency) |
| Failure impact | System-wide | Isolated |

### When to choose what:

- Start monolith when domain is unclear or team is small, early-stage product
- Move to microservices when scaling bottlenecks or team coordination becomes painful
- Common path: Monolith → Modular Monolith → Microservices (strangler fig pattern)

## 4. How to decompose a monolith

### Why decompose?

- Monolith becomes hard to scale, deploy, and maintain as it grows
- Goal: break it into smaller, independent services without a big-bang rewrite

### 1. By Business Capability

- Split based on what the business does
- Each capability becomes its own service
- Example for an e-commerce app:
  - Order Management
  - Payment Processing
  - Inventory
  - User Management
  - Notifications
- Ask: "What does the business do?" → each answer is a potential service
- Stable boundaries — business capabilities rarely change

### 2. By Subdomain (DDD approach)

- Split based on DDD's bounded contexts and subdomains
- Three types of subdomains:
  - Core — your competitive advantage (e.g., recommendation engine)
  - Supporting — needed but not unique (e.g., order tracking)
  - Generic — commodity stuff (e.g., email sending, auth)
- Each subdomain → bounded context → service
- Ask: "Where does the domain language change?" → that's a boundary
- More nuanced than business capability — focuses on where models differ

### Business Capability vs Subdomain

| Aspect | Business Capability | Subdomain |
|---|---|---|
| Driven by | What the business does | How the domain model differs |
| Granularity | Coarser | Finer |
| Approach | Org chart / business functions | Domain expert conversations |
| Stability | Very stable | Stable |

In practice they often overlap. Subdomain gives you sharper boundaries.

### 3. Strangler Fig Pattern

- Named after a vine that slowly wraps around a tree and replaces it
- Incrementally replace monolith pieces — no big-bang rewrite
- How it works:
  1. Put a facade/proxy (API gateway) in front of the monolith
  2. Pick one feature/module to extract
  3. Build it as a new service
  4. Route traffic for that feature to the new service
  5. Repeat until the monolith is hollow or gone

**Before:**

```
Client → Monolith (everything)
```

**During:**

```
Client → API Gateway
├── /orders  → New Order Service
├── /payments → New Payment Service
└── /everything-else → Monolith (shrinking)
```

**After:**

```
Client → API Gateway → All Microservices
```

- Key rules:
  - Never add new features to the monolith — build them as services
  - Migrate data gradually (dual writes or change data capture)
  - Keep the monolith working at every step

### Practical approach: combine all three

- Use subdomain/business capability analysis to decide where to cut
- Use strangler fig to execute the migration incrementally
- Start with the module that has the most pain (scaling issues, frequent changes, team bottleneck)

## 5. Service boundaries — how to decide what's one service vs two

Core principle: a service should be independently deployable, own its data, and change for one reason.

### Signs it should be ONE service

- They always change together (same PR, same deploy)
- They share the same data/tables tightly
- Splitting them would require constant back-and-forth calls
- They serve the same business capability
- One can't function without the other

### Signs it should be TWO services

- They change at different rates or for different reasons
- Different teams own them
- They need to scale independently
- They have different SLAs (e.g., payments must be 99.99%, notifications can be 99.9%)
- The domain language differs (same word, different meaning)
- Different data storage needs (one needs SQL, other needs search index)

### Decision checklist

| Question | One service | Two services |
|---|---|---|
| Do they change together? | Yes | No |
| Can they deploy independently? | No | Yes |
| Do they share a database? | Yes, tightly | No, or loosely |
| Different scaling needs? | No | Yes |
| Different team ownership? | No | Yes |
| Would splitting cause chatty calls? | Yes | No |
| Different domain language? | No | Yes |

### Rules of thumb

- If two things need a synchronous call between them for every single request, they're probably one service
- If you can't deploy one without deploying the other, they're one service pretending to be two (distributed monolith)
- A service should be owned by one team (two-pizza team rule)
- Start bigger, split later — it's easier to split a well-structured service than to merge two broken ones
- One aggregate (DDD) = usually one service, or part of one. Never split an aggregate across services

### Common mistakes

- Too fine-grained: one service per entity (UserService, AddressService, PhoneService) → chatty, fragile
- Too coarse: everything in one service → back to monolith
- Splitting by technical layer (API service, DB service, logic service) instead of business capability → tight coupling across layers
- Ignoring data ownership: two services sharing one database = hidden coupling

### Quick mental model

**Good boundary:**

```
Order Service ←(async event)→ Notification Service
(loosely coupled, independent data, independent deploy)
```

**Bad boundary:**

```
Order Service ←(sync call every request)→ OrderLine Service
(too granular, can't work alone, should be one service)
```

When in doubt, keep it together. You can always split later when the pain is real.

## 6. Conway's Law

Conway's Law says your system's architecture will naturally mirror your team's communication structure. So instead of letting it happen accidentally, design your teams intentionally to get the architecture you want.
