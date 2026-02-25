# Title
ADR-005: Implementation of Asynchronous Event Bus with RabbitMQ (AMQP)

# Status
Accepted

# Context
The OpenMarket Corp. system must handle critical post-purchase processes, such as the exact inventory update of multiple suppliers (Req. 1.5, Req. 2.5) and sending notifications to customers and sellers (Req. 2.6). If the Order Service communicates synchronously (e.g., via HTTP/REST) with the catalog and notification services, any sluggishness in those systems will block the main execution thread. This would severely degrade Responsiveness and jeopardize Availability during massive traffic spikes (like Black Friday). 

Furthermore, we needed to evaluate the right messaging infrastructure for this decoupling. We require a system capable of complex, targeted routing for distinct business transactions (e.g., specific emails, individual inventory updates) rather than a system built for ingesting continuous, massive data streams.

# Decision
We decided to implement a complementary Event-Driven architecture style using **RabbitMQ** as our central Message Broker, operating under the AMQP protocol. 

**We explicitly chose RabbitMQ over Apache Kafka for the following reasons:**
RabbitMQ excels at complex message routing (using exchanges and queues) and ensuring the reliable delivery of distinct, individual business events (like a single `OrderCreated` or `PaymentFailed` message). Kafka, while highly scalable, is an append-only distributed log optimized for continuous, massive data streaming (Big Data) and event sourcing. Using Kafka would introduce unnecessary operational complexity and infrastructure overhead for our specific use case, which primarily revolves around task queues, targeted notifications, and worker processes.

# Consequences
* **What becomes easier:**
  * Complex Routing: It is trivial to route specific messages to different queues based on routing keys (e.g., prioritizing VIP customer orders) thanks to AMQP exchanges.
  * Scaling services independently (Elasticity). If there is a spike in emails to send, only the notification service consumers scale.
  * Maintaining high UI Availability: the customer receives an immediate "Order in process" response without having to wait for the email to actually be sent.
  * Extreme decoupling: new services can be added in the future (Extensibility) that listen to the same events without modifying the original code.
* **What becomes more difficult:**
  * Message replayability: Unlike Kafka, which stores events durably in a log for extended periods, RabbitMQ typically removes messages once successfully consumed. This makes it harder to "replay" historical events if a service crashes (requiring us to configure Dead Letter Queues for failed messages).
  * Traceability and debugging: Tracking an error across multiple asynchronous services is complex and will require implementing Correlation IDs.
  * Data Consistency management: It forces us to abandon traditional transactional consistency (ACID) in favor of Eventual Consistency, requiring additional patterns (like the Outbox Pattern) to compensate for failures.