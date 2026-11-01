# CommerceFlow Platform

A production-oriented distributed e-commerce platform built with **Java 21, Spring Boot, RabbitMQ, PostgreSQL and Docker**, demonstrating **Saga orchestration, transactional outbox** and resilient microservices.

Built to show how real-world distributed transactions, eventual consistency and failure handling work at scale — not just CRUD services.

## Key Highlights

- **Saga Orchestration** for distributed order flow (Order → Payment → Inventory)
- **Transactional Outbox Pattern** for reliable event publishing, no lost messages
- **Event-driven microservices** with RabbitMQ (topic/direct exchanges + DLQ)
- **Resilience:** retries with exponential backoff, idempotency keys, dead-letter queues
- **Per-service PostgreSQL** — no shared database
- **One-command run:** `docker compose up --build`
- **Observability-ready:** centralized logging, health checks, OpenAPI/Swagger

## Architecture

```mermaid
graph TD
    Client --> API[api-gateway / order-service API]
    API --> ORD[order-service<br/>Saga Orchestrator + Outbox]
    ORD --> DB1[(PostgreSQL - orders)]
    ORD -->|order.created| RMQ[RabbitMQ]
    RMQ --> PAY[payment-service]
    RMQ --> INV[inventory-service]
    PAY --> DB2[(PostgreSQL - payments)]
    INV --> DB3[(PostgreSQL - inventory)]
    PAY -->|payment.approved / rejected| RMQ
    INV -->|stock.reserved / failed| RMQ
    RMQ --> ORD
    ORD -->|order.confirmed / cancelled| RMQ
