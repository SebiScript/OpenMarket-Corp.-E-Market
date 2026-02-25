# Title
ADR-006: Identity Delegation and Centralized Authentication with Keycloak (OIDC)

# Status
Accepted

# Context
The E-Market platform handles financial data, customers' personal information, and suppliers' inventories. Requirement 3.4 explicitly demands compliance with strict security regulations like PCI-DSS. Furthermore, the system must support complex role management including customers, suppliers, operators, and marketing managers (Req. 1.2). Developing a custom authentication (Login) and authorization module from scratch introduces critical vulnerability risks, consumes too much development time, and complicates security audits.

# Decision
We decided not to build our own custom user management system. Instead, we will delegate authentication and authorization to a dedicated Identity Provider, using **Keycloak**. 
Keycloak will centralize security by issuing JWTs (JSON Web Tokens) through the standard OIDC (OpenID Connect) protocol. Every request passing through our API Gateway will validate this token against Keycloak or an internal Auth Guard before reaching the microservices.

# Consequences
* **What becomes easier:**
  * Complying comfortably with the Security characteristic and passing technical compliance audits.
  * Centrally managing passwords, expiration policies, multi-factor authentication (MFA), and the multiple roles demanded by the business.
  * Integrating new applications in the future (like a native mobile app for delivery drivers) using standard protocols without rewriting the login logic (Extensibility).
* **What becomes more difficult:**
  * Infrastructure maintenance: The operations team (DevOps) takes on the burden of deploying, configuring, updating, and maintaining the Keycloak cluster in high availability.
  * Single Point of Failure: If Keycloak goes down and lacks redundancy, no one will be able to log in to OpenMarket (although active sessions with valid JWTs could continue operating until they expire).