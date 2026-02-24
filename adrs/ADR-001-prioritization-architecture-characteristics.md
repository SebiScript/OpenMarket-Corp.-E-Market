# Title
ADR-001: Prioritization of Architecture Characteristics for OpenMarket Corp.'s Multi-platform E-Market

# Status
Accepted

# Context
OpenMarket Corp. requires an e-commerce system that manages the sale of products from multiple external suppliers. Analyzing the business requirements, the system faces three main challenges critical to its survival:

1. **Massive traffic spikes:** During promotional campaigns or peak seasons, the system load will vary drastically (Req. 3.1).
2. **Critical cost of downtime:** System outages during high-demand periods result in direct financial losses and reputational damage (Req. 3.3).
3. **Financial transaction handling:** The system processes payments and stores sensitive data, which requires strict regulatory compliance (Req. 3.4 and 2.4).

Although requirements such as Interoperability (to connect APIs/CSVs) and Extensibility are vital for the ecosystem, architectural theory states that attempting to optimize all characteristics to the maximum generates unmanageable systems. It is necessary to prioritize the Driving Characteristics.

# Decision
We decided to establish the following three characteristics as the **Top 3 Driving Characteristics** that will guide the selection of the architectural style:

1. **Elasticity:** Chosen to comply with Requirement 3.1. It will allow the system to dynamically allocate resources in response to sudden traffic bursts during promotions and release resources when traffic drops, optimizing infrastructure costs.
2. **Availability:** Fundamental to comply with Requirement 3.3. The design must include fault tolerance and redundancy to ensure that the E-Market continues operating its critical functions (catalog, cart) even if a third-party service fails.
3. **Security:** Mandatory per Requirement 3.4. Compliance with the PCI-DSS standard for credit card handling is a non-negotiable legal requirement that must shape the network and data storage from day zero.

# Consequences

* **What becomes easier:** * Profitably managing sales spikes during Black Friday or Cyber Monday thanks to elasticity.
    * Maintaining sales levels, since high availability will isolate failures (for example, if the marketing reporting service goes down, the user can still complete the checkout).
    * Passing security audits and avoiding legal fines by having Security as a central pillar of the design.

* **What becomes harder:** * **Infrastructure complexity:** Prioritizing Elasticity and Availability rules out the use of simple monolithic architectural styles (Layered, Microkernel), forcing us to adopt more complex distributed architectures such as Microservices.
    * **Data Consistency:** By adopting a distributed model to gain Availability, achieving the "immediate stock confirmation" demanded by suppliers (Req. 1.5) will be more technically challenging. Greater design effort must be invested in handling distributed transactions using the Saga pattern to ensure order integrity.