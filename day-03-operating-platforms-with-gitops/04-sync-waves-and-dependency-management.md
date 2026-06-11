# Sync Waves and Dependency Management

## Introduction

Modern applications are rarely composed of a single resource.

A typical application deployed on Kubernetes may contain:

```text
Namespace
ConfigMap
Secret
Database
Backend
Frontend
Gateway
```

In a distributed application, these resources often depend on one another.

For example:

```text
Database
    ↓
Backend
    ↓
Frontend
```

The challenge is no longer simply deploying resources.

The challenge becomes deploying resources in the correct order.

This is where Sync Waves become important.

---

# The Resource Ordering Problem

Resources can be created in Kubernetes using:

### Manual Operations

```text
kubectl apply
```

or

### Kubernetes API

```text
REST API
```

Both methods ultimately create resources inside the cluster.

However, Kubernetes does not understand the business dependencies between resources.

For example:

```text
Backend
```

depends on:

```text
Database
```

but Kubernetes only sees:

```text
Deployment Objects
```

It does not automatically understand application-level dependency relationships.

This creates a deployment ordering problem.

---

# Why Deployment Order Matters

Consider the following resources:

```text
ConfigMap
Secret
Database
Backend
Frontend
```

If resources are applied in an arbitrary order:

```text
Backend
Database
Frontend
```

the Backend may attempt to start before the Database becomes available.

Similarly:

```text
Frontend
```

may become available before Backend APIs are operational.

The result is deployment failures, startup issues, and unstable application behavior.

Therefore deployment becomes more than:

```text
Apply Resources
```

It becomes:

```text
Apply Resources In Dependency Order
```

---

# How Argo CD Solves This Problem

Unlike kubectl, Argo CD does not simply apply resources one by one.

Before synchronization begins, Argo CD:

```text
Reads Resources
       ↓
Parses Resources
       ↓
Builds Execution Plan
       ↓
Applies Resources
```

Argo CD creates a dependency-aware execution plan for the entire application.

Instead of blindly applying resources, it determines:

```text
What Should Be Applied First
```

and

```text
What Should Be Applied Later
```

This significantly improves deployment reliability.

---

# Dynamic Resource Ordering

Argo CD automatically sorts resources before synchronization.

The sorting process ensures a stable and predictable deployment sequence.

By default, Argo CD considers:

```text
Resource Kind
```

and

```text
Resource Name
```

to determine execution order.

This allows resources to be deployed consistently during every synchronization operation.

---

# Why Default Ordering Is Not Enough

Although Argo CD provides intelligent ordering, it cannot always understand application-specific dependencies.

Consider:

```text
PostgreSQL
      ↓
Backend
```

From Kubernetes' perspective:

```text
Both Are Resources
```

However, only the application owner understands that the Backend requires PostgreSQL before it can function correctly.

This creates the need for explicit dependency management.

---

# Introducing Sync Waves

Sync Waves provide explicit deployment ordering.

A Sync Wave represents an execution priority assigned to a resource.

Resources assigned to lower waves are deployed before resources assigned to higher waves.

Example:

```text
Wave 0
     ↓
Wave 1
     ↓
Wave 2
     ↓
Wave 3
```

Argo CD processes waves sequentially.

A higher wave does not begin until the previous wave has completed.

This allows platform teams to control deployment order.

---

# Understanding Sync Wave Execution

Consider the following deployment:

```text
Wave 0
    │
    ▼
ConfigMap
Secret

Wave 1
    │
    ▼
Database

Wave 2
    │
    ▼
Backend

Wave 3
    │
    ▼
Frontend
```

The deployment sequence becomes:

```text
Configuration
      ↓
Database
      ↓
Backend
      ↓
Frontend
```

This aligns resource deployment with application dependencies.

---

# Negative Sync Waves

Argo CD also supports negative Sync Waves.

Example:

```text
Wave -2
Wave -1
Wave 0
Wave 1
```

Negative waves execute before standard resources.

They are commonly used for:

```text
CRDs
Operators
Infrastructure Components
```

This ensures prerequisite resources exist before application resources are deployed.

---

# Sync Phases and Sync Waves

Sync Phases and Sync Waves work together but solve different problems.

### Sync Phases

Answer:

```text
When Should Actions Execute?
```

Examples:

```text
PreSync
Sync
PostSync
SyncFail
```

---

### Sync Waves

Answer:

```text
In What Order Should Resources Execute?
```

Examples:

```text
Wave -1
Wave 0
Wave 1
Wave 2
```

Think of it as:

```text
Sync Phase
       +
Sync Wave
```

which together provide deployment orchestration.

---

# Resource Ordering Hierarchy

Argo CD determines deployment order using multiple levels of sorting.

The ordering hierarchy is:

### 1. Sync Phase

```text
PreSync
Sync
PostSync
SyncFail
```

---

### 2. Sync Wave

```text
Wave -1
Wave 0
Wave 1
Wave 2
```

---

### 3. Resource Kind

Examples:

```text
Namespace
CRD
Deployment
Service
```

---

### 4. Resource Name

Used to ensure deterministic ordering.

---

Final execution hierarchy:

```text
Sync Phase
      ↓
Sync Wave
      ↓
Resource Kind
      ↓
Resource Name
```

This is the order Argo CD follows when building its execution plan.

---

# Real-World Example

Consider a typical application deployment.

```text
Wave -1
    │
    ▼
Namespace

Wave 0
    │
    ▼
ConfigMap
Secret

Wave 1
    │
    ▼
PostgreSQL

Wave 2
    │
    ▼
Backend

Wave 3
    │
    ▼
Frontend
```

The deployment now follows the actual dependency chain of the application.

Each component becomes available before dependent components are deployed.

This improves deployment reliability and reduces startup failures.

---

# CRD Dependency Example

One of the most common GitOps dependency challenges involves CRDs.

Example:

```text
Prometheus Operator
```

creates:

```text
ServiceMonitor CRD
```

Before:

```text
ServiceMonitor
```

can be deployed.

Correct ordering becomes:

```text
Wave -1
      ↓
CRD

Wave 0
      ↓
ServiceMonitor
```

Without this ordering, synchronization may fail with:

```text
No Matches For Kind
```

errors because the Custom Resource Definition does not yet exist.

---

# Benefits of Sync Waves

Sync Waves provide several operational benefits.

### Explicit Dependency Management

Resources can be deployed in dependency order.

---

### Predictable Deployments

Deployments follow the same sequence every time.

---

### Reduced Deployment Failures

Dependencies are available before dependent resources start.

---

### Better Platform Scalability

Large distributed applications become easier to manage.

---

# Key Takeaway

Modern applications consist of multiple resources that often depend on one another.

Argo CD does not simply apply resources in repository order. Instead, it reads and parses all resources to build a dependency-aware execution plan.

By default, Argo CD dynamically sorts resources using Resource Kind and Resource Name. When application-specific dependencies exist, Sync Waves provide explicit deployment ordering.

Together, Sync Phases, Sync Waves, Resource Kind, and Resource Name create a predictable deployment orchestration model that enables reliable GitOps operations for complex distributed applications.
