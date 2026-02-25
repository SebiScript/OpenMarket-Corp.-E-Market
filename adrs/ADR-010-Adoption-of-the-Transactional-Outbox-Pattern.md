# ADR-010: Adoption of the Transactional Outbox Pattern
# Status
Proposed

# Context
The OpenMarket platform requires strong Data Consistency guarantees when processing payments and orders (Req. 2.5). After a payment is successfully processed, the system must update the local Payment database (PostgreSQL) and publish a corresponding domain event to the message broker (RabbitMQ).

Executing these two operations separately introduces a risk of inconsistency if the system crashes between steps. This could result in missing events, inconsistent inventory states, or failed downstream processes. Distributed transactions (2PC) are intentionally avoided due to their complexity and negative impact on system performance.

# Decision
We decided to adopt the Transactional Outbox Pattern.

Instead of publishing events directly to RabbitMQ within the business logic, the Payment Orchestrator will persist the domain event into a dedicated Outbox table as part of the same local database transaction that records the payment. A separate Event Outbox Component will asynchronously poll this table and reliably publish the pending events to the message broker.

# Consequences
* **What becomes easier:**
    * Guarantees atomicity between database updates and event publication.
    * Enables eventual consistency without relying on distributed transactions.
    * Ensures at-least-once delivery of messages to the message broker.
* **What becomes more difficult:**
    * Introduces additional complexity in the database schema and background processing logic.
    * Adds a small delay in message delivery due to the polling mechanism.
    * Requires idempotent consumers to safely handle potential duplicate events.