# Lecture 4 — PaaS Programming Model

> **Simple, detailed notes based on Lecture 4: PaaS Programming Model by K. V. Subramaniam.**
>
> **Main idea:** PaaS (Platform as a Service) lets developers focus on their application while the platform handles much of the underlying infrastructure, deployment, resource allocation, scaling, and cloud-service integration.

---

## Table of Contents

1. [Motivational Example: Photo Sharing App](#1-motivational-example-photo-sharing-app)
2. [Three-Tier Application Model](#2-three-tier-application-model)
3. [Deploying the App Using IaaS](#3-deploying-the-app-using-iaas)
4. [Why Do We Need PaaS?](#4-why-do-we-need-paas)
5. [PaaS Programming Model](#5-paas-programming-model)
6. [Application Components](#6-application-components)
7. [Web Roles and Worker Roles](#7-web-roles-and-worker-roles)
8. [Synchronous vs Asynchronous Processing](#8-synchronous-vs-asynchronous-processing)
9. [Load Balancing](#9-load-balancing)
10. [Scaling](#10-scaling)
11. [Service Monitoring and Probes](#11-service-monitoring-and-probes)
12. [Public Endpoints](#12-public-endpoints)
13. [Traffic Manager and Application Gateway](#13-traffic-manager-and-application-gateway)
14. [How to Build PaaS on Top of IaaS](#14-how-to-build-paas-on-top-of-iaas)
15. [Cloud Controller](#15-cloud-controller)
16. [Common Layered PaaS Architecture](#16-common-layered-paas-architecture)
17. [Why Storage Services Are Important](#17-why-storage-services-are-important)
18. [Windows Azure Application Architecture](#18-windows-azure-application-architecture)
19. [Azure Blob Storage](#19-azure-blob-storage)
20. [Azure Table Service](#20-azure-table-service)
21. [SQL Azure](#21-sql-azure)
22. [Complete Photo-Sharing Architecture](#22-complete-photo-sharing-architecture)
23. [IaaS vs PaaS](#23-iaas-vs-paas)
24. [Important Definitions](#24-important-definitions)
25. [Exam Revision Sheet](#25-exam-revision-sheet)

---

# 1. Motivational Example: Photo Sharing App

Imagine that we want to build a photo-sharing application.

A user should be able to:

1. Upload a photo.
2. Have the application recognize faces in the photo.
3. Store the photo.
4. Store information about the faces detected in the photo.

The lecture uses this example to introduce the programming model and deployment problem.

### Basic flow

```text
User
 |
 | Upload photo
 v
Front End
 |
 v
Application Logic
 |
 | Face recognition
 v
Database
 |
 | Store photo + face information
 v
Storage
```

The important question is:

> **How do we divide the application into different parts, and how do we deploy those parts in the cloud?**

---

# 2. Three-Tier Application Model

A common way to organize an application is the **3-tier architecture**.

```text
+----------------------+
|       Front End      |
|        / GUI         |
+----------------------+
           |
           v
+----------------------+
|    Application Logic |
+----------------------+
           |
           v
+----------------------+
|       Database       |
+----------------------+
```

## Tier 1 — Front End

The front end is what the user interacts with.

Examples:

- Web pages
- Buttons
- Forms
- Upload interfaces
- Results displayed to the user

For our application:

```text
[ Choose Photo ]
[    Upload    ]
```

The front end sends the user's request to the application.

---

## Tier 2 — Application Logic

This is the part that performs the actual work.

For our photo application:

```text
Photo uploaded
      |
      v
Application receives photo
      |
      v
Face recognition
      |
      v
Faces detected
```

This is the **brain of the application**.

---

## Tier 3 — Database

The database stores information required by the application.

For example:

```text
Photo ID: 123

Photo: vacation.jpg

Faces:
- Person A
- Person B
- Person C
```

So the basic 3-tier model is:

```text
User
 |
 v
Front End
 |
 v
Application Logic
 |
 v
Database
```

---

# 3. Deploying the App Using IaaS

Before understanding PaaS, understand what happens with **IaaS**.

IaaS gives us infrastructure such as virtual machines, storage, and networking.

But we still have to manage much of the software environment ourselves.

The lecture gives the following deployment steps.

## Step 1 — Start a VM

Create a virtual machine.

A VM (Virtual Machine) is essentially a computer provided through the cloud.

```text
Cloud
  |
  v
Virtual Machine
```

---

## Step 2 — Connect Block Storage

Attach storage to the VM.

We need storage for things such as:

- Application data
- Files
- Photos
- Other persistent data

---

## Step 3 — Install a Web Server

Install a web server so that the application can receive web requests.

Conceptually:

```text
Browser
   |
   | HTTP/HTTPS
   v
Web Server
```

---

## Step 4 — Install an Application Server

Install the environment needed to run the application logic.

---

## Step 5 — Install a Database

Install and configure the database.

---

## Step 6 — Upload the Application

Put our application code on the VM.

---

## Step 7 — Run the Application

Finally, start the application.

So the IaaS process looks like:

```text
Create VM
   |
   v
Attach Storage
   |
   v
Install Web Server
   |
   v
Install Application Server
   |
   v
Install Database
   |
   v
Upload Application
   |
   v
Run Application
```

### The problem

There is a lot of infrastructure work.

The developer has to worry about:

- VMs
- Servers
- Installation
- Configuration
- Storage
- Deployment
- Scaling
- Infrastructure management

This motivates PaaS.

---

# 4. Why Do We Need PaaS?

The lecture asks an important question:

> If mundane tasks such as installing web and database servers are taken care of by PaaS, do we simply upload the application to the cloud controller?

The answer is **not simply "yes."**

The PaaS platform still needs to understand the structure of the application.

For example:

```text
Application
 |
 +-- Front End
 |
 +-- Application Logic
 |
 +-- Background Processing
 |
 +-- Database
```

Different parts may need different environments.

For example:

```text
Front End
   |
   v
Web environment

Background task
   |
   v
Worker environment

Data
   |
   v
Database / Storage service
```

Therefore, PaaS needs a **programming/deployment model** that tells it:

- What components exist?
- What environment does each component need?
- How many instances should run?
- When should the application scale up?
- When should it scale down?
- What cloud services are required?

---

# 5. PaaS Programming Model

The central idea is:

> **An application is made up of components, and each component describes how it should be deployed and managed.**

The lecture states that each component specifies its environment, such as whether it should run under a web server or application server.

It can also specify:

- Initial number of components/instances
- Scale-up policy
- Scale-down policy

Conceptually:

```text
Application
 |
 +------------------+
 |                  |
 v                  v
Component 1      Component 2
 |
 | Environment
 | Number of instances
 | Scaling policy
 v
PaaS Platform
```

This gives the PaaS platform enough information to automatically deploy and manage the application.

---

# 6. Application Components

Instead of thinking of an application as one huge program, think of it as several components.

For the photo-sharing example:

```text
Photo Sharing Application
 |
 +--------------------------+
 |                          |
 v                          v
Web Component          Processing Component
 |                          |
 v                          v
Front End              Face Recognition
```

The application can also use storage services:

```text
                Application
                    |
          +---------+---------+
          |                   |
          v                   v
      Web Role          Worker Role
          |                   |
          |                   v
          |             Face Recognition
          |                   |
          +---------+---------+
                    |
                    v
             Storage Services
```

### Why components?

Because different components have different requirements.

For example:

| Component | Purpose | Environment |
|---|---|---|
| Web component | Receive user requests | Web server |
| Worker component | Background processing | Worker/process environment |
| Database | Store structured data | Database service |
| Blob storage | Store files/photos | Storage service |

---

# 7. Web Roles and Worker Roles

The lecture uses **Windows Azure** as an example of a PaaS platform.

Two important concepts are:

```text
Web Role
Worker Role
```

---

## 7.1 Web Role

A **Web Role** is intended for front-end tasks.

It:

- Accepts HTTP/HTTPS requests.
- Acts as the front end of an application.
- Runs under IIS (Microsoft's web server).

Basic flow:

```text
User
 |
 | HTTP/HTTPS request
 v
Web Role
 |
 v
Application
```

Think of a Web Role as the **receptionist**.

The user talks directly to it.

---

## 7.2 Worker Role

A **Worker Role** is intended for back-end processing.

It is useful for:

- Background tasks
- Long-running operations
- Asynchronous operations

Basic flow:

```text
Web Role
   |
   | Give task
   v
Worker Role
   |
   v
Process task
```

Think of a Worker Role as the **employee working behind the scenes**.

---

# 8. Synchronous vs Asynchronous Processing

This concept explains why Worker Roles are useful.

## 8.1 Synchronous

Suppose a user uploads a photo.

The web server receives it and immediately performs face recognition.

Imagine face recognition takes a long time:

```text
User
 |
 v
Web Role
 |
 v
Face Recognition
 |
 | wait...
 | wait...
 | wait...
 v
Response
```

The user has to wait.

This can cause:

- Timeouts
- The application appearing to hang
- The user cancelling the request

The lecture specifically identifies these problems with long-running operations in a Web Role.

---

## 8.2 Asynchronous

Instead, the Web Role can hand the task to a Worker Role.

```text
User
 |
 v
Web Role
 |
 | Submit background task
 v
Worker Role
 |
 v
Face Recognition
```

The web request does not need to remain active while the long operation runs.

This is better for long-running work.

### Easy way to remember

```text
Synchronous:
"Wait here until I finish."

Asynchronous:
"I'll give the task to someone else and continue."
```

---

# 9. Load Balancing

In a real application, one Web Role may not be enough.

Suppose thousands of users access our application.

We can have multiple Web Role instances:

```text
          Users
            |
            v
     +--------------+
     | Load Balancer|
     +--------------+
       /     |     \
      /      |      \
     v       v       v
  Web 1    Web 2    Web 3
```

The **load balancer** distributes incoming traffic among the Web Roles.

---

## Why do we need it?

Without load balancing:

```text
1000 users
    |
    v
 Web 1
  💥
```

With load balancing:

```text
1000 users
     |
     v
Load Balancer
 /     |      \
v      v       v
Web1  Web2    Web3
```

The work is distributed.

---

# 10. Scaling

Applications do not always have the same amount of traffic.

Sometimes there are few users.

Sometimes there are millions.

PaaS can manage this using scaling.

---

## 10.1 Scale Up

Suppose we initially have:

```text
Web1
Web2
```

Traffic becomes very high.

The platform can add more instances:

```text
Web1
Web2
Web3
Web4
```

This is **scaling up**.

In this context, scaling means increasing the number of application instances/resources.

---

## 10.2 Scale Down

Later, traffic decreases.

We don't need all four instances.

The platform can reduce them:

```text
Web1
Web2
```

This is **scaling down**.

It avoids keeping unnecessary resources running.

---

## Why must the load balancer know about scaling?

Suppose:

```text
Web1
Web2
Web3
Web4
```

Then Web4 is removed.

If the load balancer doesn't know this:

```text
User
 |
 v
Load Balancer
 |
 v
Web4  <-- no longer running
```

That would cause problems.

Therefore, the platform needs monitoring and coordination between scaling and load balancing.

---

# 11. Service Monitoring and Probes

The platform needs to know whether an application instance is healthy.

A **probe** is essentially a health check.

The system asks:

> "Is this Web Role alive and responding?"

Conceptually:

```text
Load Balancer
     |
     +----> Web1: Healthy
     |
     +----> Web2: Healthy
     |
     +----> Web3: Not healthy
```

Traffic should go to healthy instances.

The lecture mentions probe mechanisms including:

- HTTP
- TCP
- Guest-agent/API-based checks

---

# 12. Public Endpoints

A **public endpoint** is a point through which external users can reach an application.

Think:

```text
Internet
   |
   v
Public Endpoint
   |
   v
Load Balancer
   |
   v
Web Roles
```

Public endpoints listen for external requests.

For example, when a user opens a web application, the request needs an externally reachable endpoint.

---

# 13. Traffic Manager and Application Gateway

The lecture introduces two related components.

---

## 13.1 Traffic Manager

The basic idea presented in the lecture is:

```text
User
 |
 | DNS request
 v
Traffic Manager
 |
 | Gives IP address
 v
Gateway
 |
 v
Application
```

Traffic Manager is involved in directing the user toward an appropriate endpoint/gateway through DNS.

---

## 13.2 Application Gateway

The lecture describes Application Gateway as supporting HTTP(S) load balancing.

It can:

- Perform round-robin load balancing.
- Load balance internal requests as well.

---

## 13.3 Round Robin

Round robin means distributing requests in a repeating sequence.

Suppose we have:

```text
Web1
Web2
Web3
```

Requests could be distributed like:

```text
Request 1 → Web1
Request 2 → Web2
Request 3 → Web3
Request 4 → Web1
Request 5 → Web2
Request 6 → Web3
```

It simply goes around in a circle.

---

# 14. How to Build PaaS on Top of IaaS

This is one of the most important sections of the lecture.

The basic architecture is:

```text
             Developer
                 |
                 v
        +------------------+
        |       PaaS       |
        |                  |
        | Cloud Controller |
        +------------------+
                 |
                 v
                IaaS
                 |
        +--------+--------+
        |        |        |
        v        v        v
       VM       VM       VM
```

The idea is:

> **PaaS can be implemented on top of IaaS.**

IaaS provides the underlying infrastructure.

PaaS adds a management/programming layer on top.

---

# 15. Cloud Controller

The **Cloud Controller** is a key part of the PaaS layer.

It automatically manages infrastructure resources required by applications.

According to the lecture, the Cloud Controller can:

- Automatically create VMs and containers.
- Deploy applications.
- Connect applications to cloud services.
- Automatically scale applications up or down.

---

## What does the developer provide?

The developer can provide:

### 1. Application

```text
Application code
```

### 2. Software specification

For example:

```text
Operating System
Web Server
Application environment
```

### 3. Required cloud services

For example:

```text
Database
Load Balancing
Storage
```

Conceptually:

```text
Developer
    |
    | Application
    | Software requirements
    | Required services
    v
Cloud Controller
    |
    +---- Create VM/container
    |
    +---- Deploy application
    |
    +---- Connect services
    |
    +---- Scale
    |
    v
Cloud Infrastructure
```

---

# 16. Common Layered PaaS Architecture

The lecture gives a layered architecture:

```text
+-----------------------------------+
|           Applications            |
+-----------------------------------+
|              PaaS                 |
|                                   |
|        Cloud Controller           |
|                                   |
|  Deployment                       |
|  Resource Allocation              |
|  Scaling                          |
|  Service Connections              |
+-----------------------------------+
|               IaaS                |
|                                   |
|      VMs / Containers              |
|      Storage Services              |
+-----------------------------------+
```

The PaaS layer sits between applications and the underlying IaaS.

---

## Cloud Controller responsibilities

### 1. Automatically create VMs/containers

```text
Application requirements
        |
        v
Cloud Controller
        |
        v
VMs / Containers
```

### 2. Deploy applications

The controller puts the appropriate components into the required environments.

### 3. Connect to services

For example:

```text
Application
     |
     +---- Database
     |
     +---- Storage
     |
     +---- Queue
```

### 4. Automatically scale

```text
High demand
    |
    v
More instances

Low demand
    |
    v
Fewer instances
```

---

# 17. Why Storage Services Are Important

One very important idea is that applications should store their persistent state in separate storage services.

Imagine:

```text
Web1
 |
 +--- User data
```

If Web1 crashes:

```text
Web1 💥
```

The data could be lost if it existed only inside that instance.

Instead:

```text
Web1 ----\
Web2 -----+----> Storage Service
Web3 ----/
```

Now the application instances can come and go while the important data remains in storage.

The lecture points out two major benefits:

1. **Applications are simpler to scale.**
2. **Recovery from failure is easier.**

---

# 18. Windows Azure Application Architecture

The lecture presents Windows Azure architecture using several major parts.

```text
Windows Azure Application
 |
 +----------------------+
 |                      |
 v                      v
Compute             Communication
 |                      |
 v                      v
Processes          Message Queues
 |
 v
Web Roles
Worker Roles

            +

          Storage
             |
       +-----+-----+------+
       |           |      |
      SQL         NoSQL   Blob
```

---

## Compute

The application runs as processes.

The lecture identifies:

- Web roles
- Worker roles

---

## Communication

The architecture can use **message queues**.

The lecture emphasizes that message delivery can be guaranteed **eventually**, even if:

- The receiver is temporarily down.
- The network is temporarily unavailable.

Conceptually:

```text
Web Role
   |
   | Message
   v
Queue
   |
   | Later
   v
Worker Role
```

This is very useful for asynchronous processing.

---

## Storage

The lecture mentions:

- Database services
- Blob services
- SQL
- NoSQL

---

# 19. Azure Blob Storage

A **Blob** is used for storing binary or text data, such as files.

Examples:

```text
photo.jpg
video.mp4
document.pdf
```

For the photo-sharing application, Blob Storage is a natural place to store photos.

```text
Uploaded Photo
      |
      v
Blob Storage
```

---

## Two types of blobs

The lecture identifies:

### Page Blob

Optimized for:

> **Random access**

### Block Blob

Optimized for:

> **Sequential access**

Easy memory trick:

```text
Page Blob  → Random
Block Blob → Sequential
```

---

## Containers

Blobs are stored inside containers.

Think of a container as a logical collection:

```text
Container: photos
 |
 +-- photo1.jpg
 +-- photo2.jpg
 +-- photo3.jpg
```

The lecture also notes that blobs can have associated **metadata**, represented as key-value pairs.

For example:

```text
File: photo1.jpg

Metadata:
location = Bangalore
type = vacation
owner = user123
```

---

# 20. Azure Table Service

Azure Table Service is described in the lecture as a **NoSQL database**.

It organizes data using keys.

The two important keys are:

```text
Partition Key
Row Key
```

Together:

```text
<Partition Key, Row Key>
```

uniquely identify a row/entity.

---

# 21. Partition Key

The **Partition Key** determines which partition contains an entity.

For example:

```text
Partition Key = India
```

could place several related rows in the same partition.

Conceptually:

```text
Partition: India
 |
 +-- Row A
 +-- Row B
 +-- Row C

Partition: USA
 |
 +-- Row A
 +-- Row B
 +-- Row C
```

The lecture states that rows with the same partition key are stored in the same partition.

---

# 22. Row Key

The **Row Key** identifies a specific row inside a partition.

For example:

```text
Partition Key = India
Row Key       = 101
```

Another:

```text
Partition Key = India
Row Key       = 102
```

Together:

```text
<India, 101>
<India, 102>
```

identify different rows.

### Important

```text
Partition Key
      +
Row Key
      =
Unique row/entity
```

---

# 23. SQL Azure

SQL Azure is described in the lecture as a **relational database service** based on SQL Server.

So:

```text
SQL Azure
   |
   v
Relational Database
   |
   +-- Tables
   +-- Rows
   +-- Columns
   +-- SQL
```

It allows applications to use .NET APIs in a similar way to non-cloud applications, as described in the lecture.

---

# 24. Complete Photo-Sharing Architecture

Now let's combine everything.

Suppose the user uploads a photo.

## Step 1 — User sends request

```text
User
 |
 | HTTP/HTTPS
 v
Public Endpoint
```

---

## Step 2 — Load balancer chooses a Web Role

```text
Public Endpoint
       |
       v
Load Balancer
    /   |   \
   v    v    v
 Web1 Web2 Web3
```

---

## Step 3 — Web Role receives upload

```text
User
 |
 v
Web Role
 |
 v
Receive Photo
```

---

## Step 4 — Store photo

The photo can be stored in Blob Storage.

```text
Web Role
   |
   v
Blob Storage
   |
   v
photo.jpg
```

---

## Step 5 — Submit background task

Instead of performing expensive face recognition during the user's web request:

```text
Web Role
   |
   v
Message Queue
```

---

## Step 6 — Worker Role processes it

```text
Message Queue
      |
      v
Worker Role
      |
      v
Face Recognition
```

---

## Step 7 — Store results

The detected face information can be stored using an appropriate database/storage service.

```text
Worker Role
    |
    +----> SQL / relational storage
    |
    +----> NoSQL / Table service
```

---

## Complete picture

```text
                         USER
                           |
                           | HTTP/HTTPS
                           v
                  +------------------+
                  | Public Endpoint  |
                  +------------------+
                           |
                           v
                  +------------------+
                  |  Load Balancer   |
                  +------------------+
                    /       |       \
                   v        v        v
                Web 1     Web 2    Web 3
                   \        |       /
                    \       |      /
                     +------v-----+
                            |
                            v
                       Message Queue
                            |
                            v
                    +---------------+
                    |  Worker Role  |
                    +---------------+
                            |
                            v
                     Face Recognition
                            |
                  +---------+---------+
                  |                   |
                  v                   v
             Blob Storage         Database
               Photos          Face Information
```

---

# 25. IaaS vs PaaS

This is extremely important.

## IaaS

With IaaS, you get infrastructure.

```text
IaaS
 |
 +-- VM
 +-- Storage
 +-- Networking
```

You are responsible for more of the software stack.

For example:

```text
IaaS
 ↓
VM
 ↓
Install OS/configuration
 ↓
Install Web Server
 ↓
Install App Server
 ↓
Install Database
 ↓
Deploy Application
```

---

## PaaS

With PaaS, you provide your application and its requirements.

The platform handles much of the infrastructure management.

```text
PaaS
 |
 +-- Deployment
 +-- Resource allocation
 +-- Scaling
 +-- Load balancing
 +-- Cloud service integration
 |
 v
IaaS
```

### Easy comparison

| IaaS | PaaS |
|---|---|
| Gives infrastructure | Gives a platform |
| More infrastructure management | Less infrastructure management |
| You manage VMs more directly | Platform manages resources |
| You install/configure more software | Platform provides managed environments |
| More control | More abstraction |
| Developer handles more operational work | Developer focuses more on application |

---

# 26. Important Definitions

## PaaS

**Platform as a Service** is a cloud model that provides a platform for deploying and running applications while hiding much of the underlying infrastructure management.

---

## Application Component

A distinct part of an application that specifies its required environment and deployment/scaling characteristics.

---

## Web Role

A front-end process intended to handle HTTP/HTTPS requests.

In the Windows Azure example, it runs under IIS.

---

## Worker Role

A back-end process intended for long-running or asynchronous operations.

---

## Load Balancer

A component that distributes incoming traffic among multiple application instances.

---

## Scale Up

Increasing application resources/instances when demand increases.

Example:

```text
2 instances
    ↓
4 instances
```

---

## Scale Down

Reducing application resources/instances when demand decreases.

Example:

```text
4 instances
    ↓
2 instances
```

---

## Probe

A health check used to determine whether a service/application instance is available and healthy.

---

## Public Endpoint

An externally reachable endpoint through which users can access an application.

---

## Cloud Controller

A PaaS management component that can:

- Create VMs/containers
- Deploy applications
- Connect services
- Allocate resources
- Scale applications

---

## Blob Storage

Storage for binary or text objects such as photos and files.

---

## Page Blob

Blob type optimized for random access.

---

## Block Blob

Blob type optimized for sequential access.

---

## Table Service

A NoSQL storage service using partition keys and row keys.

---

## Partition Key

Determines the partition in which an entity is stored.

---

## Row Key

Identifies a specific row/entity within a partition.

---

## SQL Azure

A relational database service based on SQL Server.

---

# 27. Exam Revision Sheet

## The most important architecture

```text
                 APPLICATION
                      |
             +--------+--------+
             |                 |
             v                 v
          Web Role         Worker Role
         Front-end         Background
             |                 |
             |                 v
             |          Long-running work
             |                 |
             +--------+--------+
                      |
                      v
               Cloud Services
              /      |       \
           Blob     NoSQL     SQL
                      |
                      v
                    PaaS
                      |
                      v
                    IaaS
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
         VM          VM          VM
```

---

# 28. The PaaS Workflow

Remember this sequence:

```text
Developer
    |
    | Application + requirements
    v
PaaS
    |
    v
Cloud Controller
    |
    +---- Create VMs/containers
    |
    +---- Deploy components
    |
    +---- Connect cloud services
    |
    +---- Allocate resources
    |
    +---- Scale up/down
    |
    v
Running Application
```

---

# 29. Web Role vs Worker Role

| Web Role | Worker Role |
|---|---|
| Front end | Back end |
| Accepts HTTP/HTTPS requests | Performs background work |
| User-facing | Usually not directly user-facing |
| Runs under web server/IIS in the Azure example | Used for long-running/asynchronous operations |
| Should respond to requests efficiently | Can continue processing after a task is submitted |

### Memory trick

```text
WEB = What the user talks to

WORKER = What works behind the scenes
```

---

# 30. Storage: Blob vs Table vs SQL

| Storage | Main purpose |
|---|---|
| Blob | Files / binary / text objects |
| Table Service | NoSQL entities |
| SQL Azure | Relational data |
| Block Storage | Block-level storage |

### Memory trick

```text
Photo/video/file → Blob

NoSQL entity → Table

Relational data → SQL
```

---

# 31. Scaling + Load Balancing

Remember:

```text
Traffic increases
       |
       v
Scale up
       |
       v
More Web Role instances
       |
       v
Load Balancer distributes requests
```

When traffic decreases:

```text
Traffic decreases
       |
       v
Scale down
       |
       v
Fewer Web Role instances
```

The load balancer must be aware of which instances are available and healthy.

---

# 32. Why Worker Roles?

This is a common conceptual question.

### Question:

**Why not perform long-running operations directly in the Web Role?**

### Answer:

Because a Web Role is intended to handle user requests. Long-running operations can:

- Keep the request waiting.
- Cause timeouts.
- Make the application appear to hang.
- Cause users to cancel requests.

Therefore:

```text
Web Role
   ↓
Submit task
   ↓
Worker Role
   ↓
Long-running processing
```

---

# 33. Why Store Application State Separately?

Suppose:

```text
Web1
Web2
Web3
```

If important state is stored inside Web1:

```text
Web1
 └── Important data
```

and Web1 fails:

```text
Web1 💥
```

the application can have problems.

Instead:

```text
Web1 ----\
Web2 -----+----> External Storage
Web3 ----/
```

Now Web instances can be replaced or scaled without losing persistent application data.

This makes:

- Scaling easier.
- Failure recovery easier.

---

# 34. The Core Idea of the Entire Lecture

Everything in this lecture can be reduced to one idea:

> **PaaS provides a higher-level programming and deployment model so that developers can describe their application and required services without manually managing all the underlying infrastructure.**

The PaaS layer sits above IaaS:

```text
Developer
    |
    v
Application
    |
    v
PaaS
    |
    +-- Components
    +-- Web/Worker environments
    +-- Deployment
    +-- Scaling
    +-- Load balancing
    +-- Cloud services
    |
    v
IaaS
    |
    +-- VMs
    +-- Containers
    +-- Storage
```

---

# 35. 10 Things You MUST Remember

If you are short on time before an exam, remember these ten points:

1. **PaaS can be built on top of IaaS.**

2. **PaaS hides much of the infrastructure management from the developer.**

3. **Applications are represented as components.**

4. **Each component specifies its required environment and scaling behavior.**

5. **Web Role → front-end and HTTP/HTTPS requests.**

6. **Worker Role → background, asynchronous, and long-running operations.**

7. **Load Balancer → distributes traffic across application instances.**

8. **Scaling → add/remove application instances according to demand.**

9. **Storage services keep persistent application state separate from individual application instances.**

10. **Azure example storage includes Blob, Table/NoSQL, and SQL services.**

---

# 36. One-Minute Final Revision

```text
IaaS
 ↓
Provides infrastructure such as VMs and storage.
 ↓
PaaS is built on top of it.
 ↓
Developer gives application + requirements.
 ↓
Cloud Controller manages deployment.
 ↓
Application is divided into components.
 ↓
Web Role handles user/web requests.
 ↓
Worker Role handles background/long-running work.
 ↓
Queues can connect asynchronous tasks.
 ↓
Load Balancer distributes traffic.
 ↓
PaaS scales instances up/down.
 ↓
Probes check service health.
 ↓
Storage services keep persistent state.
 ↓
Blob → files/photos
Table → NoSQL
SQL → relational database
```

## Final mental model

> **IaaS = "Here is a computer/infrastructure. You manage it."**

> **PaaS = "Here is a platform. Give me your application and requirements; I'll manage much of the infrastructure needed to run it."**

That distinction is the foundation for understanding the entire lecture.
