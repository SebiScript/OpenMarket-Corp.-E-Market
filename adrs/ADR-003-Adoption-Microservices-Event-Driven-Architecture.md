# ADR-003: Adoption of Microservices and Event-Driven Architecture

## Status

**Accepted**
## Context

Based on the requirements analysis performed (Rol 1), the OpenMarket system must satisfy critical quality attributes:

- **Elasticity (Req 3.1):** The system must handle traffic spikes during sales seasons.
- **Availability (Req 3.3):** Downtime must be minimized; the failure of one component should not crash the entire system.
- **Interoperability (Req 1.1):** The system must integrate seamlessly with external providers and legacy systems.
- **Extensibility (Req 4.4):** New features must be added without major restructuring.

A traditional **Monolithic Architecture** was considered. However, a monolith would pose a risk to Availability (single point of failure) and Elasticity (scaling requires duplicating the entire application, which is resource-intensive).

## Decision

We decided to adopt a **Microservices Architecture** combined with an **Event-Driven Architecture** approach.

**Key implementation details visualized in the C4 diagrams:**

1. **Decomposition by Capability:** The system is split into autonomous containers (Order Service, Catalog Service, Payment Service), as shown in the **C4 Level 2 Diagram**.
2. **Asynchronous Communication:** We introduce a **Message Broker (RabbitMQ)** to decouple services. Services communicate via events (e.g., "OrderCreated") rather than direct HTTP calls where possible.
3. **Resilience Patterns:** In the **C4 Level 3 Diagram**, we implement specific components like **Circuit Breakers** and **Adapters** to handle external failures gracefully.

## Consequences

**Positive:**

- **High Elasticity:** As visualized in Level 2, we can scale the "Catalog Service" container independently during high traffic without affecting the "Payment Service."
- **Improved Availability:** The **Circuit Breaker** (shown in Level 3) ensures that an external API failure (e.g., PayPal) does not block the system threads, keeping OpenMarket operational.
- **Security Isolation:** Sensitive payment logic is isolated in its own container with strict boundaries (Level 3), facilitating PCI-DSS compliance (Req 3.4).

**Negative / Trade-offs:**

- **Complexity:** The system is significantly more complex to debug and deploy than a monolith.
- **Distributed Data Management:** We lose ACID transactions across services. To mitigate this, we must implement the **Outbox Pattern** (visualized in Level 3) to ensure data consistency.