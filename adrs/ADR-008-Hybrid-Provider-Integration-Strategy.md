# ADR 008 Hybrid Provider Integration Strategy

## Status

Accepted

## Context

To meet requirement 1.1 and ensure interoperability, the OpenMarket system must integrate with a range of diverse providers. Some of the more legacy providers only support batch file processing (CSV) through older protocols, while modern providers offer real-time integration via APIs.

## Decision

A hybrid integration strategy will be implemented to manage communication with provider systems.

- **SFTP / Batch Processing:** This will be used for legacy providers that require CSV file uploads through batch processes.
    
- **REST API:** This will be used for synchronous integrations and real-time catalog updates with modern providers.
    

By making these decisions, we ensure that OpenMarket can scale its provider network without excluding valid business partners due to technical limitations.

## Consequences

- **Positive:** Full **Interoperability** with diverse provider technology profiles.
    
- **Positive:** Facilitates rapid onboarding of new providers through REST standards.
    
- **Negative:** The system must maintain and monitor two distinct communication channels (SFTP and HTTP/REST), increasing operational effort.
    
- **Neutral:** Requires data transformation components (mappers) to normalize information from CSV and JSON into OpenMarket’s internal data model.
    
