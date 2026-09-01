# Lecture 1 — Introduction to Cloud Computing

## 1. What is Cloud Computing?

Cloud computing is a model in which computing resources are provided over a network and can be obtained **on demand**.

Instead of buying and managing your own physical servers, you can use resources provided by a cloud infrastructure.

> **Core idea:** Computing can be treated more like a utility — use resources when needed and release them when they are no longer needed.

---

## 2. Why Cloud Computing?

The growth of the Internet and Web 2.0 created:

- More users
- More applications
- Huge amounts of data
- Greater computation requirements

Applications such as Google and Facebook require large-scale computing infrastructure.

Cloud computing allows computation to happen **"somewhere in the cloud"**, without the user needing to know or manage a particular physical server.

This connects closely with:

**Web → Big Data → Cloud Computing → AI**

---

## 3. Cloud as a Utility

The lecture uses the analogy of the **electricity grid**.

You do not normally build your own power plant. You obtain electricity from a shared grid and pay for its use.

Cloud computing follows a similar idea:

```text
Electricity:
Power Grid → User

Cloud:
Cloud Infrastructure → User
```

The goal is to make computing resources available in a similar utility-like manner.

---

## 4. NIST Definition

> Cloud computing is a model for enabling ubiquitous, convenient, on-demand network access to a shared pool of configurable computing resources that can be rapidly provisioned and released with minimal management effort or service-provider interaction.

The important words are:

- **On-demand** — obtain resources when needed
- **Network access** — resources are accessed over a network
- **Resource pooling** — resources are shared among consumers
- **Rapid provisioning/release** — resources can be quickly created and removed
- **Minimal management** — the provider handles much of the underlying infrastructure

---

## 5. Five Essential Characteristics

### 1. On-Demand Self-Service

Users can provision computing resources automatically when required without directly interacting with the service provider.

### 2. Broad Network Access

Cloud services are accessible through a network from different devices such as laptops, phones and tablets.

### 3. Resource Pooling

The provider maintains a shared pool of computing resources that can be dynamically assigned to different consumers.

This supports **multi-tenancy**.

### 4. Rapid Elasticity

Resources can be rapidly increased or decreased according to demand.

```text
Low demand  →  Fewer resources
High demand →  More resources
```

### 5. Measured Service

Cloud systems measure resource usage through metering, allowing resource consumption to be monitored and optimized.

---

# 6. Cloud Service Models

The three major service models are:

```text
IaaS → PaaS → SaaS
More control     Less control
More management  Less management
```

## IaaS — Infrastructure as a Service

You obtain fundamental computing infrastructure such as:

- Compute
- Storage
- Networking
- Virtual machines

You have significant control over the operating system, software and applications.

**Think:** *"Give me a machine; I will manage the software."*

### You manage
- OS
- Runtime/software
- Applications
- Configuration

### Provider manages
- Physical servers
- Physical storage
- Physical networking
- Underlying infrastructure

**Example from the lecture:** Amazon AWS / virtual machines.

---

## PaaS — Platform as a Service

The provider gives you a platform on which you can deploy your application.

You do not need to manage as much of the underlying system.

**Think:** *"Give me a platform; I will build and deploy my application."*

### You mainly manage
- Application
- Application data
- Your code

### Provider manages more of
- Infrastructure
- OS
- Runtime/platform

**Examples from the lecture:** Windows Azure, Docker.

---

## SaaS — Software as a Service

The provider runs the application and you simply use it.

**Think:** *"I just want to use the software."*

You generally access it through a client such as a web browser.

**Example from the lecture:** Salesforce.

---

## 7. IaaS vs PaaS vs SaaS

| | IaaS | PaaS | SaaS |
|---|---|---|---|
| Main idea | Rent infrastructure | Rent a platform | Use software |
| Control | High | Medium | Low |
| You manage | OS + software + application | Application | Mostly just usage/configuration |
| Best suited for | Users needing infrastructure control | Developers | End users |

### Easy memory trick

**IaaS:** *Infrastructure*  
**PaaS:** *Platform*  
**SaaS:** *Software*

Or:

> **IaaS = I manage the machine**  
> **PaaS = I manage my application**  
> **SaaS = I use the application**

---

# 8. Cloud Technology Topics

The course introduces technologies and challenges such as:

- Virtualization
- Containers
- Scaling compute
- Scaling storage
- Kubernetes
- OpenStack
- Consistency
- Alternatives to relational databases
- Security
- Multi-tenancy

---

# 9. Exam Quick Revision

### Cloud Computing
On-demand access to a shared pool of configurable computing resources.

### Five characteristics
1. On-demand self-service
2. Broad network access
3. Resource pooling
4. Rapid elasticity
5. Measured service

### Service models
- **IaaS** → Infrastructure
- **PaaS** → Platform
- **SaaS** → Software

### Main relationship

```text
             More control
                  ↑
                IaaS
                  ↓
                PaaS
                  ↓
                SaaS
                  ↓
             Less control
```

### Main motivation

```text
Internet + Web 2.0
        ↓
More users + more data
        ↓
Large-scale computation
        ↓
Large data centers
        ↓
Cloud Computing
        ↓
Big Data / AI applications
```

**Key idea to remember:**  
> Cloud computing abstracts away physical infrastructure and makes computing resources scalable, accessible and available on demand.
