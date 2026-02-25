# ADR-004: Use of an API Gateway (Kong)

## Status

**Accepted**

## Context

The system has multiple client applications: a Web App (React), a Mobile App (Flutter), and an Admin Panel (React). Allowing these clients to communicate directly with each microservice exposes the internal topology of the system, complicates the management of **Security (Req 3.4)**, and forces clients to make multiple round-trip calls to render a single view, negatively affecting **Responsiveness (Req 2.1)**.

## Decision

We will implement **Kong** as a centralized **API Gateway**. It will act as the single entry point (an "inverted proxy") for all external requests. The Gateway will receive HTTPS traffic and route it internally to the corresponding microservices via REST/JSON.

Additionally, the Gateway will serve as the first line of defense for security, offloading cross-cutting concerns from the backend services.

## Consequences

**Positive (+):**

- **Centralized Security:** It validates **JWT tokens** by communicating with the Identity Provider (Keycloak) at the edge, ensuring that only authorized traffic reaches the internal microservices.
- **Simplified Routing:** Provides a unified interface for clients, hiding the complexity of the microservices infrastructure.
- **Resilience:** Allows implementation of Rate Limiting and Load Balancing transparently.

**Negative (–):**

- Introduces an additional network hop, adding minimal latency to every request.
- It becomes a critical point of failure. Therefore, the Gateway must be deployed in a High Availability (HA) cluster configuration to not compromise the global **Availability** of the system.