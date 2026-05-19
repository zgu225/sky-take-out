# Catering SaaS Platform (Backend Engine)

An enterprise-level, high-concurrency food delivery and catering management platform built with **Spring Boot 3.x** and **JDK 17**. This project provides a robust backend engine that serves both the Merchant Management Console (PC Web) and the Customer Client (WeChat Mini Program).

---

## Core Architecture & Modern Tech Stack

To ensure long-term maintainability, security, and exceptional performance, the project has been fully migrated to the latest modern technical baseline:

- **Core Engine:** **JDK 17** (LTS) & **Spring Boot 3.1.2** (built on Jakarta EE 10)
- **API Documentation & Exploration:** **Knife4j 4.5.0** (OpenAPI 3 / Jakarta Edition)
- **Persistence Layer:** **MyBatis 3.0.x (Spring Boot 3 Starter)**, **PageHelper 2.1.0** (Jakarta compatible), Druid Connection Pool, and **MySQL 8.0** (via `mysql-connector-j`)
- **Caching & Concurrency:** Redis (handles high-frequency menu queries and prevents inventory oversell)
- **Authentication & Security:** stateless JWT (JSON Web Tokens) combined with **ThreadLocal** for thread-safe user context tracking
- **Cloud Storage:** Alibaba Cloud OSS

---

## Modernization Highlights (Spring Boot 3 & JDK 17 Migration)

This project has been meticulously modernized from a traditional Spring Boot 2.x stack to Spring Boot 3.x, bringing several architectural enhancements:

1. **Jakarta EE Namespace Migration:** Fully migrated from old `javax.*` packages (e.g., `javax.servlet`) to the modern `jakarta.*` packages (`jakarta.servlet`), aligning with the industry-standard Jakarta EE 10 specification.
2. **Knife4j & OpenAPI 3 Specification:** Upgraded the API documentation framework from outdated Swagger 2 (Springfox) to **Knife4j OpenAPI 3**. Configured multi-group routing (`GroupedOpenApi`) for strict separation between `Management Console (Admin API)` and `Consumer App (User API)`.
3. **Advanced Java 17 Features:** Configured compiler target to Java 17, paving the way for adopting modern language features such as Text Blocks, Records, and enhanced Pattern Matching.
4. **Precision Double/Long Mapping:** Retained a custom `JacksonObjectMapper` mapping engine, automatically converting large `Long` integers (like Snowflake IDs) to strings during JSON serialization to prevent precision loss on the frontend, while maintaining standard DateTime formats.

---

## Key Business & Technical Capabilities

### 1. Robust Security & Authentication
- Stateless API authentication using **JWT**.
- Custom **HandlerInterceptors** to perform seamless token validation.
- Thread-safe user context sharing across the entire request lifecycle using **ThreadLocal** (`BaseContext`).

### 2. AOP-Based Audit Log Auto-Fill
- Introduced **Aspect-Oriented Programming (AOP)** to completely eliminate repetitive database audit boilerplate code (e.g., `create_time`, `update_user`).
- Custom `@AutoFill` annotations intercept MyBatis Mapper calls to dynamically inject timestamps and operator IDs at runtime using Reflection.

### 3. High-Concurrency Optimization
- **Menu Cache:** Integrates Spring Cache and Redis to cache active menus, cutting down database pressure by up to 90% during rush-hour traffic.
- **Oversell Prevention:** Optimized checkout mechanisms to ensure inventory consistency.

### 4. Real-Time Event & Scheduled Tasks
- **WebSocket:** Implemented full-duplex WebSocket communication to deliver real-time push alerts (new orders/order urges) directly to the merchant dashboard.
- **Scheduled Tasks (Spring Task):** Configured automated background CRON tasks (e.g., auto-canceling unpaid orders after 15 minutes, auto-closing stale orders at midnight).

---

## Project Directory Structure

The repository is organized into a highly optimized, flat Maven multi-module architecture:

- `sky-common`: Contains generic utility classes, core constants, global exception handlers, unified response wrappers (`Result`), and Jackson serialization engines.
- `sky-pojo`: Declares all database domain structures including Entities, Data Transfer Objects (DTOs), and View Objects (VOs).
- `sky-server`: The core business logic engine, containing Controllers, Services, MyBatis Mappers, Interceptors, and configuration classes.

---

## Getting Started

### Prerequisites
- **JDK 17** installed and configured in your environment.
- **Maven 3.8+**
- **MySQL 8.0+**
- **Redis 6.x+**

### Quick Launch Steps
1. **Initialize Database:** Run the `sky_take_out_init.sql` script on your MySQL instance to create the necessary tables and populate initial mock data.
2. **Start Redis:** Make sure your Redis server is running locally on the default port `6379`.
3. **Configure Environment:** Update the `application-dev.yml` file in the `sky-server` module with your own MySQL credentials, Redis password, and Alibaba Cloud OSS credentials.
4. **Build the Project:** Run the following command in the root folder to compile and build the modules:
   ```bash
   mvn clean install
   ```
5. **Run Application:** Execute the `com.sky.SkyApplication` main class in the `sky-server` module.
6. **API Documentation:** Once the server starts, open your browser and navigate to:
   ```
   http://localhost:8080/doc.html
   ```
   Enjoy the modern Knife4j OpenAPI 3 document interface!
