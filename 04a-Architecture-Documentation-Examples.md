# 04a - Architecture Documentation Examples

This document provides practical examples of an Architecture Decision Record (ADR) and a textual description of the C4 Model for a hypothetical system. These examples are intended to complement the theoretical explanation of architectural documentation.

---

## Example: Architecture Decision Record (ADR)

This ADR illustrates a common architectural decision: choosing a message broker for asynchronous communication in a microservices environment.

```markdown
# 0005 - Use Apache Kafka for Asynchronous Communication

## Status

Accepted

## Context

Our microservices architecture relies heavily on asynchronous communication for various business processes, including order processing, notification delivery, and data synchronization between services. Currently, we are using direct HTTP calls for some inter-service communication and a basic in-memory queue for others, which is not scalable, reliable, or observable enough for our growing needs.

We need a robust, scalable, and fault-tolerant message broker to handle high-throughput event streams, enable loose coupling between services, and support event-driven patterns. Key requirements include:
*   High throughput and low latency for event delivery.
*   Durability and fault tolerance to prevent message loss.
*   Scalability to handle increasing message volumes.
*   Support for multiple consumers and consumer groups.
*   Good ecosystem support and community.
*   Integration with existing .NET Core services.

## Decision

We will adopt **Apache Kafka** as our primary message broker for asynchronous inter-service communication and event streaming.

## Consequences

### Positive
*   **High Throughput & Scalability:** Kafka is designed for high-volume, real-time data streams, providing excellent performance and horizontal scalability.
*   **Durability & Fault Tolerance:** Messages are persisted to disk and replicated across multiple brokers, ensuring data durability and high availability.
*   **Loose Coupling:** Services can publish events without knowing about their consumers, and consumers can subscribe to events without knowing about producers.
*   **Event Sourcing & Stream Processing:** Kafka's log-based architecture naturally supports event sourcing patterns and enables future integration with stream processing frameworks (e.g., Kafka Streams, Flink).
*   **Rich Ecosystem:** A mature ecosystem with client libraries for .NET Core, monitoring tools, and integration with various data platforms.
*   **Ordered Delivery:** Guarantees message order within a partition, which is crucial for many business processes.

### Negative
*   **Operational Complexity:** Kafka is a distributed system and can be complex to set up, configure, and operate, requiring specialized knowledge. Managed services (e.g., AWS MSK, Confluent Cloud) can mitigate this but add cost.
*   **Learning Curve:** Developers will need to learn Kafka concepts (topics, partitions, consumer groups, offsets).
*   **Resource Intensive:** Can be resource-intensive in terms of CPU, memory, and disk I/O, especially at high scale.
*   **No Built-in Message Routing/Filtering:** While consumers can filter messages, Kafka itself doesn't provide advanced routing capabilities like some traditional message queues (e.g., RabbitMQ exchanges).

### Alternatives Considered

*   **RabbitMQ:**
    *   **Pros:** Mature, flexible routing capabilities, good for traditional message queuing patterns, simpler to operate than Kafka for basic use cases.
    *   **Cons:** Not designed for high-throughput event streaming like Kafka, message persistence can impact performance, less suitable for event sourcing.
    *   **Reason for Rejection:** While good for traditional queues, it doesn't meet our long-term needs for high-volume event streaming and event sourcing patterns as effectively as Kafka.

*   **Azure Service Bus / AWS SQS/SNS:**
    *   **Pros:** Fully managed, lower operational overhead, good integration with respective cloud ecosystems.
    *   **Cons:** Can be more expensive at high scale compared to self-managed Kafka, less flexible for complex stream processing, vendor lock-in.
    *   **Reason for Rejection:** While attractive for managed services, we wanted a solution that offered more control and flexibility for advanced event streaming patterns and potential multi-cloud strategy, which Kafka provides. We can still use managed Kafka services.

## Decision Maker

[Your Name/Role]

## Date

2025-10-31
```

---

## Example: C4 Model Description for an E-commerce Platform

This section describes a simplified C4 Model for a hypothetical e-commerce platform. Remember that C4 diagrams are visual, but this textual representation aims to convey the information typically found at each level.

### Level 1: System Context Diagram

**System Name:** Online Store

**Description:** The Online Store allows customers to browse products, place orders, and manage their accounts. It integrates with external payment gateways and a shipping provider.

*   **Users:**
    *   **Customer:** Browses products, places orders, manages profile.
    *   **Admin:** Manages products, orders, and customer data.
*   **External Systems:**
    *   **Payment Gateway:** Processes credit card payments (e.g., Stripe, PayPal).
    *   **Shipping Provider:** Handles order fulfillment and delivery (e.g., FedEx, UPS API).
    *   **Email Service:** Sends transactional emails (e.g., SendGrid, AWS SES).

**Conceptual Diagram (Textual Representation):**

```
+-----------------+
|     Customer    |
+-----------------+
        |
        | (uses)
        V
+-----------------+
|   Online Store  |
| (System Under   |
|    Consideration)|
+-----------------+
        ^   ^
        |   |
(manages)|   | (uses)
        |   |
+-----------------+
|      Admin      |
+-----------------+

Online Store -- (uses) --> Payment Gateway
Online Store -- (uses) --> Shipping Provider
Online Store -- (uses) --> Email Service
```

### Level 2: Container Diagram

**System Name:** Online Store

**Description:** The Online Store is composed of a Single Page Application (SPA), a set of backend APIs, a product database, an order database, and a message queue for asynchronous tasks.

*   **Containers:**
    *   **Web Application (SPA):** Built with React, runs in the customer's web browser.
    *   **Public API (Microservice):** .NET Core Web API, handles requests from the SPA, interacts with databases and message queue.
    *   **Admin API (Microservice):** .NET Core Web API, handles requests from an internal admin UI (not shown in this diagram), interacts with databases.
    *   **Product Database:** PostgreSQL database, stores product information.
    *   **Order Database:** PostgreSQL database, stores order and customer information.
    *   **Message Queue:** Apache Kafka, used for asynchronous communication (e.g., order fulfillment events, email notifications).

**Conceptual Diagram (Textual Representation):**

```
+-----------------+
|     Customer    |
+-----------------+
        |
        | (uses HTTPS)
        V
+-------------------------+
| Web Application (SPA)   |
| (React, runs in browser)|
+-------------------------+
        |
        | (uses HTTPS)
        V
+-------------------------+
|   Public API            |
| (.NET Core Microservice)|
+-------------------------+
        |
        | (reads/writes)
        V
+-------------------------+
|   Product Database      |
| (PostgreSQL)            |
+-------------------------+

Public API -- (reads/writes) --> Order Database (PostgreSQL)
Public API -- (publishes events) --> Message Queue (Apache Kafka)

+-----------------+
|      Admin      |
+-----------------+
        |
        | (uses HTTPS)
        V
+-------------------------+
|   Admin API             |
| (.NET Core Microservice)|
+-------------------------+
        |
        | (reads/writes)
        V
+-------------------------+
|   Order Database        |
| (PostgreSQL)            |
+-------------------------+

Admin API -- (reads/writes) --> Product Database (PostgreSQL)

Message Queue -- (consumes events) --> Shipping Provider
Message Queue -- (consumes events) --> Email Service
```

### Level 3: Component Diagram (for the Public API Container)

**Container Name:** Public API (Microservice)

**Description:** The Public API handles customer-facing operations, exposing various functionalities through its components.

*   **Components:**
    *   **Authentication Component:** Handles user login, registration, and token validation.
    *   **Product Catalog Component:** Retrieves product details, search, and filtering.
    *   **Shopping Cart Component:** Manages items in the user's cart.
    *   **Order Management Component:** Creates and updates orders, interacts with the Payment Gateway.
    *   **Notification Publisher Component:** Publishes events to the Message Queue for notifications.
    *   **Product Repository:** Handles data access for the Product Database.
    *   **Order Repository:** Handles data access for the Order Database.

**Conceptual Diagram (Textual Representation):**

```
+-------------------------+
|   Public API Container  |
| +---------------------+ |
| | Authentication      | |
| | Component           | |
| +---------------------+ |
| | Product Catalog     | |
| | Component           | |
| +---------------------+ |
| | Shopping Cart       | |
| | Component           | |
| +---------------------+ |
| | Order Management    | |
| | Component           | |
| +---------------------+ |
| | Notification        | |
| | Publisher Component | |
| +---------------------+ |
| | Product Repository  | |
| +---------------------+ |
| | Order Repository    | |
| +---------------------+ |
+-------------------------+

Web Application (SPA) -- (uses) --> Authentication Component
Web Application (SPA) -- (uses) --> Product Catalog Component
Web Application (SPA) -- (uses) --> Shopping Cart Component
Web Application (SPA) -- (uses) --> Order Management Component

Authentication Component -- (uses) --> Order Repository (for user data)
Product Catalog Component -- (uses) --> Product Repository
Shopping Cart Component -- (uses) --> Order Repository
Order Management Component -- (uses) --> Order Repository
Order Management Component -- (uses) --> Payment Gateway
Order Management Component -- (uses) --> Notification Publisher Component
Notification Publisher Component -- (publishes) --> Message Queue

Product Repository -- (uses) --> Product Database
Order Repository -- (uses) --> Order Database
```

---

This document provides a practical reference for how ADRs capture decisions and how the C4 model helps visualize architecture at different levels of detail. For actual C4 diagrams, you would typically use tools like Structurizr, PlantUML, or draw.io.
