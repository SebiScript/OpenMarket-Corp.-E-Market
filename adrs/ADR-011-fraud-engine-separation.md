# ADR-011 — Fraud Detection Engine Separation

**Architecture Decision Record**  
Following the Michael Nygard ADR model adopted by Section E

---

## ADR Information

| Field | Value |
|---|---|
| **ADR ID** | ADR-011 |
| **Title** | Separation of Fraud Detection Engine from Payment Flow |
| **Date** | 2025 |
| **Status** | Accepted |
| **Deciders** | Section E - Capstone Team |

---

## Context

The OpenMarket Corp E-Market platform processes financial transactions through the **Payment Service**, which is a critical component responsible for charging customer payment methods.

Fraud validation rules evolve frequently due to new attack patterns, regulatory changes, and business policies. Embedding fraud validation logic directly into the payment processing flow would tightly couple security rules with transaction execution.

Such coupling would create several risks:

- Frequent modifications to payment code.
- Increased risk of introducing failures in financial transactions.
- Reduced maintainability and extensibility.
- Difficulty testing fraud logic independently.

Given the system's **Security**, **Maintainability**, and **Extensibility** requirements, a more flexible solution is required.

---

## Decision

We separate the **Fraud Detection Engine** as an independent component from the main payment processing flow.

The Payment Orchestrator communicates with the Fraud Detection Engine through an abstraction interface (`IFraudEngine`).

The fraud engine becomes:

- A replaceable module.
- Independently testable.
- Independently evolvable without modifying payment execution logic.

Fraud validation is executed **before payment authorization**, allowing risk analysis without coupling business rules to financial processing.

---

## Consequences

### Positive Outcomes

- Fraud rules can evolve without modifying payment processing code.
- Improved **Security** through specialized risk analysis.
- Increased **Maintainability** via separation of concerns.
- Independent testing of fraud detection logic.
- Enables future integration of AI/ML fraud systems without refactoring the payment service.

---

### Trade-offs Accepted

| Trade-off Accepted | Rationale |
|---|---|
| Additional component complexity | Accepted to achieve modular security evolution. |
| Extra network or service call | Minimal latency impact compared to improved system safety. |
| Need for clear interface contracts | Necessary to maintain loose coupling between components. |

---

## Alternatives Rejected

### Embedded Fraud Logic inside Payment Service
Rejected because it tightly couples security rules with transaction processing, increasing maintenance risk and reducing flexibility.

### External Third-Party Fraud Service Only
Rejected as the sole solution because it removes internal control over fraud policies and business-specific validation rules.

---

## Result

The Payment Service follows a **modular security architecture**, where fraud validation operates as an independent component, enabling long-term adaptability and safer evolution of security mechanisms.