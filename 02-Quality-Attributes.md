# 02 - Quality Attributes (Non-Functional Requirements)

## Introduction

Quality Attributes, often referred to as Non-Functional Requirements (NFRs), are the characteristics or properties that a system must possess to meet the needs of its stakeholders. Unlike functional requirements, which describe *what* the system does, NFRs describe *how well* the system performs its functions. They are a cornerstone of architectural design, as they dictate the architectural choices, technology stack, and overall design of a system.

## 1. What are Quality Attributes (NFRs)?

Quality Attributes define the "ilities" of a system – how well it performs, scales, secures, and maintains itself. They are critical because they directly influence the design decisions and trade-offs an architect must make.

### Key Distinctions:
*   **Functional Requirements:** Define the specific actions or services the system must perform (e.g., "the system shall allow users to log in").
*   **Non-Functional Requirements (Quality Attributes):** Define the quality characteristics of those actions or services (e.g., "the system shall log in users within 2 seconds for 95% of requests").

## 2. Key Quality Attributes

Here are some of the most common and critical quality attributes:

### Performance
*   **Definition:** The responsiveness of the system to user actions or events under various conditions.
*   **Metrics:** Response time (latency), throughput (transactions per second), resource utilization (CPU, memory, I/O).
*   **Architectural Impact:** Influences choices like caching strategies, asynchronous processing, database indexing, efficient algorithms, and load balancing.

### Scalability
*   **Definition:** The ability of a system to handle an increasing amount of work or users by adding resources.
*   **Types:**
    *   **Vertical Scaling (Scale Up):** Adding more resources (CPU, RAM) to an existing server.
    *   **Horizontal Scaling (Scale Out):** Adding more servers or instances to distribute the load.
*   **Architectural Impact:** Drives decisions around stateless services, load balancing, distributed databases, message queues, and container orchestration (e.g., Kubernetes).

### Reliability
*   **Definition:** The probability that a system will perform its intended function without failure for a specified period under specified conditions.
*   **Metrics:** Mean Time Between Failures (MTBF), error rates.
*   **Architectural Impact:** Requires fault-tolerant designs, robust error handling, retry mechanisms, circuit breakers, and strong data integrity measures.

### Availability
*   **Definition:** The proportion of time a system is operational and accessible when required for use. Often expressed as a percentage (e.g., "four nines" = 99.99%).
*   **Metrics:** Uptime percentage, Mean Time To Recovery (MTTR).
*   **Architectural Impact:** Demands redundancy (active-active, active-passive), failover mechanisms, disaster recovery plans, load balancing across multiple zones/regions, and robust monitoring.

### Security
*   **Definition:** The ability of a system to protect information and data from unauthorized access, use, disclosure, disruption, modification, or destruction.
*   **Aspects:** Authentication, authorization, data encryption (at rest and in transit), input validation, threat modeling, auditing, and compliance.
*   **Architectural Impact:** Involves secure coding practices, Identity and Access Management (IAM), firewalls, intrusion detection systems, API gateways, secure communication protocols (TLS), and regular security audits.

### Maintainability
*   **Definition:** The ease with which a system can be modified, understood, and repaired.
*   **Aspects:** Modifiability, testability, extensibility, understandability, simplicity.
*   **Architectural Impact:** Favors modular design, clear interfaces, loose coupling, high cohesion, adherence to design principles (SOLID), comprehensive documentation, and automated testing.

### Usability/User Experience (UX)
*   **Definition:** The ease with which users can learn, operate, and interact with the system to achieve their goals.
*   **Aspects:** Learnability, efficiency, satisfaction, error prevention.
*   **Architectural Impact:** While often UI-focused, architecture can support usability through responsive APIs, fast data retrieval, and consistent data models.

### Portability
*   **Definition:** The ease with which a system can be transferred from one environment (hardware, operating system, cloud provider) to another.
*   **Architectural Impact:** Encourages platform-agnostic technologies, containerization (Docker), cloud-agnostic design patterns, and standardized interfaces.

### Operability/Manageability
*   **Definition:** The ease with which a system can be monitored, deployed, configured, and managed in a production environment.
*   **Architectural Impact:** Requires robust logging, monitoring, alerting, centralized configuration management, automated deployment (CI/CD), and self-healing capabilities.

### Cost
*   **Definition:** The financial outlay associated with developing, deploying, operating, and maintaining the system.
*   **Aspects:** Development cost, infrastructure cost (cloud resources), operational cost (support, maintenance), licensing.
*   **Architectural Impact:** Influences technology choices (open source vs. commercial), cloud service selection, resource optimization, and architectural complexity.

## 3. How to Define and Prioritize Quality Attributes

*   **Make them Measurable:** NFRs must be quantifiable. Instead of "the system should be fast," specify "the system shall respond to user login requests within 2 seconds for 95% of requests." This leads to Service Level Objectives (SLOs) and Service Level Indicators (SLIs).
*   **Elicitation Techniques:**
    *   **Interviews:** Discuss NFRs directly with stakeholders.
    *   **Workshops:** Facilitate group discussions to identify and prioritize NFRs.
    *   **Scenarios/Use Cases:** Analyze how NFRs apply to specific user interactions.
*   **Prioritization:** Not all NFRs can be equally important. Techniques include:
    *   **MoSCoW Method:** Must-have, Should-have, Could-have, Won't-have.
    *   **Ranking:** Ordering NFRs by importance.
    *   **Weighted Scoring:** Assigning scores based on business value and technical feasibility.

## 4. Trade-off Analysis

Quality attributes often conflict with each other; improving one might degrade another. Managing these trade-offs is a core responsibility of an architect.

*   **Examples of Conflicts:**
    *   **Security vs. Performance:** Strong encryption can add overhead.
    *   **Performance vs. Cost:** Achieving ultra-low latency might require expensive resources.
    *   **Availability vs. Consistency:** In distributed systems, a choice often exists between high availability and strong data consistency (CAP theorem).
    *   **Maintainability vs. Performance:** Highly optimized, complex code might be faster but harder to understand and maintain.
*   **Architectural Drivers:** For any given system, a few quality attributes will be paramount. These "architectural drivers" heavily influence the core design decisions and must be identified early.

## 5. Impact on Architectural Design

The chosen quality attributes directly inform the architectural patterns and technology selections:

*   **High Availability:** Leads to patterns like active-active deployments, replication, load balancing, and multi-region architectures.
*   **High Performance:** Might suggest caching layers, Content Delivery Networks (CDNs), asynchronous processing, event-driven architectures, and optimized data stores.
*   **Scalability:** Often points towards microservices, serverless functions, message queues, and horizontally scalable databases.
*   **Security:** Requires robust authentication/authorization services, API gateways, network segmentation, and secure communication protocols.

## Conclusion

Understanding, defining, and effectively managing quality attributes is fundamental to designing a successful, sustainable, and fit-for-purpose system. They are the non-negotiable characteristics that determine a system's fitness for its intended use and its long-term viability.
