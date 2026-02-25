# ADR 007 Search and Cache Segregation

## Status

Accepted

## Context

According to requirement 2.1, a responsive user interface is required where customers do not experience excessive delays when browsing or filtering products. Performing complex searches and advanced filtering directly on the relational database (PostgreSQL) under heavy traffic loads (Elasticity, Req 3.1) would compromise the system’s availability and responsiveness.

## Decision

It has been decided to offload read and search operations from the transactional database primarily through the implementation of two specialized technologies.

1. **Redis Cache Server:** This will be used as an in-memory data store for frequently accessed product listings and prices, ensuring ultra-fast reads.
    
2. **Elasticsearch (Search Engine):** This will act as a dedicated indexer to handle complex text searches and dynamic catalog filtering without overloading the main database.
    

## Consequences

- **Positive:** Significant improvement in **Responsiveness** for the end user.
    
- **Positive:** Greater **Elasticity**, allowing the system to handle spikes in search traffic independently of the transactional database.
    
- **Negative:** Increased architectural complexity due to the need to synchronize data among PostgreSQL, Redis, and Elasticsearch.
    
- **Negative:** Requires a cache invalidation and index update strategy to prevent stale data.
    