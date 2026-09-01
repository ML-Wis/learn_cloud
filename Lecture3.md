# Lecture 3 --- IaaS Programming Models

> **Topic:** Infrastructure as a Service (IaaS), virtualization, VMs,
> containers, storage, virtual networking, cloud management, OpenStack,
> REST APIs, and Service-Oriented Architecture (SOA)

------------------------------------------------------------------------

## 1. What is Cloud Computing?

Before understanding IaaS, first understand the basic idea of **cloud
computing**.

Normally, if you want to run an application, you need your own
computer/server:

``` text
Application
    ↓
Operating System
    ↓
CPU + RAM + Storage + Network
    ↓
Physical Computer
```

In cloud computing, instead of buying and managing all the physical
hardware yourself, computing resources can be provided to you as a
service.

For this lecture, the important cloud model is:

> **IaaS --- Infrastructure as a Service**

------------------------------------------------------------------------

# 2. Why Do We Need IaaS?

## The Old Model

The traditional approach was:

> **One application → One physical server**

For example:

``` text
Physical Server 1
├── CPU
├── Memory
├── Storage
├── Network
├── Operating System
└── Application A
```

Another application might use another physical server:

``` text
Physical Server 2
├── CPU
├── Memory
├── Storage
├── Network
├── Operating System
└── Application B
```

### Problem: Resource Underutilization

A server may have a lot of unused capacity.

For example:

``` text
Server capacity = 100%

CPU usage     = 20%
Memory usage  = 25%
Network usage = 10%
```

You still have to maintain and pay for the entire physical server even
though the application uses only a small portion of its resources.

The lecture notes that this underutilization creates real infrastructure
cost.

------------------------------------------------------------------------

# 3. Virtualization

The solution introduced in the lecture is **virtualization**.

Instead of dedicating an entire physical server to one application, the
physical hardware can be divided into multiple **Virtual Machines
(VMs)**.

Conceptually:

``` text
                 Physical Server
        ┌──────────────────────────────┐
        │ CPU: 16 cores                │
        │ RAM: 32 GB                   │
        │ Storage                      │
        │ Network                      │
        └──────────────┬───────────────┘
                       │
                 Hypervisor
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
        VM 1         VM 2         VM 3
      4 cores       8 cores       4 cores
       8 GB         16 GB          8 GB
```

The lecture describes virtualization as abstracting physical hardware
through a **virtualization layer/hypervisor**.

It separates the operating system and application from the physical
hardware.

### Why is this useful?

Multiple applications can share the same physical machine while
remaining isolated from one another.

The lecture gives the following utilization comparison:

-   **Before virtualization:** roughly 10--30% server utilization
-   **After virtualization:** roughly 60--80% of hardware capacity

------------------------------------------------------------------------

# 4. What Is a Virtual Machine?

A **Virtual Machine (VM)** is a software-created computer.

To an application, a VM can look like a normal physical computer.

A VM can have:

-   CPU resources
-   Memory
-   Storage
-   Network connectivity
-   Its own operating system
-   Its own applications

For example:

``` text
VM
├── Virtual CPU
├── Virtual Memory
├── Virtual Disk
├── Virtual Network
├── Operating System
└── Application
```

A single physical server can therefore host several VMs.

``` text
Physical Server
      ↓
  Hypervisor
      ↓
 ┌────┼────┐
 ↓    ↓    ↓
VM1  VM2  VM3
```

Different VMs may belong to different users.

The important idea is **isolation**: one VM should not simply behave as
though it has access to another user's VM.

------------------------------------------------------------------------

# 5. What Is a Hypervisor?

A **hypervisor** is the virtualization layer that manages virtual
machines and their access to physical hardware.

Think of it as a manager between:

``` text
Virtual Machines
       ↓
   Hypervisor
       ↓
Physical Hardware
```

The hypervisor helps provide each VM with virtualized CPU, memory,
storage, and networking resources.

For this lecture, the internal implementation of virtualization is not
the focus. The important concept is that the hypervisor provides the
virtualization layer.

------------------------------------------------------------------------

# 6. IaaS --- Infrastructure as a Service

## Definition

**IaaS = Infrastructure as a Service**

The lecture uses the NIST definition: the consumer can provision
fundamental computing resources such as:

-   Processing
-   Storage
-   Networks
-   Other fundamental computing resources

The consumer can then deploy and run software, including:

-   Operating systems
-   Applications

So the basic idea is:

> **The cloud provider gives you virtual infrastructure, and you use
> that infrastructure to run your own software.**

------------------------------------------------------------------------

# 7. IaaS in Simple Words

Suppose you want a server.

Instead of buying:

``` text
Physical CPU
Physical RAM
Physical Disk
Physical Network Card
Physical Server
```

you can request virtual resources:

``` text
Create VM

CPU     = 4 cores
Memory  = 8 GB
Disk    = 100 GB
OS      = Ubuntu
Network = Enabled
```

The cloud infrastructure creates the required virtual environment.

You can then install and run your application.

Conceptually:

``` text
Your Application
       ↓
Operating System
       ↓
Virtual Machine
       ↓
Hypervisor
       ↓
Physical Hardware
```

------------------------------------------------------------------------

# 8. What Happens When You Run a Program?

Consider:

``` bash
./HelloWorld.exe
```

A program needs resources to execute.

The lecture highlights:

### CPU

The CPU executes the program's instructions.

``` text
Program
   ↓
Instructions
   ↓
CPU
```

### Memory

The program needs memory while it is running.

``` text
Storage
   ↓
Program loaded into memory
   ↓
CPU executes it
```

### Storage

The program itself needs to be stored somewhere.

For example:

``` text
HelloWorld.exe
```

could exist on a disk.

### Network

Some applications may also need network access.

### Operating System

The application normally obtains access to resources through the
operating system.

So:

``` text
Application
     ↓
Operating System
     ↓
Hardware
```

------------------------------------------------------------------------

# 9. Running an Application Inside a VM

Now suppose `HelloWorld.exe` runs inside a VM.

The application still needs:

``` text
CPU
Memory
Storage
Network
Operating System
```

But there is an additional virtualization layer:

``` text
Application
     ↓
Operating System
     ↓
Virtual Machine
     ↓
Hypervisor
     ↓
Physical Hardware
```

The physical hardware is hidden behind the virtualization layer.

------------------------------------------------------------------------

# 10. VMs vs Containers

The lecture introduces two ways of sharing physical machines:

1.  Virtual Machines
2.  Containers

------------------------------------------------------------------------

## 10.1 Virtual Machine

A VM looks like an actual physical machine to the software.

It can have its own:

-   Operating system
-   Applications
-   Virtual CPU
-   Virtual memory
-   Virtual disk
-   Virtual network

Conceptually:

``` text
Physical Server
      ↓
  Hypervisor
      ↓
┌───────────────┐
│ VM            │
│               │
│ Guest OS      │
│ Application   │
└───────────────┘
```

------------------------------------------------------------------------

## 10.2 Container

A container is more lightweight.

The lecture describes it as a **sandboxed process** that uses the
physical server's operating system.

Conceptually:

``` text
Physical Server
      ↓
    OS
      ↓
Container Runtime
      ↓
┌──────┬──────┬──────┐
│ C1   │ C2   │ C3   │
│ App  │ App  │ App  │
└──────┴──────┴──────┘
```

Containers are therefore generally lighter than VMs.

------------------------------------------------------------------------

## 10.3 VM vs Container

  Feature            VM                    Container
  ------------------ --------------------- ----------------------------------
  Looks like         Complete machine      Isolated process/environment
  Operating system   Can have its own OS   Uses host OS
  Weight             Heavier               More lightweight
  Isolation          VM-level isolation    Process/container isolation
  Main idea          Virtual computer      Lightweight isolated environment

### Easy way to remember

**VM:**

> "Give me another computer."

**Container:**

> "Give me an isolated environment to run this application."

------------------------------------------------------------------------

# 11. Storage in IaaS

The lecture discusses two important storage approaches:

1.  **Virtual Disks / Block Storage**
2.  **Object Storage / Blob Storage**

These solve different problems.

------------------------------------------------------------------------

# 12. Virtual Disks / Block Storage

A **virtual disk** is a virtualized storage device that can be attached
to a VM.

The VM sees it as a disk.

Conceptually:

``` text
Physical Storage
       ↓
Storage Virtualization
       ↓
Virtual Disk
       ↓
VM
```

For example:

``` text
VM
├── CPU
├── RAM
├── OS
└── 256 GB Virtual Disk
```

The VM can use the virtual disk just like a normal disk.

It can:

-   Create files
-   Store application data
-   Store the operating system
-   Use it for paging/swapping

------------------------------------------------------------------------

## 12.1 Sharing and Pooling

Physical storage can be pooled.

For example:

``` text
Physical Disk 1 → 128 GB
Physical Disk 2 → 128 GB
```

A storage virtualization system could present:

``` text
256 GB Virtual Disk
```

The VM does not need to know exactly which physical disk contains every
part of the virtual disk.

The lecture's main idea is:

> **Physical disks can be shared and pooled to provide virtual disks.**

------------------------------------------------------------------------

# 13. Object Storage / Blob Storage

Object storage is designed for storing files/objects.

The lecture compares it to systems such as:

-   Dropbox
-   Google Drive
-   Amazon S3

Examples of objects:

``` text
image.jpg
video.mp4
dataset.csv
model.pkl
application.zip
```

Conceptually:

``` text
Object Store
│
├── image.jpg
├── video.mp4
├── dataset.csv
├── model.pkl
└── application.zip
```

Applications retrieve objects when they need them.

Objects are commonly referenced using URLs.

A typical conceptual structure is:

``` text
account / container / object-name
```

------------------------------------------------------------------------

# 14. Block Storage vs Object Storage

This distinction is important.

  -----------------------------------------------------------------------
  Block / Virtual Disk                Object / Blob Storage
  ----------------------------------- -----------------------------------
  Looks like a disk                   Looks like a collection of
                                      objects/files

  Usually attached to a VM            Accessed by applications

  VM can create normal files on it    Objects are stored and retrieved

  Useful for VM storage               Useful for large collections of
                                      files/objects
  -----------------------------------------------------------------------

### Easy memory trick

**Block storage = hard disk**

**Object storage = cloud file/object repository**

------------------------------------------------------------------------

# 15. Virtual Networking

VMs also need networks.

A physical computer normally has:

``` text
Computer
   ↓
Network Adapter
   ↓
Network Switch
   ↓
Router
   ↓
Internet
```

A cloud environment creates virtual versions of these networking
components.

The lecture discusses:

-   Virtual Network Adapters
-   Virtual Switches
-   Virtual Ports
-   Physical Network Adapters

------------------------------------------------------------------------

# 16. Virtual Network Adapter

A VM can have one or more **virtual network adapters**.

A virtual network adapter can have:

-   IP address
-   DNS name
-   MAC address

Conceptually:

``` text
VM
 ↓
Virtual Network Adapter
 ↓
Virtual Network
```

From the VM's perspective, this behaves similarly to a normal network
interface.

------------------------------------------------------------------------

# 17. Virtual Switch

A **virtual switch** is a software-based switch.

It connects virtual machines.

``` text
       VM1
        │
        ↓
   Virtual Switch
      /       \
     ↓         ↓
   VM2        VM3
```

Because the switch is virtual, the networking can be implemented in
software.

------------------------------------------------------------------------

# 18. Virtual Ports

A virtual switch has virtual ports.

Conceptually:

``` text
VM1
 ↓
Virtual Adapter
 ↓
Virtual Port
 ↓
Virtual Switch
 ↓
Virtual Port
 ↓
VM2
```

The lecture describes virtual ports as software networking components
associated with the virtual switch.

------------------------------------------------------------------------

# 19. VM-to-VM Communication

Suppose:

``` text
VM1
IP = 10.0.0.5
```

and:

``` text
VM2
IP = 10.0.0.6
```

VM1 wants to send a message to VM2.

It specifies VM2's destination IP:

``` text
Destination IP = 10.0.0.6
```

The virtual networking system delivers the message.

The lecture represents this path conceptually as:

``` text
V → VP → VP → V
```

where:

-   `V` = virtual network adapter
-   `VP` = virtual port

------------------------------------------------------------------------

# 20. VM-to-Outside Communication

Now suppose VM2 wants to communicate with an external machine.

The path conceptually becomes:

``` text
VM2
 ↓
Virtual Adapter
 ↓
Virtual Port
 ↓
Virtual Switch
 ↓
Physical Network Adapter
 ↓
Outside Network
```

The lecture represents this as:

``` text
V → VP → VP → N → outside world
```

where `N` represents the physical network adapter.

------------------------------------------------------------------------

# 21. Creating a VM

The lecture describes a sequence for creating a VM.

## Step 1 --- User specifies configuration

The user specifies things such as:

``` text
CPU
Memory
Storage
Software
Network requirements
```

------------------------------------------------------------------------

## Step 2 --- Cloud software creates the VM

The cloud management software determines how to create the requested VM.

------------------------------------------------------------------------

## Step 3 --- Create virtual disk(s)

The VM receives the virtual storage it needs.

``` text
VM
 ↓
Virtual Disk
```

------------------------------------------------------------------------

## Step 4 --- Copy OS/application binaries

The required operating system and application binaries can be placed
onto the boot disk.

For example:

``` text
Ubuntu
Application
Configuration
```

------------------------------------------------------------------------

## Step 5 --- Boot the operating system

The VM starts its operating system.

``` text
VM
 ↓
Boot OS
 ↓
OS running
```

------------------------------------------------------------------------

## Step 6 --- Set up networking

The VM gets the required virtual networking configuration.

``` text
VM
 ↓
Virtual Network
 ↓
External Network
```

------------------------------------------------------------------------

# 22. Example: Bookarts Bookstore

The lecture asks us to imagine an online bookstore called **Bookarts**.

Users can:

1.  Search for books
2.  Order books

The important question is:

> How would Bookarts be implemented in an IaaS cloud?

We need to think about:

-   What VMs are required?
-   Where are the VMs created?
-   What storage do they need?
-   What software runs on them?
-   How are the VMs connected?
-   How do they connect to the outside world?

A conceptual architecture could be:

``` text
                    Internet
                       │
                       ↓
                 Frontend VM
                       │
                       ↓
                  Backend VM
                       │
              ┌────────┴────────┐
              ↓                 ↓
          Database VM       Other Services
```

Storage might include:

``` text
Virtual Disk
     ↓
Operating system
Application
Database data
```

And object storage might contain:

``` text
Book images
Application binaries
Other large files
```

The exact architecture depends on the application requirements.

------------------------------------------------------------------------

# 23. The VM Sprawl Problem

At first, managing VMs seems easy.

Maybe you have:

``` text
VM1
VM2
VM3
```

But imagine a large organization with:

``` text
VM1
VM2
VM3
...
VM1000
...
VM10000
```

Now many questions appear:

-   Where should a new VM be created?
-   Which physical server should host it?
-   How do we keep track of all VMs?
-   Who is allowed to create VMs?
-   How do we handle failures?
-   How do we manage security?
-   How do users self-service?

This growing number of VMs can create:

> **VM sprawl**

VM sprawl means that the environment becomes difficult to manage because
there are many virtual machines and associated resources.

------------------------------------------------------------------------

# 24. Why a Cloud Management Layer Is Needed

Virtualization alone is not enough.

We have:

``` text
Physical Infrastructure
        ↓
Virtualization
        ↓
VMs
```

But who manages everything?

We need a **cloud management layer**.

Conceptually:

``` text
Users
  ↓
Cloud Management Layer
  ↓
Virtual Infrastructure
  ↓
Physical Infrastructure
```

This management layer provides centralized control over the
infrastructure.

------------------------------------------------------------------------

# 25. Cloud Controller

The lecture introduces an **IaaS Cloud Controller**.

Think of the cloud controller as a management/coordination layer for the
cloud infrastructure.

The lecture lists responsibilities such as:

### 1. Schedule and manage VMs

Decide where VMs should be created and manage their lifecycle.

### 2. Security

Help enforce isolation and access control.

### 3. Fault handling

Handle infrastructure failures and respond appropriately.

Conceptually:

``` text
                 Cloud Controller
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
     VM Management   Security     Fault Handling
        │
        ↓
Physical Infrastructure
```

------------------------------------------------------------------------

# 26. What Does VM Scheduling Mean?

Suppose we have three physical servers:

``` text
Server A
8 CPU available

Server B
16 CPU available

Server C
2 CPU available
```

A user asks:

``` text
Create VM
4 CPU
8 GB RAM
```

The cloud controller needs to determine which physical machine can host
the VM.

For example:

``` text
User request
     ↓
Cloud Controller
     ↓
Choose suitable server
     ↓
Create VM
```

This decision is called **scheduling**.

------------------------------------------------------------------------

# 27. Cloud Management and Self-Service

In a cloud environment, users should not have to physically contact an
administrator every time they need a VM.

Instead:

``` text
User
 ↓
Cloud interface / API
 ↓
Cloud management
 ↓
VM created
```

This provides **self-service**.

The user can request infrastructure while the cloud management system
handles the underlying complexity.

------------------------------------------------------------------------

# 28. OpenStack

The lecture introduces **OpenStack** as an example of a cloud platform.

OpenStack is an open-source cloud infrastructure platform.

The lecture emphasizes that OpenStack is designed as:

> **A collection of independent services**

and that these services provide:

> **REST APIs**

Conceptually:

``` text
                    OpenStack
                        │
       ┌────────────────┼────────────────┐
       ↓                ↓                ↓
    Compute           Storage          Network
       ↓                ↓                ↓
      VMs             Disks          Virtual Networks
```

The major idea is not that OpenStack is one giant program.

Instead, it is composed of multiple services that work together.

------------------------------------------------------------------------

# 29. Why Independent Services?

Suppose a cloud has separate responsibilities:

``` text
Compute
Storage
Networking
Identity
```

We can implement them as separate services.

``` text
Compute Service
      │
      ├── manages compute resources

Storage Service
      │
      ├── manages storage

Network Service
      │
      ├── manages networking
```

These services can communicate using APIs.

This makes the overall system modular.

------------------------------------------------------------------------

# 30. REST APIs

An **API** is an interface through which software communicates with
another software component.

For example:

``` text
Your Program
     ↓
API Request
     ↓
Cloud Service
     ↓
Operation
```

A REST API commonly uses HTTP methods such as:

``` text
GET
POST
PUT
DELETE
```

Conceptually:

``` text
GET /vms
```

could request information about VMs.

And:

``` text
POST /vms
```

could represent a request to create a VM, depending on the API design.

The important idea from the lecture is:

> **Cloud services expose interfaces through which other software can
> interact with them.**

------------------------------------------------------------------------

# 31. Service-Oriented Architecture (SOA)

The lecture then introduces **Service-Oriented Architecture**.

SOA means building an application using independent services.

Instead of:

``` text
One Huge Application
┌───────────────────────────────┐
│ Everything                    │
│ Search                        │
│ Orders                        │
│ Payments                      │
│ Users                         │
└───────────────────────────────┘
```

we can have:

``` text
              Application
                   │
       ┌───────────┼───────────┐
       ↓           ↓           ↓
    Search       Orders      Payment
    Service      Service     Service
```

Each service has a particular responsibility.

------------------------------------------------------------------------

# 32. How Services Communicate

Services communicate through messages/service calls.

For example:

``` text
Frontend
   ↓
Search Service
   ↓
Search result
```

Then:

``` text
Frontend
   ↓
Order Service
   ↓
Order created
```

A service does not necessarily need to know the internal implementation
of another service.

It only needs to know how to interact with its interface.

------------------------------------------------------------------------

# 33. Heterogeneous Services

The lecture notes that services are likely to be **heterogeneous**.

That means different services can potentially be implemented using
different technologies.

For example:

``` text
Search Service
→ Python

Payment Service
→ Java

User Service
→ Node.js
```

They can still communicate if they follow compatible
interfaces/protocols.

------------------------------------------------------------------------

# 34. Service Registry

If many services exist, another problem appears:

> How does one service find another service?

This is where a **Service Registry** comes in.

Think of it as a directory containing services and their endpoints.

For example:

``` text
Service Registry

Service             Endpoint
-----------------------------------
Search              10.0.0.10:8080
Orders              10.0.0.11:8080
Payment             10.0.0.12:8080
```

A service can look up another service and discover where it can be
contacted.

The lecture describes the registry as providing a list of services and
their endpoints, such as IP address and port.

------------------------------------------------------------------------

# 35. Putting the Entire Lecture Together

This is the most important diagram to remember:

``` text
                         USER
                          │
                          ↓
                    Cloud API
                          │
                          ↓
                ┌─────────────────┐
                │ Cloud Controller│
                └────────┬────────┘
                         │
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
       Compute         Storage        Network
          │              │              │
          ↓              ↓              ↓
        VMs          Virtual Disks   Virtual Network
          │
          ↓
      Hypervisor
          │
          ↓
 ┌──────────────────────────┐
 │   Physical Infrastructure │
 │                          │
 │ CPU + RAM + Disk + NIC   │
 └──────────────────────────┘
```

And applications running on top:

``` text
Application
     ↓
Operating System
     ↓
Virtual Machine
     ↓
Hypervisor
     ↓
Physical Hardware
```

------------------------------------------------------------------------

# 36. The Complete Story of the Lecture

The entire lecture can be understood as a chain of problems and
solutions.

## Problem 1 --- Physical servers are underutilized

``` text
One application
      ↓
One physical server
      ↓
Lots of unused capacity
```

### Solution

``` text
Virtualization
      ↓
Multiple VMs per physical server
      ↓
Better utilization
```

------------------------------------------------------------------------

## Problem 2 --- VMs need resources

A VM needs:

``` text
CPU
Memory
Storage
Network
```

### Solution

IaaS provides virtualized:

``` text
Compute
Storage
Networking
```

------------------------------------------------------------------------

## Problem 3 --- Storage needs to be virtualized

### Solution

Use:

``` text
Virtual Disks / Block Storage
```

and:

``` text
Object / Blob Storage
```

------------------------------------------------------------------------

## Problem 4 --- VMs need networking

### Solution

Use:

``` text
Virtual Network Adapter
Virtual Switch
Virtual Ports
Physical Network Adapter
```

------------------------------------------------------------------------

## Problem 5 --- Thousands of VMs are difficult to manage

``` text
VM1
VM2
VM3
...
VM10000
```

This causes **VM sprawl**.

### Solution

Use a:

> **Cloud Management Layer**

with a:

> **Cloud Controller**

------------------------------------------------------------------------

## Problem 6 --- Cloud infrastructure has many responsibilities

### Solution

Organize cloud functionality into services.

For example:

``` text
Compute Service
Storage Service
Network Service
```

OpenStack is an example of a platform designed around this service-based
approach.

------------------------------------------------------------------------

## Problem 7 --- Services need to communicate and discover each other

### Solution

Use:

``` text
REST APIs
```

and potentially:

``` text
Service Registry
```

This connects to the broader idea of:

> **Service-Oriented Architecture (SOA)**

------------------------------------------------------------------------

# 37. Important Definitions to Memorize

### Cloud Computing

Providing computing resources/services through a network instead of
requiring the user to own all of the physical infrastructure.

### IaaS

**Infrastructure as a Service** --- provides fundamental computing
resources such as processing, storage, and networking, on which users
can deploy operating systems and applications.

### Virtualization

Abstracting physical computing resources so that multiple virtual
environments can share physical hardware.

### Hypervisor

The virtualization layer that manages virtual machines and their access
to physical resources.

### Virtual Machine

A software-created computer environment that behaves like a physical
machine to the software running inside it.

### Container

A lightweight, isolated execution environment described in the lecture
as a sandboxed process that uses the physical server's OS.

### Block Storage

Virtual disk storage that can be attached to a VM and used like a disk.

### Object Storage

Storage that keeps files/objects and allows applications to retrieve
them, commonly through APIs/URLs.

### Virtual Network Adapter

A software-based network interface associated with a VM.

### Virtual Switch

A software-based switch used to connect virtual networking components.

### Cloud Controller

A management component responsible for tasks such as VM
scheduling/management, security, and fault handling.

### OpenStack

An open-source cloud platform built as a collection of independent
services exposing REST APIs.

### REST API

An interface that allows software/services to communicate using
web-style requests.

### SOA

**Service-Oriented Architecture** --- an architectural approach in which
applications are built from independent services that communicate
through defined interfaces.

### Service Registry

A directory that allows services to discover other services and their
endpoints.

------------------------------------------------------------------------

# 38. Quick Revision Sheet

``` text
IaaS
│
├── Compute
│   └── Virtual Machines
│
├── Storage
│   ├── Block Storage / Virtual Disks
│   └── Object Storage / Blob Store
│
└── Networking
    ├── Virtual Network Adapter
    ├── Virtual Switch
    └── Virtual Ports
```

``` text
Physical Server
      ↓
Hypervisor
      ↓
Virtual Machines
      ↓
Operating Systems
      ↓
Applications
```

``` text
Many VMs
   ↓
VM Sprawl
   ↓
Cloud Management Layer
   ↓
Cloud Controller
   ↓
Scheduling + Security + Fault Handling
```

``` text
OpenStack
   ↓
Independent Cloud Services
   ↓
REST APIs
   ↓
Compute + Storage + Networking
```

``` text
SOA
│
├── Independent Services
├── Communicate using messages/service calls
├── Defined interfaces
├── Potentially heterogeneous technologies
└── Service Registry for discovering endpoints
```

------------------------------------------------------------------------

# 39. One-Minute Explanation

If you are asked **"What is this lecture about?"**, you can explain it
like this:

> IaaS stands for Infrastructure as a Service. Instead of giving every
> application its own physical server, cloud infrastructure uses
> virtualization to divide physical hardware into multiple virtual
> machines. A hypervisor manages these VMs. Each VM can have its own
> operating system and applications.
>
> IaaS provides three major types of resources: compute, storage, and
> networking. Compute is provided through things such as VMs. Storage
> can be provided through virtual disks/block storage or object/blob
> storage. Networking is implemented using virtual network adapters,
> virtual switches, and virtual ports.
>
> As the number of VMs grows, manually managing them becomes difficult
> and leads to VM sprawl. Therefore, a cloud management layer and cloud
> controller are needed to manage VM scheduling, security, and fault
> handling.
>
> OpenStack is an example of an open-source cloud platform built as a
> collection of independent services that expose REST APIs. This
> connects to Service-Oriented Architecture, where applications are
> constructed from independent services that communicate through defined
> interfaces. A service registry can help services discover the
> endpoints of other services.

------------------------------------------------------------------------

# 40. What You Should Study First

If you're completely new to cloud, learn these in this order:

``` text
1. Physical Server
        ↓
2. Virtualization
        ↓
3. Hypervisor
        ↓
4. Virtual Machine
        ↓
5. IaaS
        ↓
6. Block Storage
        ↓
7. Object Storage
        ↓
8. Virtual Networking
        ↓
9. Cloud Controller
        ↓
10. VM Sprawl
        ↓
11. OpenStack
        ↓
12. REST API
        ↓
13. SOA
        ↓
14. Service Registry
```

**Do not try to memorize OpenStack/SOA first.** Once you understand
**physical hardware → virtualization → VM → IaaS → storage/networking →
cloud management**, the later concepts become much easier.
