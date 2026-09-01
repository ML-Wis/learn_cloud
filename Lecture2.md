## Table of Contents

1. [Lecture Overview](#1-lecture-overview)
2. [Cloud Computing Service Models](#2-cloud-computing-service-models)
3. [Technology Challenges](#3-technology-challenges)
   - [Scalability](#31-scalability)
   - [Elasticity](#32-elasticity)
   - [Performance Unpredictability](#33-performance-unpredictability)
   - [Reliability and Availability](#34-reliability-and-availability)
   - [Security](#35-security)
   - [Compliance](#36-compliance)
   - [Multi-tenancy](#37-multi-tenancy)
4. [Business Drivers of Cloud Computing](#4-business-drivers-of-cloud-computing)
5. [Inhibitors to Cloud Adoption](#5-inhibitors-to-cloud-adoption)
6. [Cloud Deployment Models](#6-cloud-deployment-models)
7. [Public vs Private Cloud](#7-public-vs-private-cloud)
8. [Applying the Concepts — Architecture Exercises](#8-applying-the-concepts--architecture-exercises)
9. [Quick Revision Table](#9-quick-revision-table)
10. [Important Exam Questions](#10-important-exam-questions)
11. [One-Page Mental Model](#11-one-page-mental-model)

---

# 1. Lecture Overview

The previous lecture introduced different **cloud computing service models**. This lecture focuses on the major **technology challenges** that arise when applications and infrastructure are moved to the cloud.

The main challenges discussed are:

- **Scalability**
- **Elasticity**
- **Performance unpredictability**
- **Reliability and availability**
- **Security**
- **Compliance**
- **Multi-tenancy**

The lecture then looks at:

- Why organizations adopt cloud computing
- What prevents organizations from adopting cloud computing
- Different cloud deployment models
- Public cloud vs private cloud
- Practical architecture decisions

> **Central idea:** Cloud computing provides major benefits such as scalability, flexibility, automation and pay-per-use, but achieving these benefits requires solving difficult problems involving performance, failures, security, regulation and sharing of infrastructure.

---

# 2. Cloud Computing Service Models

The lecture begins by recalling three major cloud service models:

```text
                 CLOUD COMPUTING
                       |
        +--------------+--------------+
        |              |              |
       IaaS           PaaS           SaaS
```

## 2.1 IaaS — Infrastructure as a Service

With **IaaS**, the customer gets infrastructure resources such as virtual machines and other basic computing resources.

The customer has relatively high control over:

- Operating system
- Software
- Application
- Configuration

### Basic idea

```text
Application
    ↓
Software / Libraries
    ↓
Operating System
    ↓
Virtual Machine
    ↓
Cloud Infrastructure
```

The lecture describes IaaS as suitable for customers who want control over the OS and software and want to develop applications.

### Example from the lecture

**Amazon AWS**

The lecture notes that:

- VMs look like hardware to the user.
- There is no restriction on the application model.
- The user must implement scalability and failover.

---

## 2.2 PaaS — Platform as a Service

With **PaaS**, the provider manages more of the underlying infrastructure and provides a platform on which developers can build applications.

The customer primarily focuses on the application.

```text
Application
    ↓
Platform
    ↓
Infrastructure
```

The lecture describes PaaS as suitable for customers who want to develop applications.

### Examples mentioned

- Microsoft Azure
- Force.com / Salesforce

The lecture also associates PaaS with capabilities such as automatic provisioning and scaling.

---

## 2.3 SaaS — Software as a Service

With **SaaS**, the customer primarily wants to **use an application** rather than build and manage the underlying infrastructure.

```text
Cloud Provider
      ↓
Complete Application
      ↓
     User
```

The lecture describes SaaS as suitable for customers who simply want to use an application.

---

## 2.4 Easy way to remember

| Model | Main idea |
|---|---|
| **IaaS** | Rent infrastructure and control more of the stack |
| **PaaS** | Develop applications using a managed platform |
| **SaaS** | Use a complete application |

### Memory trick

> **IaaS → Infrastructure**  
> **PaaS → Platform**  
> **SaaS → Software**

---

# 3. Technology Challenges

Cloud computing looks attractive because resources can be obtained on demand. However, operating a large cloud system creates several difficult technical problems.

The lecture focuses on seven major areas:

```text
                 Technology Challenges
                         |
      +------------------+------------------+
      |        |         |        |         |
   Scale   Elasticity  Performance  Reliability
                              |
                           Security
                              |
                         Compliance
                              |
                        Multi-tenancy
```

---

# 3.1 Scalability

## What is scalability?

**Scalability is the ability of a system to handle increasing workload.**

Imagine a website initially has:

```text
100 users
   ↓
1 server
```

Then the number of users grows:

```text
100 users
   ↓
10,000 users
   ↓
1,000,000 users
```

The system needs to continue providing an acceptable service.

This is the scalability problem.

---

## Why is scalability important?

Large websites can receive enormous numbers of requests while still trying to maintain good response times.

The lecture uses popular websites such as Facebook and Google as examples of systems dealing with high traffic while maintaining relatively low response times.

The lecture also highlights very large storage requirements:

- Google stores many copies of web content and adds storage at very large scale.
- Facebook adds several petabytes of storage per day according to the lecture material.

### The important point

It is not enough to handle more users.

The system should also maintain good performance.

```text
Increasing workload
        +
Acceptable response time
        ↓
     Scalability
```

---

## Vertical Scaling

One way to scale is to make a single machine more powerful.

```text
Before:

4 CPU
8 GB RAM

       ↓

After:

16 CPU
64 GB RAM
```

This is commonly called:

> **Scale Up**

---

## Horizontal Scaling

Another approach is to add more machines.

```text
                 Load Balancer
                /      |      \
               ↓       ↓       ↓
           Server 1 Server 2 Server 3
```

This is commonly called:

> **Scale Out**

Cloud systems often make extensive use of adding resources or instances to deal with increasing demand.

---

## Scalability challenge

A cloud system needs mechanisms that allow resources to be allocated effectively as workload grows.

Important concerns include:

- How many resources are needed?
- Where should workloads be placed?
- How should traffic be distributed?
- How can response time remain acceptable?

---

# 3.2 Elasticity

## What is elasticity?

**Elasticity is the ability to increase or decrease resources according to workload.**

This is closely related to scalability, but elasticity emphasizes **dynamic adjustment**.

Example:

### Low traffic

```text
100 users
   ↓
2 servers
```

### High traffic

```text
100,000 users
      ↓
50 servers
```

### Traffic decreases

```text
500 users
   ↓
2 servers
```

The system should be able to adapt.

---

## Why is elasticity difficult?

The lecture asks:

> How can resources be scaled up and down quickly?

This requires mechanisms such as:

- Resource allocation
- Workload placement
- Decisions about when to add resources
- Decisions about when to remove resources

The lecture specifically states that **resource allocation and workload placement algorithms are required**.

---

## Scalability vs Elasticity

This distinction is important.

| Scalability | Elasticity |
|---|---|
| Ability to handle increasing workload | Ability to dynamically adjust resources |
| Focuses strongly on growth | Focuses on increasing and decreasing resources |
| May involve adding resources | Adds and removes resources according to demand |

### Simple example

**Scalability:**

> My system can handle 1 million users.

**Elasticity:**

> My system automatically adds servers when traffic increases and removes them when traffic falls.

---

# 3.3 Performance Unpredictability

Cloud environments commonly involve **shared resources**.

For example:

```text
                 Physical Server
                /       |       \
               ↓        ↓        ↓
            Tenant A Tenant B Tenant C
```

Different users or applications may share underlying infrastructure.

The lecture identifies:

> **Resources are shared**

and asks:

> **How can performance isolation be guaranteed?**

---

## Why is this a problem?

Suppose your application normally receives:

```text
Request → 100 ms
```

Another workload running on shared infrastructure may suddenly consume significant resources.

Your application could experience:

```text
Request → 500 ms
```

This creates **performance unpredictability**.

---

## Performance Isolation

The cloud environment should try to ensure that:

> One customer's workload does not significantly degrade another customer's performance.

This is known as **performance isolation**.

---

## Key idea

```text
Shared Infrastructure
        ↓
Resource contention
        ↓
Possible performance variation
        ↓
Need performance isolation
```

---

# 3.4 Reliability and Availability

The lecture identifies two major factors affecting reliability and availability:

1. **High number of components**
2. **Complexity**

It uses AWS outages as an example of the challenges created by complex cloud systems.

---

## Reliability

Reliability refers to the ability of a system to continue operating correctly despite failures.

---

## Availability

Availability refers to whether the service is accessible and operational when users need it.

A system can contain many components:

```text
User
 ↓
Network
 ↓
Load Balancer
 ↓
Web Server
 ↓
Application Server
 ↓
Database
 ↓
Storage
```

Every additional component creates another possible point of failure.

---

## Failure example

Suppose:

```text
Server 1 → FAILED
```

A highly available architecture should have another server that can continue serving users.

```text
                 Load Balancer
                /             \
               ↓               ↓
          Server 1          Server 2
             ❌                 ✓
                               ↑
                         Continue serving
```

This general idea is called **failover**.

---

## Why cloud reliability is challenging

A large cloud service may contain:

- Many servers
- Networks
- Storage systems
- Databases
- Load balancers
- Virtual machines
- Management services

Therefore:

> More components + more complexity → more opportunities for failures.

Good architecture needs mechanisms to tolerate failures.

---

# 3.5 Security

Security is one of the major challenges highlighted by the lecture.

The basic question is:

> **How can cloud systems protect users, applications, infrastructure and data?**

The lecture gives several security incidents as examples, including:

- LinkedIn — 2012
- Home Depot — 2014
- Apple iCloud — incident discussed in the lecture
- Microsoft incident
- Sony incident

---

## Security involves multiple layers

Security is not just about protecting a server.

It can involve:

```text
                  SECURITY
                     |
       +-------------+-------------+
       |             |             |
      Data        Identity      Infrastructure
       |             |             |
  Encryption       MFA         Isolation
  Access control   Passwords   Network security
```

---

## Microsoft Incident

The lecture describes a Microsoft incident in which:

- Password spraying was used.
- The target was a legacy test account.
- The account lacked MFA.
- Emails and documents of senior Microsoft executives and legal/security teams were accessed.
- The compromise remained undetected for nearly two months.
- Microsoft removed access and disclosed the breach in January 2024.

### Lesson

A secure cloud environment needs more than infrastructure protection.

It also requires:

- Strong authentication
- MFA
- Access control
- Monitoring
- Protection of legacy accounts
- Good security processes

---

## Isolation of Users

A cloud provider may serve many customers simultaneously.

For example:

```text
Cloud
 ├── Customer A
 ├── Customer B
 └── Customer C
```

Customer A should not be able to access Customer B's resources.

Therefore the system needs **user/tenant isolation**.

---

## Physical Security

Security also includes the physical infrastructure.

Cloud providers operate physical:

- Servers
- Data centers
- Networking equipment
- Storage systems

Therefore physical security is also part of the overall security problem.

---

## Security and shared cloud infrastructure

The lecture asks an important question:

> **Can we be secure if we are in the same cloud as a hacker?**

The lecture discusses a Sony incident involving rented Amazon EC2 servers and an attack against the PlayStation Network.

The conceptual lesson is:

> Sharing cloud infrastructure makes strong isolation and security controls essential.

---

# 3.6 Compliance

Security and compliance are related, but they are **not the same thing**.

### Security asks:

> Can unauthorized people access the system or data?

### Compliance asks:

> Does the organization and its cloud provider follow the required laws, regulations and rules?

---

## Why does compliance matter?

Different industries can have different requirements.

For example, the lecture mentions:

### Healthcare

Healthcare organizations may have special requirements for handling sensitive healthcare information.

### Sarbanes-Oxley

The lecture mentions **Sarbanes-Oxley (2002)**, associated with financial reporting and audit controls following major corporate accounting scandals.

One example mentioned is:

> Audit controls on transactions.

### India — SEBI Clause 49

The lecture also mentions **SEBI Clause 49**, associated with corporate governance practices.

---

## Questions to ask about compliance

When moving to cloud, an organization needs to consider:

- Is the cloud provider compliant?
- Where is data stored?
- Who can access it?
- Are required controls implemented?
- Can activities be audited?
- Are legal requirements satisfied?

---

## Security vs Compliance

| Security | Compliance |
|---|---|
| Protects systems and data | Ensures rules/regulations are followed |
| Focuses on unauthorized access and attacks | Focuses on legal/industry requirements |
| Technical controls are important | Technical + organizational + legal controls matter |

---

# 3.7 Multi-tenancy

## What is multi-tenancy?

**Multi-tenancy means that multiple users/customers share cloud resources or infrastructure.**

The lecture specifically describes the idea as:

> Sharing the same database by multiple users.

But the sharing must happen:

> **Without compromising security.**

---

## Example

Imagine:

```text
                  Shared Database
                /       |       \
               ↓        ↓        ↓
          Customer A Customer B Customer C
```

All customers may use the same underlying system.

But:

```text
Customer A ❌→ Customer B's data
Customer B ❌→ Customer C's data
Customer C ❌→ Customer A's data
```

---

## Why use multi-tenancy?

It can allow infrastructure to be shared efficiently among many customers.

Instead of:

```text
Customer A → Separate infrastructure
Customer B → Separate infrastructure
Customer C → Separate infrastructure
```

we can have:

```text
             Shared Infrastructure
              /       |       \
             A        B        C
```

---

## Main challenge

The central problem is:

> **How do we share resources while maintaining isolation and security?**

This connects directly to:

- Security
- Performance isolation
- Data isolation
- Compliance

---

# 4. Business Drivers of Cloud Computing

After discussing the challenges, the lecture discusses why organizations still adopt cloud computing.

The benefits are divided into:

1. **Operational benefits**
2. **Financial benefits**

---

# 4.1 Operational Benefits

## 1. Speed to Market

Cloud resources can be provisioned without requiring an organization to build an entire physical infrastructure first.

This can help organizations develop and deploy services faster.

---

## 2. Scalability

Cloud computing can support changing workloads by allowing resources to be added as demand increases.

---

## 3. Agility

Cloud environments can allow organizations to respond quickly to changing business and technical requirements.

For example:

```text
Need new environment
        ↓
Provision resources
        ↓
Test application
        ↓
Deploy
```

---

## 4. Highly Automated

Cloud environments can automate many infrastructure-related activities.

Examples include:

- Provisioning
- Scaling
- Deployment
- Resource allocation

Automation reduces manual infrastructure management.

---

## 5. Flexibility

Organizations can adapt their computing resources and deployment approaches according to their needs.

---

# 4.2 Financial Benefits

## 1. Pay Per Use

Instead of necessarily purchasing all infrastructure upfront, cloud computing can use a usage-based model.

Conceptually:

```text
Traditional:

Buy infrastructure
      ↓
Large upfront investment


Cloud:

Use resources
      ↓
Pay for usage
```

---

## 2. Lower Capital Investment

Organizations may avoid a large initial investment in physical infrastructure.

---

## 3. Reduced Financial Risk

Instead of buying infrastructure for an uncertain future workload, an organization can start with a smaller amount of resources and expand as needed.

---

## 4. Reduced Cost

Cloud computing can potentially reduce infrastructure-related costs depending on workload and usage patterns.

However, cloud is **not automatically cheaper in every situation**. Cost depends on how resources are used.

---

# 5. Inhibitors to Cloud Adoption

If cloud provides so many benefits, why doesn't every organization immediately move everything to the cloud?

The lecture identifies three major inhibitors:

```text
       Cloud Adoption Inhibitors
                 |
       +---------+---------+
       |         |         |
   Security  Compliance  Interoperability
                           +
                       Vendor Lock-in
```

---

## 5.1 Security

Organizations may worry about:

- Data protection
- Unauthorized access
- Shared infrastructure
- Account compromise
- Isolation between customers

Security is especially important for sensitive information.

---

## 5.2 Compliance

Organizations may have regulatory and legal obligations.

They need to ensure that using a cloud provider does not violate those requirements.

---

## 5.3 Interoperability

**Interoperability** is the ability of systems/services to work together.

If an application is designed around one cloud provider, moving it to another provider may be difficult if the services and interfaces are not compatible.

---

# 5.4 Vendor Lock-in

Vendor lock-in occurs when an organization becomes heavily dependent on a particular cloud provider and finds it difficult or expensive to move elsewhere.

Example:

```text
Application
     ↓
Provider A's proprietary services
     ↓
Provider A infrastructure
```

Later:

```text
Want to move to Provider B
             ↓
     APIs may differ
     Services may differ
     Data migration required
     Application changes required
```

This can make migration difficult.

---

## Why vendor lock-in matters

Before selecting a cloud provider, organizations may need to think about:

- Portability
- Standard interfaces
- Data migration
- Application dependencies
- Proprietary services

---

# 6. Cloud Deployment Models

The lecture identifies four deployment models:

1. **Private Cloud**
2. **Community Cloud**
3. **Public Cloud**
4. **Hybrid Cloud**

```text
                  Deployment Models
                         |
       +-----------------+-----------------+
       |        |               |          |
    Private  Community       Public      Hybrid
```

---

# 6.1 Private Cloud

A **private cloud** is built for a **single enterprise/organization**.

```text
              Organization
                   ↓
             Private Cloud
                   ↓
            Organization Users
```

The lecture explicitly describes private clouds as being built for a single enterprise.

### Advantages

Potential advantages include:

- More organizational control
- Greater ability to customize infrastructure
- Stronger control over policies and access

### Costs

The lecture notes that a private cloud requires costs associated with:

- Hardware
- Software
- Services/maintenance

Therefore, the organization bears much of the infrastructure responsibility.

---

# 6.2 Community Cloud

A **community cloud** is infrastructure shared by a community of organizations/users with common requirements.

Conceptually:

```text
Organization A
       \
Organization B → Community Cloud
       /
Organization C
```

The lecture defines it as:

> Infrastructure shared by a community.

---

# 6.3 Public Cloud

A **public cloud** is owned/operated by a service provider and serves customers.

Conceptually:

```text
              Cloud Provider
                    ↓
               Public Cloud
             /       |       \
            ↓        ↓        ↓
       Customer A Customer B Customer C
```

### Advantages

Potential benefits include:

- No need to own all physical infrastructure
- Easier resource expansion
- Pay-for-service model
- Provider-managed infrastructure

### Costs/considerations

The lecture notes:

- Service fees
- WAN/network costs
- Legal issues involving third-party infrastructure

---

# 6.4 Hybrid Cloud

A **hybrid cloud** combines different deployment models.

For example:

```text
          Private Cloud
                ↕
          Hybrid Environment
                ↕
           Public Cloud
```

A company might keep some workloads in a private environment while using public cloud resources for other workloads.

---

# 7. Public vs Private Cloud

This comparison is important for architecture questions.

## Private Cloud

```text
Single Organization
        ↓
Private Infrastructure
        ↓
Organization Users
```

## Public Cloud

```text
Cloud Service Provider
        ↓
Public Infrastructure
        ↓
Multiple Customers
```

---

## Cost comparison

### Private Cloud

The organization needs to account for:

```text
Hardware
+
Software
+
Maintenance / Services
```

### Public Cloud

The organization generally deals with:

```text
Service Fees
+
WAN / Network Costs
```

The lecture also highlights legal considerations involving third-party involvement.

---

## High-level comparison

| Factor | Private Cloud | Public Cloud |
|---|---|---|
| Primary ownership/use | Single organization | Cloud service provider serves customers |
| Control | Higher organizational control | Provider manages infrastructure |
| Infrastructure cost | Hardware + software + maintenance | Service fees + network/WAN costs |
| Sharing | More dedicated | Shared environment |
| Scalability | Organization must provide/manage capacity | Provider can offer large-scale resources |
| Main concerns | Cost and management | Security, compliance, lock-in, dependency |

> **Important:** The lecture does not say that one model is always better. The appropriate choice depends on application requirements.

---

# 8. Applying the Concepts — Architecture Exercises

The lecture contains practical exercises to make you think like a **software architect**.

---

## 8.1 Exercise: 15-Year-Old Successful Product

### Problem

You are the software architect of a very successful product that has been in the market for more than 15 years.

The company wants to move the application to the cloud **ASAP**.

Question:

> Would you use a private cloud or a public cloud?

---

## How to analyze it

There is no universal answer from the slide.

You should evaluate:

### 1. Security requirements

Is the application handling highly sensitive data?

### 2. Compliance requirements

Are there regulatory restrictions?

### 3. Legacy dependencies

A 15-year-old application may contain:

- Old software dependencies
- Legacy databases
- Old deployment assumptions
- Infrastructure-specific code

### 4. Workload

Is the workload:

- Stable?
- Highly variable?
- Extremely large?

### 5. Migration speed

The company wants migration ASAP.

You need to consider which environment can support the migration while satisfying the other requirements.

### Exam-style conclusion

> The choice between public and private cloud should be based on security, compliance, legacy dependencies, workload characteristics, cost, control requirements and migration constraints.

---

# 8.2 BookKarts Architecture Exercise

The lecture introduces a fictional company called **BookKarts**.

The company:

1. Sells books.
2. Accepts orders.
3. Allows customers to select books.
4. Receives payment.
5. Dispatches books to customers.

The exercise asks:

> What components need to be built?

and

> What technologies should be selected for each component?

---

## Possible logical components

A typical system can be decomposed into:

```text
                         Customer
                            ↓
                    Web / Mobile UI
                            ↓
                    Application Layer
                     /      |       \
                    ↓       ↓        ↓
                Users     Books     Orders
                    \       |        /
                     \      |       /
                       Database
                           |
                     Payment System
                           |
                     Shipping System
```

The exact technology choices are part of the exercise.

---

## Component 1 — User Interface

Responsibilities:

- Display books
- Search books
- Login/register
- Shopping cart
- Checkout
- Order status

---

## Component 2 — Application / Backend

Responsibilities:

- User authentication
- Book management
- Order processing
- Business logic
- Inventory-related operations
- Communication with other services

---

## Component 3 — Database

Could contain logical data such as:

```text
Users
Books
Orders
Payments
Inventory
```

---

## Component 4 — Payment

Responsible for processing payments.

Payment-related operations should be handled securely.

---

## Component 5 — Shipping / Dispatch

Responsible for:

- Order fulfillment
- Dispatch
- Delivery-related processing

---

## Component 6 — Infrastructure

The application needs infrastructure to host:

- Frontend
- Backend
- Database
- Supporting services

This is where the cloud deployment decision becomes relevant.

---

# 8.3 Should BookKarts use a cloud?

The lecture asks whether BookKarts should also be stored/deployed on the cloud.

This should be treated as an architecture decision.

For a growing business, cloud infrastructure can be attractive because of:

- Scalability
- Elasticity
- Speed to market
- Pay-per-use
- Reduced upfront infrastructure investment
- Flexibility

However, the architecture must still address:

- Security
- Payment protection
- Availability
- Data management
- Compliance where applicable
- Vendor lock-in

---

# 9. Quick Revision Table

| Concept | Meaning | Main Problem / Goal |
|---|---|---|
| **IaaS** | Infrastructure service | More control over infrastructure |
| **PaaS** | Platform service | Develop applications without managing all infrastructure |
| **SaaS** | Complete software service | Simply use the application |
| **Scalability** | Handle increasing workload | Support growth |
| **Elasticity** | Dynamically add/remove resources | Match resources to demand |
| **Performance unpredictability** | Performance can vary in shared environments | Maintain predictable performance |
| **Reliability** | Ability to operate correctly despite failures | Tolerate failures |
| **Availability** | Service remains accessible | Minimize downtime |
| **Security** | Protect systems and data | Prevent unauthorized access/attacks |
| **Compliance** | Follow legal/regulatory requirements | Meet required rules |
| **Multi-tenancy** | Multiple customers share resources | Share efficiently without compromising isolation |
| **Interoperability** | Systems work together | Make integration/portability easier |
| **Vendor lock-in** | Dependence on one provider | Avoid difficult migration |
| **Private cloud** | Cloud for one enterprise | More control |
| **Community cloud** | Shared by a community | Common requirements |
| **Public cloud** | Provider-owned cloud serving customers | Flexible provider-managed infrastructure |
| **Hybrid cloud** | Combination of deployment models | Combine environments |

---

# 10. Important Exam Questions

## Short-answer questions

### Q1. What are the three cloud service models?

**Answer:**

- IaaS
- PaaS
- SaaS

---

### Q2. What is scalability?

**Answer:**

Scalability is the ability of a system to handle increasing workload by appropriately increasing resources/capacity.

---

### Q3. What is elasticity?

**Answer:**

Elasticity is the ability to dynamically increase and decrease resources according to workload.

---

### Q4. Why does cloud computing have performance unpredictability?

**Answer:**

Cloud resources may be shared among multiple users or workloads. Resource contention can cause variations in application performance, creating a need for performance isolation.

---

### Q5. What factors affect cloud reliability and availability?

**Answer:**

The lecture highlights:

- High number of components
- System complexity

Failures in complex systems can affect service availability.

---

### Q6. What is multi-tenancy?

**Answer:**

Multi-tenancy is the sharing of cloud resources/infrastructure, including potentially the same database, among multiple users while maintaining security and isolation.

---

### Q7. What are the major inhibitors to cloud adoption?

**Answer:**

- Security
- Compliance
- Interoperability
- Vendor lock-in

---

### Q8. What are the four deployment models?

**Answer:**

- Private cloud
- Community cloud
- Public cloud
- Hybrid cloud

---

### Q9. What is vendor lock-in?

**Answer:**

Vendor lock-in occurs when an organization becomes dependent on a cloud provider's services and moving to another provider becomes difficult or expensive.

---

### Q10. What are the major benefits of cloud computing?

**Answer:**

### Operational

- Speed to market
- Scalability
- Agility
- Automation
- Flexibility

### Financial

- Pay per use
- Lower capital investment
- Reduced financial risk
- Reduced cost

---

# 11. One-Page Mental Model

Remember the lecture using this chain:

```text
                    CLOUD COMPUTING
                           |
             +-------------+-------------+
             |                           |
       SERVICE MODELS              DEPLOYMENT MODELS
             |                           |
      +------+------+             +------+------+------+
      |      |      |             |      |      |      |
    IaaS   PaaS   SaaS         Private Public Community Hybrid
      |
      ↓
 WHY CLOUD?
      |
      +------------------------------+
      |                              |
 Operational                    Financial
      |                              |
 Speed to market                 Pay per use
 Scalability                     Lower capital
 Agility                         Lower risk
 Automation                      Reduced cost
 Flexibility
      |
      ↓
 BUT THERE ARE CHALLENGES
      |
 +----+-------+----------+-------------+
 |    |       |          |             |
Scale Elasticity Performance Reliability
                    |
                    ↓
                 Security
                    |
                    ↓
                Compliance
                    |
                    ↓
              Multi-tenancy
                    |
                    ↓
          Adoption Concerns
                    |
          +---------+----------+
          |         |          |
       Security Compliance Vendor
                              Lock-in
                              +
                         Interoperability
```

---

# Final Takeaways

If you remember only the most important points from Lecture 2, remember these:

1. **Cloud service models:** IaaS, PaaS, SaaS.
2. **Scalability:** Handle more workload.
3. **Elasticity:** Dynamically add/remove resources according to workload.
4. **Performance unpredictability:** Shared resources can affect performance.
5. **Reliability & availability:** Large, complex systems must tolerate failures.
6. **Security:** Protect data, users, applications and infrastructure.
7. **Compliance:** Follow legal, regulatory and industry requirements.
8. **Multi-tenancy:** Share infrastructure while preserving isolation.
9. **Cloud benefits:** Speed, scalability, agility, automation, flexibility and financial advantages.
10. **Cloud adoption inhibitors:** Security, compliance, interoperability and vendor lock-in.
11. **Deployment models:** Private, community, public and hybrid.
12. **Architecture decisions:** There is no universally best cloud model; choose based on workload, security, compliance, cost, control and application requirements.

