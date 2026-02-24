# ADR-002 — Architecture Decision Record

*Following the Michael Nygard ADR model adopted by Section E*

---

## ADR Information

| Field | Value |
|---|---|
| *ADR ID* | ADR-002 |
| *Title* | Selection of Architectural Style for OpenMarket Corp E-Market |
| *Date* | 2025 |
| *Status* | Accepted |
| *Deciders* | Alejandro Gonzales Jesus (Section E - Capstone Team) |

---

## Context

OpenMarket Corp requires a scalable, highly available, and secure e-commerce platform capable of handling significant traffic spikes (e.g., Black Friday), supporting multiple third-party integrations such as payment gateways, shipping carriers, and suppliers, and evolving continuously with new features.

The architectural style chosen at this stage will determine the system's capacity to meet these long-term goals.

---

## Decision

We adopt a *Microservices Architecture* as the primary structural style, complemented by *Event-Driven communication* for asynchronous workflows.

Each business domain (e.g., Catalog, Orders, Payments, Inventory, Notifications) will be encapsulated in an independent, deployable service with its own data store following the *Database-per-Service pattern*.

---

## Consequences

### Positive Outcomes

- Services can be scaled independently, directly supporting the *Elasticity requirement (Req 3.1)*.
- A failure in one service (e.g., Notifications) does not cascade to the core checkout flow, supporting *Availability (Req 3.3)*.
- Sensitive domains such as Payments and Authentication can be isolated and audited independently, supporting *Security / PCI-DSS compliance (Req 3.4)*.
- Teams can deploy services independently, enabling rapid feature iteration and continuous delivery (*Deployability*).

---

## Trade-offs Accepted

| Trade-off Accepted | Rationale |
|---|---|
| Increased operational complexity (DevOps overhead) | Justified by the need for independent scaling and fault isolation to meet Elasticity and Availability requirements. |
| Distributed tracing and debugging challenges | Mitigated with observability tooling (e.g., Jaeger, ELK Stack). Accepted as a necessary cost of resilience. |
| Higher infrastructure cost compared to a monolith | Offset by cloud auto-scaling (pay-per-use), reducing cost during low-traffic periods. |

---

## Alternatives Rejected

### Monolithic Architecture
Rejected due to poor alignment with Elasticity and Availability. A single deployment unit creates a single point of failure and limits independent scaling.

### Pure Event-Driven Architecture
Rejected as the sole approach due to increased debugging complexity and eventual consistency challenges that conflict with the *Data Consistency requirements (Req 1.5, 2.5)*. Retained only as a complementary pattern for asynchronous workflows.

### Microkernel Architecture
Explicitly excluded per team constraints — not suitable for scalable and elastic systems of this scope.

---