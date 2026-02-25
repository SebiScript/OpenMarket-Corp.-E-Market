# ADR-009: Implementation of Circuit Breaker Pattern
# Status
Proposed

# Context
The Payment Service of the OpenMarket platform relies on external third-party payment providers, specifically Stripe and PayPal APIs. These services are outside of our control and may experience high latency or complete outages.

In a microservices architecture, slow or failing external dependencies can lead to thread exhaustion within the Payment Service, potentially causing cascading failures that impact overall system Availability and violate availability requirements (Req. 3.3). A mechanism is required to isolate these failures and prevent them from propagating through the system.

# Decision
We decided to implement the Circuit Breaker Pattern within the Payment Service using the Resilience4j library.

The circuit breaker will continuously monitor the success and failure rates of outgoing calls to the Stripe and PayPal adapters. When the failure rate exceeds a predefined threshold, the circuit will transition to an open state, causing subsequent calls to fail fast without attempting to reach the external APIs. After a configured wait period, the circuit will partially close to test whether the external service has recovered.

# Consequences
* **What becomes easier:**
    * Improved system resilience by preventing cascading failures caused by unstable third-party services.
    * Maintaining high Availability by avoiding thread blocking during external outages.
    * Better user experience through a fail-fast mechanism that returns immediate error responses instead of long timeouts.
* **What becomes more difficult:**
    * Increased service complexity due to additional fault-tolerance components.
    * Careful configuration and tuning of thresholds (failure rate, wait duration) is required to avoid premature circuit openings or delayed recovery.