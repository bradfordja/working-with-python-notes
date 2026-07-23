# Microservices and Event-Driven Architecture

## Senior Interview Preparation for ExxonMobil

For a senior-level discussion, avoid describing microservices as simply “small services.” Focus on **business boundaries, operational independence, failure isolation, security, observability, and delivery autonomy**.

A strong opening answer is:

> “I use microservices when the business domain, scalability requirements, deployment cadence, and team ownership justify independent services. I define services around bounded business capabilities, keep data ownership local to each service, use synchronous APIs only where an immediate response is required, and use events for decoupled workflows, integration, and state propagation. The design must include failure handling, security, observability, deployment, and operational ownership from the beginning.”

---

# 1. Reference Architecture

A typical enterprise architecture may look like this:

```text
Users
  |
  v
React / Next.js Portal
  |
  v
API Gateway / Load Balancer
  |
  +-------------------+--------------------+
  |                   |                    |
  v                   v                    v
Trade Service     Position Service     Reporting Service
  |                   |                    |
  v                   v                    v
Trade DB          Position DB          Reporting Store
  |
  v
Event Broker: Kafka
  |
  +--------------+---------------+----------------+
  |              |               |                |
  v              v               v                v
Risk Service   Audit Service   Notification     Analytics
```

Senior-level points:

* Each service owns a clear business capability.
* Each service owns its data.
* Services should be independently deployable.
* Kafka or another broker decouples producers from consumers.
* API Gateway centralizes cross-cutting concerns, but not business logic.
* Reporting often consumes events instead of querying operational services directly.
* Audit and compliance are treated as first-class architecture concerns.

---

# 2. Service Boundaries

The most important microservices decision is not technology. It is deciding where one service ends and another begins.

## Good boundaries

Services should align with business capabilities such as:

* Trade capture
* Pricing
* Risk
* Scheduling
* Settlements
* Position management
* Reporting
* User entitlements
* Audit

## Poor boundaries

Avoid splitting services based only on technical layers:

```text
Customer Controller Service
Customer Business Logic Service
Customer Database Service
```

That creates distributed coupling rather than true business separation.

## Senior answer

> “I identify service boundaries through domain-driven design, data ownership, change patterns, scalability needs, and team responsibility. A service should own a business capability and its data, not simply one technical layer. If two services always change and deploy together, that is usually a sign the boundary is wrong.”

---

# 3. Synchronous vs Event-Driven Communication

Use synchronous communication when the caller requires an immediate response.

Examples:

* Validate a user entitlement
* Retrieve current trade details
* Submit a command requiring immediate acceptance
* Query a current position

Use event-driven communication when the workflow can be decoupled.

Examples:

* Trade created
* Position updated
* Settlement completed
* Report requested
* Pricing data received
* Audit event generated

## Synchronous flow

```text
Portal
  |
  v
Trade API
  |
  v
Risk API
  |
  v
Response
```

Risk:

If the Risk API is unavailable, the entire request may fail.

## Event-driven flow

```text
Trade Service
  |
  v
TradeCreated Event
  |
  v
Kafka
  |
  +---------> Risk Service
  |
  +---------> Reporting Service
  |
  +---------> Audit Service
```

Benefits:

* Lower coupling
* Independent scaling
* Better failure isolation
* Easier integration
* Replay capability
* Supports asynchronous workflows

Trade-offs:

* Eventual consistency
* More complex debugging
* Duplicate event handling
* Schema evolution
* Ordering concerns
* Operational dependency on the broker

## Senior answer

> “I avoid making every interaction synchronous. Synchronous APIs are appropriate for commands and queries that require immediate feedback. Events are better for downstream state propagation, audit, analytics, and long-running workflows. The trade-off is eventual consistency, so consumers must be idempotent and the business must understand when data becomes consistent.”

---

# 4. Failover and Resilience

A senior engineer should discuss failure as a normal operating condition.

## Key resilience patterns

### Timeouts

Every remote call must have a timeout.

```text
No timeout = potentially blocked threads and cascading failure
```

### Retries

Retries should be:

* Limited
* Used only for transient failures
* Exponentially backed off
* Combined with jitter
* Avoided for non-idempotent operations unless protected by an idempotency key

### Circuit breaker

A circuit breaker stops repeatedly calling a failing dependency.

States:

```text
Closed -> requests pass normally
Open -> requests fail fast
Half-open -> limited test requests
```

### Bulkhead isolation

Separate thread pools, connection pools, or compute resources so one failing dependency cannot consume all resources.

### Rate limiting

Protect services from:

* Accidental overload
* Retry storms
* Malicious traffic
* Expensive report queries

### Graceful degradation

A service may return partial or cached data when a dependency is unavailable.

Example:

> Current reference data is unavailable, so return the latest approved cached version with a timestamp.

---

# 5. High Availability

High availability should exist at multiple levels.

## Application layer

* Multiple service replicas
* Load balancing
* Health probes
* Automatic restart
* Stateless application instances
* Zone distribution

## Database layer

* Multi-zone deployment
* Replication
* Automated backups
* Point-in-time recovery
* Connection retry handling
* Tested failover procedures

## Messaging layer

* Replicated broker partitions
* Multiple brokers
* Appropriate replication factor
* Consumer groups
* Durable messages

## Infrastructure layer

* Multiple availability zones
* Infrastructure as code
* Automated recovery
* Capacity headroom
* Disaster recovery planning

## Senior answer

> “Failover is not only adding more application pods. I design for failure across the application, database, broker, network, and infrastructure layers. Services run across availability zones, the database has tested recovery and replication, Kafka topics use appropriate replication, and health checks only send traffic to ready instances.”

---

# 6. Idempotency

Event systems and distributed systems may deliver the same message more than once.

Consumers must safely process duplicates.

## Example

A settlement event is delivered twice.

Without idempotency:

```text
Payment processed twice
```

With idempotency:

```text
Consumer checks event ID or business transaction ID
Duplicate detected
Second execution ignored
```

Implementation options:

* Unique event ID
* Unique business transaction key
* Database unique constraint
* Idempotency table
* Redis deduplication cache
* Processed-event ledger

## Senior answer

> “I assume at-least-once delivery and make consumers idempotent. I use a stable event ID or business key, persist processed-event state, and enforce database uniqueness where possible. I do not rely on the broker alone to guarantee business-level exactly-once processing.”

---

# 7. Transactional Outbox Pattern

A common failure occurs when a service updates its database but fails before publishing the event.

```text
Database commit succeeds
Event publication fails
Downstream systems never receive the event
```

The transactional outbox solves this.

```text
Single Database Transaction
  |
  +--> Update business table
  |
  +--> Insert event into outbox table
```

A separate publisher reads the outbox and sends events to Kafka.

This guarantees that the business change and the intention to publish the event are committed together.

## Senior answer

> “For reliable event publication, I use the transactional outbox pattern. The service writes the domain change and event record in the same local transaction. A publisher then asynchronously sends the event to Kafka. This avoids dual-write inconsistency between the database and broker.”

---

# 8. Distributed Transactions and Sagas

Avoid distributed two-phase commit across microservices when possible.

Use a Saga for long-running business workflows.

Example:

```text
Create Trade
   |
   v
Validate Credit
   |
   v
Reserve Position
   |
   v
Schedule Settlement
```

If one step fails, compensating actions may be required.

```text
Settlement fails
  |
  v
Release reserved position
  |
  v
Mark trade as failed
```

Two Saga styles:

## Choreography

Services react to events.

```text
TradeCreated
  -> CreditValidated
  -> PositionReserved
  -> SettlementScheduled
```

Advantages:

* Loosely coupled
* No central coordinator

Disadvantages:

* Workflow can become difficult to understand
* Harder debugging

## Orchestration

A workflow service directs the process.

Advantages:

* Central visibility
* Easier control
* Clear workflow state

Disadvantages:

* Central orchestrator can become overly complex

## Senior answer

> “I avoid cross-service ACID transactions. For multi-step workflows, I use a Saga. Choreography works for simple, naturally event-driven flows, while orchestration is better when the workflow requires explicit state, timeouts, compensation, and operational visibility.”

---

# 9. Security Architecture

Security should be applied at every boundary.

## User authentication

Common options:

* OAuth 2.0
* OpenID Connect
* Enterprise identity provider
* JWT access tokens
* SSO

## Authorization

Use:

* Role-based access control
* Attribute-based access control
* Fine-grained entitlements
* Resource-level authorization

In a trading environment, authorization may depend on:

* Business unit
* Commodity
* Region
* Trading desk
* Role
* Transaction limit
* Data classification

## Service-to-service security

Use:

* Mutual TLS
* Workload identity
* Short-lived tokens
* Signed JWTs
* Service mesh identity
* Cloud IAM roles

Avoid shared permanent credentials.

## Secrets management

Use:

* AWS Secrets Manager
* HashiCorp Vault
* Kubernetes external secrets
* Automated rotation

Never place secrets in:

* Source code
* Docker images
* Plain Kubernetes manifests
* Application logs

## Data protection

* TLS in transit
* Encryption at rest
* Field-level encryption where required
* Data masking
* Tokenization
* Least privilege
* Audit trails

## Senior answer

> “I apply zero-trust principles. Every user and service call must be authenticated and authorized. I use OIDC for users, workload identity or mTLS for service-to-service traffic, least-privilege access, centralized secret management, and encrypted transport and storage. Authorization is enforced in the service, not only at the gateway.”

---

# 10. API Gateway Responsibilities

An API Gateway should handle cross-cutting concerns:

* Authentication
* Rate limiting
* Request routing
* TLS termination
* Correlation IDs
* Request size limits
* Basic schema enforcement
* API versioning

It should not contain core business logic.

## Senior statement

> “I use the gateway as a policy and traffic-management layer. Business authorization and domain rules remain inside the service because gateway-only enforcement creates security gaps and makes the architecture difficult to test and evolve.”

---

# 11. Event Security

Events must also be secured.

Important controls:

* Topic-level authorization
* Producer and consumer identity
* Encryption in transit
* Schema validation
* Sensitive-data minimization
* Audit logging
* Retention policy
* Access control by domain

Avoid putting unnecessary personal or confidential data into events because events may be retained and replayed.

## Senior answer

> “I treat the event broker as a protected enterprise platform. Producers and consumers receive topic-level permissions, schemas are validated, sensitive fields are minimized, and event retention is aligned with business and compliance requirements.”

---

# 12. Logging Strategy

Logs should be structured, centralized, searchable, and correlated.

## Structured logging

Use JSON:

```json
{
  "timestamp": "2026-07-23T14:30:00Z",
  "level": "ERROR",
  "service": "trade-service",
  "traceId": "abc123",
  "correlationId": "trade-789",
  "eventType": "TradeCreated",
  "message": "Failed to publish event",
  "errorCode": "KAFKA_TIMEOUT"
}
```

Important fields:

* Timestamp
* Service name
* Environment
* Log level
* Trace ID
* Span ID
* Correlation ID
* Business transaction ID
* Event ID
* Error code
* Deployment version

## Do not log

* Passwords
* Tokens
* Secrets
* Full account numbers
* Personally identifiable data
* Confidential trading data unless explicitly approved

## Senior answer

> “I use structured JSON logs and propagate trace IDs, correlation IDs, and business transaction IDs across synchronous and asynchronous boundaries. Logs are centralized and searchable, but I avoid sensitive data and use standardized error codes rather than unstructured messages.”

---

# 13. Observability

Observability includes more than logs.

Use the three pillars:

* Logs
* Metrics
* Traces

## Metrics

Key service metrics:

* Request rate
* Error rate
* Latency
* CPU and memory
* Thread pool usage
* Database pool usage
* Kafka consumer lag
* Retry rate
* Circuit breaker status
* Dead-letter queue size

## Distributed tracing

Tracing helps follow one transaction across multiple services.

```text
Portal
  |
  v
API Gateway
  |
  v
Trade Service
  |
  v
Kafka
  |
  v
Risk Service
```

The same trace or correlation context should continue across the workflow.

## Business metrics

Technical metrics are not enough.

Examples:

* Trades processed
* Failed settlements
* Reports completed
* Events delayed
* Positions out of sync
* Reconciliation failures

## Senior answer

> “I distinguish monitoring from observability. Monitoring tells us that something is wrong; observability helps us determine why. I instrument services with OpenTelemetry, collect logs, metrics, and traces, and combine technical indicators with business KPIs such as failed trades or delayed settlements.”

---

# 14. Service Communication

## REST

Best for:

* Request-response APIs
* External integrations
* Simple service queries
* Broad compatibility

## gRPC

Best for:

* Internal low-latency communication
* Strongly typed contracts
* Streaming
* High-throughput service calls

## Kafka or messaging

Best for:

* Event distribution
* Decoupled workflows
* Audit trails
* Analytics
* Replay
* Integration

## Senior answer

> “I choose the communication style based on business semantics. REST is appropriate for broad compatibility and standard request-response interactions. gRPC is useful for strongly typed, low-latency internal communication. Kafka is appropriate for asynchronous events, integration, audit, and replay. I avoid selecting one technology for every interaction.”

---

# 15. Event Design

Good events represent completed business facts.

Good:

```text
TradeCreated
PositionUpdated
SettlementCompleted
ReportGenerated
```

Weak:

```text
UpdateDatabase
CallReportingService
ProcessMessage
```

Events should contain enough context for consumers, but should not expose the producer’s internal database structure.

## Event envelope

```json
{
  "eventId": "evt-123",
  "eventType": "TradeCreated",
  "eventVersion": "2",
  "occurredAt": "2026-07-23T14:30:00Z",
  "producer": "trade-service",
  "correlationId": "corr-456",
  "payload": {
    "tradeId": "T-1001",
    "commodity": "NaturalGas",
    "quantity": 1000
  }
}
```

---

# 16. Schema Evolution

Event schemas must evolve without breaking consumers.

Strategies:

* Add optional fields
* Avoid renaming or removing fields abruptly
* Version schemas
* Use schema registry
* Support backward compatibility
* Test producer-consumer compatibility

## Senior answer

> “I treat event schemas as public contracts. I use versioning and schema compatibility checks, prefer additive changes, and avoid coupling events to internal persistence models. Producers should not break consumers during independent deployment.”

---

# 17. Event Ordering

Ordering may be critical for trades, positions, or settlements.

Kafka guarantees ordering only within a partition.

To preserve order for one trade:

```text
Partition key = tradeId
```

All events for that trade go to the same partition.

Trade-off:

* More partitions increase parallelism
* One key always maps to one partition
* Hot keys may create uneven load

## Senior answer

> “When ordering matters, I partition by the business aggregate key, such as trade ID or account ID. I avoid assuming global ordering because it reduces scalability and is rarely required.”

---

# 18. Dead-Letter Queues

Messages that repeatedly fail should not block the consumer indefinitely.

Typical flow:

```text
Kafka Topic
   |
   v
Consumer
   |
   +--> Success
   |
   +--> Retry Topic
           |
           +--> Dead-Letter Topic
```

The DLQ should include:

* Original event
* Error reason
* Retry count
* Timestamp
* Consumer name
* Trace ID

A DLQ must have an operational process. It is not simply a place to hide failures.

## Senior answer

> “I use retry topics for transient errors and a dead-letter topic for messages that require investigation or manual correction. DLQ volume is monitored, ownership is explicit, and replay is controlled to avoid duplicate business actions.”

---

# 19. Deployment Architecture

A common deployment platform is Kubernetes or AWS EKS.

```text
CI Pipeline
  |
  v
Build and Test
  |
  v
Container Image
  |
  v
Security Scan
  |
  v
Artifact Registry
  |
  v
Deploy to Kubernetes
```

## Kubernetes concerns

* Deployments
* Services
* Ingress
* ConfigMaps
* Secrets
* Readiness probes
* Liveness probes
* Resource requests and limits
* Horizontal Pod Autoscaling
* Pod disruption budgets
* Rolling updates
* Network policies

## Readiness vs liveness

### Readiness

Can the instance receive traffic?

### Liveness

Is the process alive, or should Kubernetes restart it?

Do not make liveness depend on every external dependency. Otherwise a database outage may cause all pods to restart repeatedly.

## Senior answer

> “Readiness determines whether a pod should receive traffic. Liveness determines whether the process is unhealthy enough to restart. I keep liveness focused on the application process and use readiness for critical startup or dependency conditions.”

---

# 20. Deployment Strategies

## Rolling deployment

Gradually replaces instances.

Good default for low-risk changes.

## Blue-green deployment

Two complete environments:

```text
Blue = current
Green = new
```

Traffic switches after validation.

## Canary deployment

Send a small percentage of traffic to the new version.

Useful for:

* High-risk changes
* Performance validation
* Gradual rollout

## Feature flags

Separate deployment from feature release.

## Senior answer

> “For routine changes I use rolling deployment. For high-risk services, I prefer canary or blue-green release patterns with automated health validation and rollback. Feature flags allow deployment without immediately exposing the feature.”

---

# 21. CI/CD Pipeline

A mature pipeline should include:

```text
Code Commit
  |
  v
Linting
  |
  v
Unit Tests
  |
  v
Integration Tests
  |
  v
Contract Tests
  |
  v
Security Scanning
  |
  v
Container Build
  |
  v
Image Scan
  |
  v
Deploy
  |
  v
Smoke Test
  |
  v
Observability Validation
```

Important controls:

* Immutable artifacts
* Signed images
* Dependency scanning
* Secrets scanning
* Infrastructure validation
* Automated rollback
* Approval gates for production
* Deployment audit history

---

# 22. Database per Service

Each microservice should control its own data.

Avoid:

```text
Service A
Service B
Service C
   |
   v
One shared database schema
```

Problems:

* Tight coupling
* Independent deployment becomes difficult
* Hidden dependencies
* Schema changes break multiple services
* Ownership is unclear

Preferred:

```text
Trade Service -> Trade DB
Risk Service -> Risk DB
Reporting Service -> Reporting Store
```

Data is shared through APIs or events.

## Senior answer

> “I prefer database ownership by service. Other services should not directly query another service’s tables. Shared databases create implementation coupling and undermine independent delivery. Reporting data should usually be replicated through events into a purpose-built read model.”

---

# 23. CQRS for Reporting

Reporting workloads often conflict with transaction workloads.

CQRS separates:

* Command model
* Query model

```text
Trade Service
  |
  v
Trade Events
  |
  v
Reporting Consumer
  |
  v
Reporting Database
```

The reporting store may be optimized for:

* Aggregation
* Search
* Large reads
* Historical views
* Dashboard queries

## Senior answer

> “I avoid running large reports against transactional databases. I use event-driven replication to create a separate reporting model optimized for analytical queries. That protects operational performance and gives reporting teams more flexibility.”

---

# 24. Production Failure Scenario

An interviewer may ask:

> What happens if Kafka is unavailable when the trade is created?

Strong answer:

> “The trade service should not perform an unsafe dual write. I would commit the trade and an outbox record in one database transaction. The outbox publisher retries event delivery when Kafka becomes available. Alerts should trigger on outbox backlog growth. This preserves the business transaction and prevents event loss.”

---

# 25. Another Failure Scenario

> What happens if a consumer processes the event but crashes before acknowledging it?

Strong answer:

> “The broker may redeliver the event. The consumer must be idempotent, using the event ID or business transaction key to prevent duplicate processing. The database update and processed-event record should be committed atomically.”

---

# 26. Security Scenario

> How would you secure an internal trading portal?

Strong answer:

> “I would integrate with the enterprise identity provider using OIDC and SSO. The gateway would validate the token, but each service would enforce domain-level authorization. Permissions could depend on role, trading desk, region, commodity, and transaction limits. Service-to-service calls would use workload identity or mTLS. Secrets would be stored in a managed vault, and all sensitive actions would be audited.”

---

# 27. Logging Scenario

> How do you debug a failed transaction across multiple services?

Strong answer:

> “I propagate a correlation ID and distributed trace context from the portal through the gateway, services, and Kafka events. I use centralized structured logs, OpenTelemetry traces, and business transaction IDs. I can then follow the transaction across synchronous calls, asynchronous consumers, retries, and the dead-letter queue.”

---

# 28. Direct Senior-Level Summary

A concise interview answer:

> “My approach to microservices is domain-first and operations-aware. I define services around business capabilities, maintain database ownership, use synchronous APIs only where immediate responses are required, and use events for decoupled workflows and reporting. I design for failure with timeouts, retries, circuit breakers, idempotency, outbox publication, dead-letter handling, and Sagas. Security is zero-trust, with OIDC, workload identity, least privilege, encryption, and auditability. Observability includes structured logs, metrics, distributed tracing, correlation IDs, and business KPIs. Deployment uses automated CI/CD, immutable containers, Kubernetes health checks, controlled rollout strategies, and tested rollback.”

---

# ExxonMobil-Relevant Positioning

For an energy or trading environment, emphasize:

* High reliability
* Transaction integrity
* Auditability
* Entitlement security
* Global availability
* Real-time and near-real-time events
* Reconciliation
* Historical reporting
* Controlled release management
* Integration with legacy platforms
* Business continuity
* Data lineage

A useful statement:

> “In energy and trading environments, the architecture must support more than scale. It must preserve transaction integrity, event ordering where required, detailed auditability, data lineage, role-based entitlements, and operational recovery. I would prioritize correctness and traceability before optimizing for raw throughput.”

---

# Questions to Ask the Interviewer

Use one or two:

> “How does the team currently divide synchronous API communication from event-driven workflows?”

> “What messaging platform is used, and how are schema compatibility, replay, and dead-letter processing managed?”

> “Are services independently owned and deployed, or are there still shared database and release dependencies?”

> “How does the team trace a business transaction across APIs, events, and downstream reporting systems?”

> “What are the current availability, recovery-time, and recovery-point objectives for the platform?”
