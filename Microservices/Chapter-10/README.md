# Containerization

Containerization — package your service + its dependencies into a container. Runs the same everywhere (laptop, CI, prod).

---

## 1. Docker: Multi-Stage Builds, Layered JARs, Minimal Base Images

### Multi-stage build

Multi-stage build — use one stage to build, another to run. Build tools don't end up in the final image.

```dockerfile
# Stage 1: Build
FROM eclipse-temurin:21-jdk AS build
WORKDIR /app
COPY . .
RUN ./mvnw package -DskipTests

# Stage 2: Run (only JRE, no JDK, no Maven, no source code)
FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Build stage has JDK + Maven (~600MB). Final image has only JRE + JAR (~200MB).

### Layered JARs (Spring Boot)

Layered JARs (Spring Boot) — Docker caches layers. If only your code changes, dependencies layer is cached (not re-downloaded).

```dockerfile
FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=build /app/target/extracted/dependencies/ ./
COPY --from=build /app/target/extracted/spring-boot-loader/ ./
COPY --from=build /app/target/extracted/snapshot-dependencies/ ./
COPY --from=build /app/target/extracted/application/ ./
ENTRYPOINT ["java", "-jar", "app.jar"]
```

```
Layer 1: dependencies (50MB) — cached, rarely changes
Layer 2: spring-boot-loader (1MB) — cached
Layer 3: snapshot-deps (5MB) — occasionally changes
Layer 4: application code (2MB) — changes every build
```

Only layer 4 rebuilds on code change → fast builds.

### Minimal base images

| Image | Size | What's in it |
|-------|------|-------------|
| `eclipse-temurin:21-jdk` | ~400MB | Full JDK + tools (build stage only) |
| `eclipse-temurin:21-jre` | ~200MB | JRE only (good default for runtime) |
| `eclipse-temurin:21-jre-alpine` | ~100MB | JRE on Alpine Linux (smaller) |
| `gcr.io/distroless/java21` | ~80MB | No shell, no OS tools — just Java runtime |

Distroless = no bash, no apt, no curl — nothing an attacker can use. Most secure, smallest. Downside: can't shell into the container to debug.

---

## 2. Image Size Optimization

```
✗ Bad:    FROM eclipse-temurin:21-jdk → 400MB+ (ships JDK, compiler, build tools)
✓ Good:   Multi-stage + JRE base → ~200MB
✓ Better: Alpine JRE → ~100MB
✓ Best:   Distroless → ~80MB
```

Other tips:

- `.dockerignore` — exclude `.git`, `target`, `node_modules`, IDE files
- Don't `COPY . .` in the run stage — only copy what's needed
- Pin image versions (`eclipse-temurin:21.0.2_13-jre` not `latest`)
- Run as non-root user:

```dockerfile
RUN addgroup --system app && adduser --system --ingroup app app
USER app
```

---

## 3. Docker Compose for Local Development

Run all services + infrastructure locally with one command.

```yaml
version: '3.8'
services:
  order-service:
    build: ./order-service
    ports:
      - "8080:8080"
    environment:
      - DB_HOST=order-db
      - KAFKA_BOOTSTRAP=kafka:9092
    depends_on:
      - order-db
      - kafka

  payment-service:
    build: ./payment-service
    ports:
      - "8081:8080"
    environment:
      - DB_HOST=payment-db

  order-db:
    image: postgres:16
    environment:
      POSTGRES_DB: orders
      POSTGRES_PASSWORD: localdev
    ports:
      - "5432:5432"

  payment-db:
    image: postgres:16
    environment:
      POSTGRES_DB: payments
      POSTGRES_PASSWORD: localdev

  kafka:
    image: confluentinc/cp-kafka:7.5.0
    ports:
      - "9092:9092"

  zipkin:
    image: openzipkin/zipkin
    ports:
      - "9411:9411"
```

```bash
docker compose up      # start everything
docker compose down    # stop and clean up
docker compose up -d   # detached (background)
docker compose logs -f order-service  # tail logs
```

Entire microservice ecosystem on your laptop — services, databases, Kafka, tracing. Same setup for every developer on the team.
