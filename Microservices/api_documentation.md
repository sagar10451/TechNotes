# 📘 API Documentation in This Project

## 🏗️ Theory: Why API Documentation?

> **The Big Picture:** Documentation acts as the "Source of Truth" between different services and teams.

### 🎯 Purpose

* **Contract Definition:** Defines what endpoints exist, what they accept, and what they return.
* **Developer Experience:** Helps frontend developers, testers, and other teams understand how to use your APIs without reading code.
* **Testing Interface:** Provides interactive UI to test APIs directly from the browser.
* **Standardization:** Uses **OpenAPI** (formerly Swagger) specification—the industry standard for REST APIs.

### 🚀 Benefits

* **Reduced Communication Overhead:** No need to explain APIs verbally or via emails.
* **Faster Integration:** Other teams can integrate without waiting for manual documentation.
* **Interactive Testing:** Test APIs without needing external tools like Postman or curl.
* **Auto-Generated:** Documentation updates automatically whenever your code changes.
* **Microservices Essential:** In microservices, each service needs clear API contracts.

---

## 🛠️ How It's Implemented in This Project

### 1. Dependencies (`pom.xml`)

We use the **SpringDoc OpenAPI** starter to automate the documentation process.

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.5.0</version>
</dependency>
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-api</artifactId>
    <version>2.5.0</version>
</dependency>

```

### 2. Configuration Files

Each service has an `OpenAPIConfig.java` file. While the class name can be anything, the requirements are:

1. **`@Configuration`** annotation.
2. **`@Bean`** method returning an `OpenAPI` object.

**Example from Order Service:**

```java
@Configuration
public class OpenAPIConfig {
    @Bean
    public OpenAPI orderServiceAPI() {
        return new OpenAPI()
                .info(new Info().title("Order Service API")
                        .description("This is the REST API for Order Service")
                        .version("v0.0.1")
                        .license(new License().name("Apache 2.0")))
                .externalDocs(new ExternalDocumentation()
                        .description("You can refer to the Order Service Wiki Documentation")
                        .url("https://order-service-dummy-url.com/docs"));
    }
}

```

### 3. Controllers

Your controllers (like `OrderController.java`) are automatically documented by scanning:

* `@RestController` / `@Controller` — Identifies endpoints.
* `@RequestMapping` — Base path.
* `@PostMapping` / `@GetMapping` — HTTP methods.
* `@RequestBody` — Payload structure.
* `@ResponseStatus` — Response codes.

#### 🙈 How to Exclude an API

If you need to hide sensitive endpoints from the documentation:

| Scope | Method |
| --- | --- |
| **Entire Controller** | Add `@Hidden` above the class. |
| **Specific Method** | Add `@Hidden` above the method. |
| **Using Operation** | Add `@Operation(hidden = true)` above the method. |

```java
@GetMapping("/secret")
@Hidden  // Hides just this method from Swagger
public String secretEndpoint() { ... }

```

---

## 🔍 OpenAPI vs. Swagger UI

It's important to understand the difference between the specification and the tool:

* **OpenAPI:** The **specification** (the "What"). It defines the raw data structure.
* **Swagger UI:** The **visual viewer** (the "How"). It renders that data into an interactive page.

| Dependency | Purpose |
| --- | --- |
| `springdoc-openapi-starter-webmvc-api` | Generates raw OpenAPI JSON/YAML data. |
| `springdoc-openapi-starter-webmvc-ui` | Provides the interactive web interface (Swagger UI). |

---

## 🚦 How to Access & Use

For the **Order Service**, use the following local URLs:

* **✨ Swagger UI:** `http://localhost:<port>/swagger-ui.html`
* **📄 OpenAPI JSON:** `http://localhost:<port>/v3/api-docs`

### What You'll See:

1. **Interactive UI:** Every endpoint is listed with its requirements.
2. **"Try it out" Button:** Test live API calls directly.
3. **Request/Response Schemas:** View detailed DTO structures.
4. **Response Codes:** Clear mapping of `200`, `201`, `400`, etc.
