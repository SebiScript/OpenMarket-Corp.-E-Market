# ADR-003: Database per Service Pattern

## Status

**Accepted**

## Context

In a microservices architecture, using a shared database creates strong coupling between services and introduces a single point of failure, compromising **Availability (Req 3.3)** and **Elasticity (Req 3.1)**. For the OpenMarket system, the Order Service, Catalog Service, and Payment Service must operate with maximum autonomy to support massive traffic spikes without the degradation of one service affecting the data persistence of the others.

Furthermore, different business domains have different data storage needs. The Catalog requires flexibility for product attributes, while Payments require strict transactional integrity.

## Decision

We will implement the **Database per Service** pattern. Each microservice will have exclusive ownership of its data schema:

- **Order Service:** Will use **PostgreSQL** to ensure ACID transactions for orders and critical business data.
- **Catalog Service:** Will use **MongoDB** to handle the flexibility of the product catalog (Polyglot Persistence).
- **Payment Service:** Will use a separate instance of **PostgreSQL** to comply with data isolation standards for financial information (PCI-DSS).

Communication between these data stores is strictly prohibited at the database level. Data exchange must occur only through service APIs or via asynchronous events (as visualized in the Level 2 Diagram).

## Consequences

**Positive (+):**

- Complete fault isolation; if the Catalog database fails, the Payment Service can continue processing transactions.
- Allows for independent scaling of each database (Elasticity).
- Enables "Polyglot Persistence," using the best database technology for each specific domain (SQL vs. NoSQL).

**Negative (–):**

- Increased operational complexity.
- Cross-service queries (SQL JOINs) are no longer possible at the database level. They must be resolved via application-level aggregation or the **Outbox Pattern** (as visualized in the Level 3 Diagram) to ensure data consistency.