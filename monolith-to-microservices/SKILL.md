# Monolith to Microservices

## Description
Use this skill to break down a monolithic application repository into a microservices architecture. Applies microservice design patterns and database isolation techniques to decompose tightly coupled monoliths into loosely coupled services.

## Use When 
- You have a large monolithic codebase that is difficult to maintain and scale
- The monolith contains multiple domains/bounded contexts that can be extracted
- You want to migrate incrementally to microservices rather than a full rewrite 
- The user asks to decompose a monolith, extract services, or migrate to microservices

## Don't Use When
- The codebase is small and cohesive, a monolith may be more appropriate
- Domains are too tightly coupled to cleanly separate
- A full rewrite is preferred over incremental migration
- User just needs help with microservice patterns in a greenfield project

## Process
1. Understand the domain:
   - Work with the user to create a bounded context model of the monolith 
   - Have the user explain the key entities, aggregates and contexts
   - Align on the bounded context map before proceeding
2. For each bounded context:
   - Extract the domain model and business logic into a separate microservice
   - Define the microservice API contract
   - Create a memory file to store domain knowledge for that service
   - Document table schema, access patterns, business purpose, and ownership 
   - Analyze read/write ratios, number of writers, transaction boundaries, and cross-service dependencies
   - Identify hot tables based on write contention, concurrency, and scaling needs
   - Discuss multiple options with user and document trade-offs:
     - Keep shared initially: simpler, allows transactions, but couples services 
     - Split: improves autonomy, but requires eventual consistency handling
     - Duplicate: optimizes reads, but creates data duplication overhead
     - CQRS/event-driven: enables scaling, but adds complexity 
   - Keep tables shared temporarily when contention is low and boundaries are unclear
   - Split tables when there is clear domain ownership, scaling needs, or high contention
   - Prefer sagas, outbox pattern, change data capture and idempotent retries over distributed transactions
   - Explicitly document eventual consistency boundaries for split tables
   - Use events or change data capture to handle duplicate data and define ownership

   - Recommend appropriate database types (SQL vs NoSQL) and schema designs

3. Decouple services:
   - Replace synchronous calls between domains with asynchronous messaging 
   - Use an API gateway pattern to route requests to microservices
   - Implement a pub/sub model or event streaming for inter-service communication
4. Handle cross-cutting concerns:
   - Implement distributed tracing to monitor requests across services
   - Migrate shared code/libraries to services that own them
   - Use a separate service for shared concerns like auth, logging, monitoring
5. Incrementally migrate traffic from the monolith to microservices
   - Use the Strangler Fig pattern to route increasing traffic to services
   - Maintain backward compatibility in APIs to avoid breaking changes
6. Fully retire the monolith once all functionality has been migrated

## Design Patterns to Use  
- Decompose by subdomain - split based on business capabilities
- Database per Service - each service manages its own data store
- Saga - use compensating transactions to maintain data consistency across services 
- API Composition - the API gateway composes responses from multiple services
- Domain event - use events to communicate state changes between services
- Command Query Responsibility Segregation (CQRS) - separate read and write models
- Distributed Tracing - instrument services to track requests end-to-end

## Common Pitfalls
- Shared database between services - leads to tight coupling. Use database-per-service. 
- Insufficiently decoupled services - services should be independently deployable
- Overuse of distributed transactions - use sagas or event-driven designs instead
- Ignoring cross-cutting concerns until the end - consider logging, monitoring, auth early
- Not maintaining parity with the monolith - avoid breaking changes during migration

## References
- Monolithic to Microservices Migration Research Paper: https://vfast.org/journals/index.php/VTCS/article/view/1808/1508
- Microservices Patterns by Chris Richardson: https://microservices.io/patterns/index.html 
- Monolith to Microservices by Sam Newman: https://samnewman.io/books/monolith-to-microservices/
