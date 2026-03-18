# Chapter-2: Inter-Service Communication

## Synchronous:

### 1. REST

REST is a way to expose backend endpoints to the outside world or communicate between services, over HTTP using standard methods: GET, POST, PUT, DELETE.

- Data format is typically JSON (lightweight, human-readable)
- Stateless — each request contains all the info needed, server doesn't remember previous calls

In microservices context:

- Service A makes an HTTP call to Service B and waits for a response (synchronous)
- Easy to implement, easy to debug (standard HTTP status codes — 200, 404, 500)
- Creates runtime coupling — if Service B is slow or down, Service A is stuck waiting
- Mitigation: use timeouts, retries, and circuit breakers (e.g., Resilience4j) to handle failures gracefully

**When to use**

- Simple request-response interactions (get me this data, create this resource)
- When the caller needs an immediate answer
- CRUD operations between services

**When to avoid**

- Long chains: A → B → C → D (latency adds up, failure risk multiplies)
- Fire-and-forget scenarios (use async messaging instead)
- High-throughput event streams

#### Famous HTTP codes:

**1xx — Informational**

- 100 Continue — keep going, server received the initial part

**2xx — Success**

- 200 OK — request succeeded (the happy path)
- 201 Created — resource created successfully (after POST)
- 204 No Content — success but nothing to return (common for DELETE)

**3xx — Redirection**

- 301 Moved Permanently — resource moved to a new URL forever
- 302 Found — resource temporarily at a different URL
- 304 Not Modified — cached version is still valid, no need to re-download

**4xx — Client Errors (your fault)**

- 400 Bad Request — malformed request, invalid data
- 401 Unauthorized — not authenticated (who are you?)
- 403 Forbidden — authenticated but not allowed (you can't do this)
- 404 Not Found — resource doesn't exist
- 405 Method Not Allowed — e.g., sending DELETE to an endpoint that only supports GET
- 408 Request Timeout — server tired of waiting for your request
- 409 Conflict — conflict with current state (e.g., duplicate entry)
- 429 Too Many Requests — rate limited, slow down

**5xx — Server Errors (server's fault)**

- 500 Internal Server Error — generic server blew up
- 502 Bad Gateway — server got an invalid response from upstream
- 503 Service Unavailable — server is overloaded or down for maintenance
- 504 Gateway Timeout — upstream server didn't respond in time

**Quick memory trick**

- 2xx = all good
- 4xx = you messed up
- 5xx = server messed up

#### More about HTTP:

- HTTP = protocol
- HTTP/1.0 = released in (1996)
- HTTP/2 = released in (2015)
- HTTP/3 = released in (2022)
- HTTPS = HTTP version + TLS encryption

#### Main alternatives to REST for exposing/communicating with your backend:

**Synchronous (request-response)**

| Alternative | What it is | When to use over REST |
|---|---|---|
| gRPC | Uses Protocol Buffers (binary, not JSON), HTTP/2, strongly typed contracts | High performance, low latency, service-to-service. Faster than REST but harder to debug |
| GraphQL | Single endpoint, client asks for exactly the fields it needs | Frontend-heavy apps where clients need flexible queries. Avoids over-fetching/under-fetching |
| WebSocket | Persistent two-way connection between client and server | Real-time apps — chat, live scores, stock tickers, gaming |
| SOAP | XML-based, strict contracts (WSDL), heavy | Legacy enterprise systems, banking. Mostly outdated for new projects |

**Asynchronous (fire and forget / event-based)**

| Alternative | What it is | When to use over REST |
|---|---|---|
| Message Queues (RabbitMQ, SQS) | Service sends a message to a queue, another service picks it up later | When caller doesn't need an immediate response. Decouples services |
| Event Streaming (Kafka) | Services publish events to a log, consumers read at their own pace | High-throughput event streams, event sourcing, real-time data pipelines |

**Quick comparison**

- Need simple CRUD API for a web/mobile app? → REST
- Need speed between backend services? → gRPC
- Need flexible frontend queries? → GraphQL
- Need real-time push to client? → WebSocket
- Need decoupled async communication? → Message queues / Kafka

REST is the default choice for most cases. You pick alternatives when REST's limitations (text-based, over-fetching, synchronous, request-response only) become a bottleneck for your specific use case.

#### Can one application use both REST and grpc or multiple?

Example:

```
Mobile/Browser → REST (JSON) → Order Service
├── gRPC → Inventory Service (fast internal call)
├── Kafka → Notification Service (async, fire & forget)
└── WebSocket → Client (real-time order tracking)
```

- REST for external clients (browsers, mobile apps) — easy to consume, human-readable
- gRPC for internal service-to-service — faster, typed contracts
- Kafka/queues for async stuff — notifications, analytics, logging
- WebSocket for real-time — live updates to the user

**Why mix?**

- External clients (browsers) can't easily use gRPC, so you expose REST/GraphQL for them
- Internally, services benefit from gRPC's speed and type safety
- Some operations don't need a response, so async messaging makes more sense

There's no rule that says you must stick to one. Most production microservice systems use 2-3 communication styles depending on the context.

### 2. gRPC (Google Remote Procedure Call)

- Developed by Google, open-source framework for service-to-service communication
- Built on HTTP/2 — gets multiplexing, header compression, and streaming for free

#### Protocol Buffers (protobuf)

- gRPC's data format (like JSON is to REST)
- Binary format — not human-readable, but much smaller and faster to serialize/deserialize than JSON
- You define a .proto file as a contract:

```protobuf
service OrderService {
  rpc GetOrder (OrderRequest) returns (OrderResponse);
}

message OrderRequest {
  int32 order_id = 1;
}

message OrderResponse {
  int32 order_id = 1;
  string status = 2;
  double total = 3;
}
```

- From this .proto file, code is auto-generated in any language (Java, Go, Python, etc.)

#### Strongly typed

- The .proto contract defines exact types for every field
- Both client and server know the exact shape of data at compile time
- No guessing, no runtime surprises — if it compiles, the contract is valid
- Compare to REST/JSON where you can send anything and hope the other side understands

#### Streaming support (thanks to HTTP/2)

- Unary: one request → one response (like REST)
- Server streaming: one request → stream of responses (e.g., live price updates)
- Client streaming: stream of requests → one response (e.g., uploading chunks)
- Bidirectional streaming: both sides stream simultaneously (e.g., chat)

#### Why great for internal service-to-service

- Faster than REST — binary is smaller, HTTP/2 is efficient
- Type safety — contract-first, auto-generated code, fewer integration bugs
- Streaming — REST can't do this natively
- Language agnostic — generate client/server code in any language from the same .proto

#### Why not used for external/browser clients

- Browsers don't natively support gRPC (need gRPC-Web as a workaround)
- Binary format is not human-readable — hard to debug with curl or browser dev tools
- REST/JSON is much easier for frontend devs to consume

#### gRPC vs REST quick comparison

| Aspect | REST | gRPC |
|---|---|---|
| Format | JSON (text) | Protobuf (binary) |
| Speed | Slower | Faster |
| Contract | Loose (OpenAPI optional) | Strict (.proto required) |
| Streaming | No (workarounds exist) | Native support |
| Browser support | Native | Needs gRPC-Web |
| Debugging | Easy (curl, browser) | Harder (binary) |
| Best for | External APIs, CRUD | Internal service-to-service, real-time |

### 3. GraphQL

- Developed by Facebook (2012, open-sourced 2015)
- A query language for APIs — single endpoint, client asks for exactly what it needs
- Runs over HTTP (usually POST to /graphql)

#### How it works

Server defines a schema (what data is available):

```graphql
type Order {
  id: ID
  status: String
  total: Float
  items: [OrderItem]
  customer: Customer
}

type Query {
  getOrder(id: ID!): Order
}
```

Client sends a query asking for only the fields it needs:

```graphql
# Mobile app — just needs status
{
  getOrder(id: "123") {
    status
  }
}

# Web app — needs everything
{
  getOrder(id: "123") {
    id
    status
    total
    items { name, price }
    customer { name, email }
  }
}
```

One endpoint, different responses based on what the client asks

#### Client-driven queries

- Client decides the shape of the response, not the server
- Solves two big REST problems:
  - Over-fetching: REST returns all fields even if you need one (GET /orders/123 returns everything)
  - Under-fetching: need data from multiple REST endpoints → multiple round trips (/orders/123 + /customers/456 + /items?orderId=123)
- GraphQL gets it all in one request

#### BFF Pattern (Backend for Frontend)

- GraphQL fits naturally as a BFF layer
- Different clients (web, mobile, TV) have different data needs
- Instead of building separate REST APIs for each client, put a GraphQL layer in front:

```
Mobile App ──┐
Web App ─────┼── GraphQL BFF ──→ Order Service (REST/gRPC)
TV App ──────┘                ──→ User Service (REST/gRPC)
                              ──→ Inventory Service (REST/gRPC)
```

GraphQL aggregates data from multiple backend services and lets each client query what it needs

#### Why not ideal for service-to-service

- Services know exactly what data they need — the flexibility of GraphQL is unnecessary
- Adds complexity (schema stitching, resolvers) for no benefit internally
- gRPC or REST is simpler and faster for backend-to-backend
- GraphQL's strength is handling diverse client needs — services don't have that problem

#### GraphQL vs REST vs gRPC

| Aspect | REST | GraphQL | gRPC |
|---|---|---|---|
| Endpoints | Multiple (/users, /orders) | Single (/graphql) | Per service method |
| Data fetching | Fixed response | Client picks fields | Fixed (protobuf) |
| Over/under-fetching | Common problem | Solved | Not applicable |
| Best for | Simple CRUD, external APIs | Frontend-heavy apps, multiple clients | Internal service-to-service |
| Learning curve | Low | Medium | Medium-high |

**When to use GraphQL**

- Multiple clients needing different data shapes
- Complex frontend with lots of nested/related data
- As a BFF layer in front of microservices

**When to avoid**

- Simple CRUD APIs (REST is simpler)
- Service-to-service communication (gRPC is better)
- File uploads, real-time streaming (not GraphQL's strength)

### 4. RestClient / WebClient / OpenFeign (Java/Spring HTTP Clients)

REST is just a concept — "use HTTP, send JSON, use GET/POST." But your Java code needs actual libraries to open connections, set headers, serialize/deserialize JSON, send requests, read responses, and handle errors/timeouts. That's what these clients do.

#### RestClient (Spring 6.1+)

- Modern, fluent, synchronous HTTP client
- Replacement for the deprecated RestTemplate

```java
Order order = restClient.get()
    .uri("/orders/123")
    .retrieve()
    .body(Order.class);
```

#### WebClient (Spring WebFlux)

- Non-blocking, asynchronous — thread doesn't wait for response
- Best for high-throughput or reactive applications

```java
Mono<Order> order = webClient.get()
    .uri("/orders/123")
    .retrieve()
    .bodyToMono(Order.class);
```

#### OpenFeign (Spring Cloud)

- Declarative — write an interface, Spring generates the HTTP client
- Built-in integration with service discovery, load balancing, circuit breakers

```java
@FeignClient(name = "order-service")
public interface OrderClient {
    @GetMapping("/orders/{id}")
    Order getOrder(@PathVariable int id);
}

// call it like a normal method
orderClient.getOrder(123);
```

**When to use what**

- Standard Spring Boot app → RestClient
- Need async/non-blocking → WebClient
- Microservices with Spring Cloud (Eureka, LB) → OpenFeign

Every language has its own HTTP clients (Python has requests, JS has axios). These are just Java/Spring's options to make REST calls.

### 5. Timeouts, retries, circuit breakers on every sync call (non-negotiable)

#### Why non-negotiable?

- In microservices, every sync call (REST/gRPC) goes over the network
- Networks are unreliable — services go down, slow down, or get overloaded
- Without protection, one slow service can cascade and take down your entire system

#### 1. Timeouts

- Set a max time you're willing to wait for a response
- Without it, your thread hangs forever waiting for a dead/slow service
- Two types:
  - Connection timeout — how long to wait to establish a connection (e.g., 2s)
  - Read/response timeout — how long to wait for data after connecting (e.g., 5s)
- If exceeded, fail fast and move on

Without timeout:

```
Order Service → Payment Service (down)
Thread hangs... 30s... 60s... threads exhausted... Order Service dies too
```

With timeout (3s):

```
Order Service → Payment Service (down)
3s later → timeout → return error → thread freed
```

#### 2. Retries

- If a call fails, try again — it might be a temporary glitch
- Rules:
  - Only retry on transient errors (network timeout, 503) — not on 400/404
  - Limit retry count (e.g., max 3 attempts)
  - Use exponential backoff — wait longer between each retry (1s, 2s, 4s) to avoid hammering a struggling service
  - Add jitter (random delay) so all clients don't retry at the exact same time

```
Attempt 1: failed (timeout)
  wait 1s
Attempt 2: failed (503)
  wait 2s + random jitter
Attempt 3: success ✓
```

Danger: retrying without backoff on a struggling service = making it worse (retry storm)

#### 3. Circuit Breaker

- Inspired by electrical circuit breakers — if too many failures, stop trying
- Prevents your service from repeatedly calling a service that's clearly down
- Three states:

```
CLOSED (normal)
  ↓ failures exceed threshold (e.g., 5 failures in 10s)
OPEN (tripped — all calls fail immediately, no network call)
  ↓ after a wait period (e.g., 30s)
HALF-OPEN (test — allow a few calls through)
  ↓ if they succeed → back to CLOSED
  ↓ if they fail → back to OPEN
```

- When open: return a fallback response (cached data, default value, error message) instead of waiting and failing

#### How they work together

```
Request → Timeout (don't wait forever)
        → Retry (try again with backoff)
        → Circuit Breaker (stop trying if it's clearly dead)
        → Fallback (return something useful anyway)
```

#### Java/Spring tools

- Resilience4j — the go-to library for all three (timeout, retry, circuit breaker, rate limiter, bulkhead)
- Spring Cloud Circuit Breaker — abstraction over Resilience4j
- Hystrix — Netflix's original library, now deprecated. Use Resilience4j instead.

Key takeaway: every sync call without timeout + retry + circuit breaker is a ticking time bomb. One slow dependency will eventually cascade and bring down everything. These aren't optional — they're the cost of doing distributed systems.

## Asynchronous:

### 1. Kafka

Kafka = recording a fact → "This thing happened"

- Message says: "Order was placed" (an event — something that already happened)
- It stays in the log forever (or for configured time)
- Anyone interested can read it now or later

With events, the producer just says "this happened." It doesn't tell anyone what to do. Any service can subscribe and react however it wants — even services that didn't exist when the event was published.

**Why keep events around (not delete after reading)?**

- New service added 3 months later? It can replay all past events and catch up
- Bug in consumer? Fix it, replay events, reprocess
- Need analytics on what happened last week? Events are still there
- This is called event sourcing — your event log is the source of truth

**Fanout** = one message goes to multiple consumers. RabbitMQ, SNS+SQS, Kafka — all can do fan-out.

Kafka way:

```
Order Service → "OrderPlaced" → Kafka Topic
  ← Payment Service reads
  ← Email Service reads
  ← Inventory Service reads
```

AWS way (SNS + SQS):

```
Order Service → "OrderPlaced" → SNS Topic
  → SQS Queue 1 → Payment Service
  → SQS Queue 2 → Email Service
  → SQS Queue 3 → Inventory Service
```

Same idea — producer publishes once, multiple consumers get it. SNS handles the fan-out, SQS gives each consumer its own queue.

#### What Kafka does (full picture)

- Event streaming — continuous flow of events in real-time (user clicks, transactions, sensor data)
- Event sourcing — store every state change as an event, rebuild state by replaying events
- Event replay — new service? replay past events to catch up. Bug? fix and reprocess.
- Stream processing — transform, filter, aggregate events in real-time using Kafka Streams or ksqlDB (e.g., "count orders per minute", "detect fraud patterns")
- Data pipeline — move data between systems (DB → Kafka → data warehouse/analytics)
- Log aggregation — collect logs from hundreds of services into one place
- Change Data Capture (CDC) — capture database changes as events (using Debezium + Kafka)

#### Where Kafka is the must-use

| Use case | Why Kafka |
|---|---|
| High-throughput event streaming (millions/sec) | Built for it — partitioned, distributed, fast |
| Event sourcing | Events are retained, replayable — Kafka is the log |
| Real-time data pipelines | DB → Kafka → Elasticsearch/Data Lake/Analytics |
| Log/metrics aggregation | Handles massive volume from hundreds of services |
| CDC (database change streaming) | Debezium + Kafka is the standard |
| Multiple consumers need the same data | Consumer groups, fan-out, replay — all built-in |
| Ordering matters at scale | Ordering guaranteed per partition |

#### Where Kafka is overkill / others are better

| Use case | Better choice | Why |
|---|---|---|
| Simple task queue (process jobs one by one) | RabbitMQ / SQS | Kafka is too heavy for simple work distribution |
| Delayed/scheduled messages | RabbitMQ | Native support for delayed delivery. Kafka doesn't have this. |
| Priority queues | RabbitMQ | RabbitMQ supports message priority natively |
| Complex routing (route message X to queue Y based on rules) | RabbitMQ | Exchanges + routing keys are built for this |
| Simple decoupling on AWS, zero ops | SQS | Fully managed, no brokers to run, pay per message |
| Request-reply pattern (send and wait for response) | RabbitMQ | Built-in reply queues. Kafka isn't designed for this. |
| Low message volume (few hundred/day) | SQS / RabbitMQ | Kafka cluster is expensive and complex for low volume |
| Dead letter handling | SQS / RabbitMQ | Simpler DLQ setup. Kafka needs custom implementation. |

#### Simple decision guide

- "I need to stream and replay millions of events" → Kafka
- "I need a job queue to distribute tasks to workers" → RabbitMQ or SQS
- "I'm on AWS and want zero infrastructure management" → SQS + SNS
- "I need smart routing, priorities, delays" → RabbitMQ
- "I need real-time data pipelines between systems" → Kafka

One-liner: Kafka is for when data is a continuous stream of events you want to keep, replay, and process. RabbitMQ/SQS are for when you just want to send a message from A to B and be done with it.

### 2. Event-driven architecture (events vs commands)

#### Event-Driven Architecture (EDA)

A design pattern where services communicate by producing and reacting to events instead of calling each other directly. Things happen in response to something that already happened, not because someone told them to.

#### Events vs Commands — the core difference

| | Event | Command |
|---|---|---|
| What it says | "This happened" | "Do this" |
| Direction | Broadcast to anyone interested | Sent to a specific target |
| Coupling | Loose — producer doesn't know consumers | Tight — sender knows the receiver |
| Naming | Past tense: OrderPlaced, PaymentCompleted | Imperative: ProcessPayment, SendEmail |
| Ownership | Producer owns it | Consumer owns it |
| Failure | Producer doesn't care if no one listens | Sender expects it to be executed |

Command example:

```
Order Service → "ProcessPayment" → Payment Service
(I'm telling you what to do)
```

- Order Service knows Payment Service exists
- If Payment Service is renamed or split, Order Service must change

Event example:

```
Order Service → "OrderPlaced" → Kafka/Event Bus
  ← Payment Service reacts
  ← Email Service reacts
  ← Inventory Service reacts
```

### 3. Pub/Sub pattern (fan-out, decoupling producers from consumers)

#### Pub/Sub (Publish/Subscribe) Pattern

A messaging pattern where producers (publishers) send messages to a topic/channel without knowing who will receive them. Consumers (subscribers) listen to topics they care about without knowing who sent the message. Neither side knows the other exists.

#### How it works

```
Publisher → Topic/Channel → Subscriber A
                          → Subscriber B
                          → Subscriber C
```

- Publisher doesn't know A, B, C exist
- A, B, C don't know who published
- The topic is the middleman

Real example — user signs up:

```
User Service → publishes "UserRegistered" → Topic
  ← Email Service (sends welcome email)
  ← Analytics Service (tracks signup)
  ← Billing Service (creates free plan)
  ← Notification Service (sends Slack alert)
```

User Service wrote zero code for any of those reactions.

#### Fan-out

- One message → multiple consumers. That's fan-out.
- Pub/Sub enables fan-out naturally — every subscriber gets a copy of the message
- Add a new subscriber tomorrow? No change to the publisher.

#### Decoupling

- Producer doesn't know consumers → add/remove consumers without touching producer code
- Consumer doesn't know producer → swap out who publishes without touching consumer code
- They only agree on the message format (contract)

Coupled (direct call):

```
Order Service → knows about → Payment Service
Order Service → knows about → Email Service
Order Service → knows about → Inventory Service
(Order Service changes every time you add a consumer)
```

Decoupled (Pub/Sub):

```
Order Service → publishes to topic → doesn't care who listens
(add 10 more consumers, Order Service never changes)
```

#### Pub/Sub implementations

| Tool | How it does Pub/Sub |
|---|---|
| Kafka | Topics + consumer groups |
| SNS | Topics + subscriptions (SQS, Lambda, HTTP) |
| RabbitMQ | Fanout exchange → multiple queues |
| Google Pub/Sub | Fully managed topics + subscriptions |
| Redis Pub/Sub | Lightweight, in-memory, no persistence |

#### Pub/Sub vs Point-to-Point

| Aspect | Pub/Sub | Point-to-Point (Queue) |
|---|---|---|
| Consumers | Multiple (all get the message) | One (only one consumer processes it) |
| Pattern | Fan-out | Load balancing / task distribution |
| Example | "OrderPlaced" → everyone reacts | "ProcessPayment" → one worker picks it up |
| Tools | Kafka topics, SNS, fanout exchange | SQS, RabbitMQ queue |

**When to use Pub/Sub**

- Multiple services need to react to the same event
- You want to add new consumers without changing existing code
- Loose coupling between teams/services is a priority

**When not to use**

- Only one consumer needs the message → simple queue is enough
- You need guaranteed ordering across all consumers → harder with Pub/Sub
- Request-reply pattern → Pub/Sub is fire-and-forget, not designed for responses

### 4. Kafka Deep Dive:

#### Topics

- A named channel/category where messages are published
- Like a table in a database — you create one per type of data
- Examples: order-events, payment-events, user-signups
- Messages in a topic are ordered, immutable, and retained for a configurable period (default 7 days)

#### Partitions

- A topic is split into partitions — the unit of parallelism in Kafka
- Each partition is an ordered, append-only log
- Messages within a partition have a sequential ID called an offset

```
Topic: order-events (3 partitions)
  Partition 0: [msg0, msg1, msg2, msg3, ...]
  Partition 1: [msg0, msg1, msg2, ...]
  Partition 2: [msg0, msg1, msg2, msg3, msg4, ...]
```

- How Kafka decides which partition: by message key
  - Same key → always same partition (e.g., orderId=123 always goes to partition 1)
  - No key → round-robin across partitions
- More partitions = more parallelism = more throughput

#### Consumer Groups

- A group of consumers that work together to read a topic
- Each partition is assigned to exactly one consumer in the group
- This is how Kafka does load balancing

```
Topic: order-events (3 partitions)
Consumer Group: payment-service (3 instances)
  Partition 0 → Consumer A
  Partition 1 → Consumer B
  Partition 2 → Consumer C
```

- If Consumer B dies, its partition is reassigned to A or C (rebalancing)
- Different consumer groups read independently — each group gets all messages (fan-out)

```
Consumer Group: payment-service  → reads all partitions
Consumer Group: email-service    → reads all partitions (independently)
Consumer Group: analytics        → reads all partitions (independently)
```

#### Offsets

- A sequential number assigned to each message within a partition
- Tracks where a consumer is in the partition — like a bookmark
- Consumers commit offsets to tell Kafka "I've processed up to here"

```
Partition 0: [0, 1, 2, 3, 4, 5, 6, 7]
                          ↑
              consumer's committed offset = 4
              (next read starts from 5)
```

- If consumer crashes and restarts, it resumes from the last committed offset
- This is how Kafka enables replay — reset the offset to 0 and reprocess everything

#### Ordering Guarantees (per partition only)

- Messages within a single partition are strictly ordered
- Across partitions, there is NO ordering guarantee
- So if order matters, use the same key to ensure related messages go to the same partition

```
orderId=123 → always Partition 1 → messages for order 123 are ordered ✓
orderId=456 → always Partition 2 → messages for order 456 are ordered ✓
Order of 123 vs 456 relative to each other? No guarantee ✗
```

Trade-off: fewer partitions = stronger ordering, less throughput. More partitions = more throughput, ordering only per partition.

#### Delivery Semantics

| Semantic | Meaning | How | Risk |
|---|---|---|---|
| At-most-once | Message delivered 0 or 1 time | Commit offset before processing | Message lost if consumer crashes after commit but before processing |
| At-least-once | Message delivered 1 or more times | Commit offset after processing | Duplicate processing if consumer crashes after processing but before commit |
| Exactly-once | Message delivered exactly 1 time | Kafka transactions + idempotent producer | No loss, no duplicates. Most complex, slight performance cost |

```
At-most-once:  commit offset → process (might lose)
At-least-once: process → commit offset (might duplicate)
Exactly-once:  process + commit in a transaction (safe but complex)
```

- Most systems use at-least-once + make consumers idempotent (processing the same message twice has no side effect)
- Exactly-once is available in Kafka (since 0.11) using idempotent producers + transactional APIs, but adds overhead

#### Compacted Topics

- Normal topic: messages expire after retention period (e.g., 7 days)
- Compacted topic: Kafka keeps only the latest value per key, deletes older entries
- Like a key-value store that remembers the latest state

```
Normal topic (time-based retention):
  key=user1, name=Alice
  key=user1, name=Alice Smith    ← both kept until retention expires

Compacted topic:
  key=user1, name=Alice          ← deleted (older)
  key=user1, name=Alice Smith    ← kept (latest)
```

Use cases:

- Event sourcing — rebuild current state from latest events
- CDC — latest state of each database row
- Config/state distribution — latest config per service

Think of it as a changelog where only the latest entry per key survives

#### Schema Registry (Avro / Protobuf)

Problem: producer changes the message format, consumer breaks

- Schema Registry stores and enforces message schemas (the contract)
- Producer registers a schema, consumer reads using that schema
- Supports Avro (most common with Kafka), Protobuf, JSON Schema

Without Schema Registry:

```
Producer sends: { "orderId": 123, "total": 50.0 }
Producer changes to: { "id": 123, "amount": 50.0 }
Consumer breaks 💥
```

With Schema Registry:

```
Schema v1: { orderId: int, total: double }
Producer tries to change incompatibly → Registry rejects it ✗
Producer adds optional field → Registry allows (backward compatible) ✓
```

Compatibility modes:

- Backward — new schema can read old data (add optional fields)
- Forward — old schema can read new data (remove optional fields)
- Full — both directions

Avro is popular with Kafka because it's compact (binary), has schema evolution, and integrates natively with Schema Registry

#### How it all fits together

```
Producer → Schema Registry (validate schema)
         → Topic (3 partitions, compacted or time-based)
             Partition 0: [offset 0, 1, 2, ...]
             Partition 1: [offset 0, 1, 2, ...]
             Partition 2: [offset 0, 1, 2, ...]
                 ↓
Consumer Group A (payment-service)
  Consumer 1 ← Partition 0 (offset tracked)
  Consumer 2 ← Partition 1
  Consumer 3 ← Partition 2

Consumer Group B (email-service)
  Consumer 1 ← all partitions
```

### 5. When to Use Sync vs Async

#### Synchronous — caller waits for a response

**Use when:**

- User is waiting on the screen for a result
- You need the response to continue (can't proceed without it)
- Data must be consistent immediately

**Examples:**

- User clicks "Place Order" → needs confirmation right now
- GET /products/123 → frontend needs data to render the page
- Login/authentication → must validate before proceeding
- Payment validation → need to know if card is valid before confirming order

```
User → "Show my profile" → API → User Service → DB → response → render page
(user is staring at a loading spinner, needs it fast)
```

**Trade-offs:**

- Simple to reason about (call → wait → response)
- Tight coupling — caller depends on the callee being up and fast
- Latency adds up in chains (A → B → C = sum of all latencies)
- Must have timeouts, retries, circuit breakers

#### Asynchronous — caller sends and moves on

**Use when:**

- Caller doesn't need an immediate response
- The work can happen later (eventual consistency is OK)
- You want to decouple services
- The task is slow or unreliable

**Examples:**

- Send confirmation email after order → user doesn't wait for email to arrive
- Update analytics/reporting → can be seconds or minutes behind
- Process order fulfillment → warehouse picks it up when ready
- Generate PDF invoice → takes time, notify user when done
- Sync data to search index → slight delay is fine

```
User → "Place Order" → Order Service → returns "Order Accepted" immediately
  ↓ (async)
  publishes "OrderPlaced" event
    ↓
    Email Service picks up later → sends email
    Inventory Service picks up later → reserves stock
    Analytics picks up later → records metrics
```

**Trade-offs:**

- Loose coupling — services are independent
- Resilient — if consumer is down, messages wait in the queue
- Harder to debug (no immediate error, need to trace through queues)
- Eventual consistency — data might be stale for a short period

#### Decision guide

| Question | Sync | Async |
|---|---|---|
| Does the user need an immediate answer? | Yes | No |
| Can the work happen later? | No | Yes |
| Is eventual consistency OK? | No | Yes |
| Is the downstream service slow/unreliable? | Use sync + circuit breaker | Better fit |
| Is it a query (read data)? | Almost always sync | Rarely |
| Is it a side effect (email, log, analytics)? | Rarely | Almost always async |

#### Common pattern — mix both in one flow

```
User clicks "Place Order"
│
├── SYNC: validate order, check payment → return "Order Confirmed" to user
│
└── ASYNC (after response sent):
      → send confirmation email
      → update inventory
      → notify warehouse
      → update analytics
```

The user-facing part is sync (they need confirmation). Everything else is async (they don't need to wait for an email to be sent or inventory to update).

One-liner: if the user is waiting, go sync. If no one is waiting, go async.
