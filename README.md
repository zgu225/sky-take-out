# Catering SaaS Platform (Backend Engine)

An enterprise-level, high-concurrency food delivery and catering management platform built with Spring Boot, Redis, and MySQL. This project provides a robust backend engine that serves both the Merchant Management Console (PC Web) and the Customer Client (WeChat Mini Program).

## Core Architecture & Tech Stack

- **Core Framework:** Java 11, Spring Boot 2.7, Spring MVC
- **Persistence Layer:** MyBatis, Druid Connection Pool, MySQL 8.0
- **Caching & Concurrency:** Redis (handles high-frequency menu queries and prevents inventory oversell)
- **Authentication:** JWT (JSON Web Tokens) with ThreadLocal for thread-safe user context tracking
- **Cloud Storage:** Alibaba Cloud OSS
- **API Documentation:** Swagger / Knife4j

## Key Features & Technical Highlights

### 1. Robust Security & Authentication
- Implemented stateless API authentication using **JWT**.
- Built standard **HandlerInterceptors** to intercept and validate tokens.
- Leveraged **ThreadLocal** to safely isolate and pass the current user's ID across the entire request thread lifecycle.
- Utilized MD5 hashing with salt for employee password encryption.

### 2. AOP-Based Auto-Fill Mechanism
- Instead of manually writing boilerplate code for database audit fields (e.g., `create_time`, `update_user`, `update_time`), an **Aspect-Oriented Programming (AOP)** approach was introduced.
- Custom annotations (`@AutoFill`) were created to intercept MyBatis Mapper methods and automatically inject timestamps and current operator IDs using Reflection.

### 3. Complex Order State Machine
- Designed a reliable state machine to handle the full lifecycle of an order: `Pending Payment` -> `To be Accepted` -> `Delivering` -> `Completed` / `Canceled`.
- Handled edge cases such as customer refunds and merchant rejections with strict data consistency.

### 4. Concurrency Control & High Availability
- **Menu Cache:** Integrated Spring Cache and Redis to pre-load and cache the dish/combo lists, drastically reducing MySQL pressure during peak meal times (lunch/dinner rushes).
- **Oversell Prevention:** Optimized the checkout process to ensure inventory precision. 

### 5. Real-Time Communication & Scheduled Tasks
- **WebSocket:** Implemented full-duplex WebSocket connections. When a customer pays for an order or clicks "Urge Order", the backend instantly pushes a message to the merchant's browser, triggering a voice broadcast.
- **Spring Task:** Configured CRON jobs to run silently in the background. It automatically cancels unpaid orders after 15 minutes and acts as a safety net to close "delivering" orders that linger past midnight.

## Project Structure

This project follows a standard multi-module Maven architecture:

- `sky-common`: Contains generic utilities, constants, custom exceptions, unified response wrappers (`Result`), and JSON configuration.
- `sky-pojo`: Contains all Data Objects (`Entity`), Data Transfer Objects (`DTO`), and View Objects (`VO`).
- `sky-server`: The core business module containing Controllers, Services, Mappers, Interceptors, and configuration classes.

## Getting Started

1. Clone the repository.
2. Setup MySQL (8.0+) and execute the `sky_take_out_init.sql` script to initialize the database and tables.
3. Setup Redis and ensure it is running on the default port (6379).
4. Update the `application-dev.yml` file with your own `datasource` credentials (username/password) and AliCloud OSS keys.
5. Run the `SkyApplication` main class.
6. Access the API documentation at `http://localhost:8080/doc.html`.
