# Understanding the Reconciliation Loop

## Introduction

One of the most important concepts in both Kubernetes and GitOps is reconciliation.

Many engineers associate reconciliation with GitOps tools such as Argo CD and Flux. However, reconciliation existed long before GitOps.

In reality, Kubernetes itself is built around reconciliation.

GitOps extends this concept beyond the cluster by introducing Git as the source of truth.

Understanding reconciliation is fundamental to understanding how modern platforms operate.

---

# Kubernetes Was Built Around Reconciliation

Kubernetes is fundamentally a reconciliation-based system.

When resources are deployed to a Kubernetes cluster, the desired state of those resources is stored within:

```text
etcd
```

The desired state represents:

> How the system should look.

For example:

```text
Deployment
Replicas = 3
Image = v1.0
```

Once the resource is stored in etcd, controllers running inside the control plane continuously monitor the cluster.

Their responsibility is simple:

```text
Desired State
      =
Actual State
```

Whenever a difference exists, Kubernetes attempts to eliminate that difference.

This process is known as reconciliation.

---

# Understanding Desired State

Desired State represents the intended configuration of the system.

It answers the question:

> How should the system look?

Examples include:

```text
Replicas = 3
Image = backend:v1.0
CPU = 500m
Memory = 512Mi
```

The desired state is stored in Kubernetes resources and persisted within etcd.

Kubernetes continuously attempts to enforce this state.

---

# Understanding Actual State

Actual State represents what is currently running inside the cluster.

It answers the question:

> What is currently happening?

Example:

Desired State:

```text
Replicas = 3
```

Actual State:

```text
Replicas = 2
```

A difference now exists between the intended state and the running state.

This difference triggers reconciliation.

---

# The Kubernetes Reconciliation Loop

The Kubernetes reconciliation loop follows a continuous cycle.

```text
Observe
    ↓
Compare
    ↓
Detect Difference
    ↓
Apply Changes
    ↓
Observe Again
```

This process never stops.

Controllers continuously monitor resources and ensure that the actual state matches the desired state.

Example:

Desired State:

```text
Replicas = 3
```

Actual State:

```text
Replicas = 2
```

The Deployment Controller detects the difference and creates an additional Pod.

The system returns to:

```text
Replicas = 3
```

The desired state has been restored.

---

# Controllers and Operators

Kubernetes relies on Controllers to perform reconciliation.

Examples include:

```text
Deployment Controller
ReplicaSet Controller
StatefulSet Controller
Node Controller
Service Controller
```

Each controller manages a specific type of resource.

Operators extend this concept further.

Operators introduce custom reconciliation logic for applications and platform services.

Examples include:

```text
Prometheus Operator
Vault Operator
Cert-Manager
```

Both Controllers and Operators follow the same principle:

```text
Desired State
        =
Actual State
```

---

# How GitOps Extends Reconciliation

Kubernetes reconciliation focuses on resources running inside the cluster.

GitOps introduces another layer of reconciliation.

Instead of comparing only:

```text
Desired State In Etcd
            VS
Runtime State
```

GitOps introduces:

```text
Git
 │
 ▼
Desired State
 │
 ▼
Cluster
```

Git becomes the source of truth for the platform.

The GitOps controller continuously compares:

```text
Git State
       VS
Cluster State
```

and ensures they remain aligned.

This extends reconciliation beyond Kubernetes resources and into platform operations.

---

# Kubernetes Reconciliation vs GitOps Reconciliation

Although both rely on the same principle, they operate at different layers.

## Kubernetes Reconciliation

Focuses on:

```text
Runtime State
```

Examples:

```text
Pods
Deployments
Services
StatefulSets
```

Kubernetes controllers ensure resources behave as expected.

---

## GitOps Reconciliation

Focuses on:

```text
Declarative State
```

Examples:

```text
Applications
Platform Components
Security Policies
Environment Configurations
```

GitOps controllers ensure that the cluster matches the state declared in Git.

---

# Understanding Drift

Drift occurs whenever the desired state differs from the actual state.

```text
Desired State
       ≠
Actual State
```

Common examples include:

### Manual Scaling

```text
kubectl scale deployment
```

### Manual Configuration Changes

```text
kubectl edit deployment
```

### Resource Deletion

```text
kubectl delete deployment
```

These actions modify the cluster without updating Git.

As a result, the cluster begins drifting away from the declared state.

---

# GitOps Reconciliation Workflow

When drift occurs, GitOps controllers perform reconciliation.

The workflow follows:

```text
Observe
    ↓
Compare
    ↓
Detect Drift
    ↓
Apply Changes
    ↓
Restore Desired State
```

Example:

Git:

```text
Replicas = 5
```

Cluster:

```text
Replicas = 2
```

The GitOps controller detects the difference and restores:

```text
Replicas = 5
```

The cluster once again matches the desired state stored in Git.

---

# Benefits of Reconciliation

Reconciliation provides several operational advantages.

## Consistency

The cluster continuously matches the declared state.

---

## Drift Detection

Unauthorized changes are detected automatically.

---

## Self-Healing

Deleted or modified resources can be restored automatically.

---

## Reliability

Platform behavior becomes predictable.

---

## Reduced Manual Intervention

Operators spend less time correcting configuration drift.

---

# Limitations of Reconciliation

Although reconciliation is powerful, it has limitations.

Reconciliation guarantees:

```text
Desired State
       =
Actual State
```

It does not guarantee:

## Application Health

The application may still fail.

---

## Business Logic Correctness

The application may contain bugs.

---

## Successful Releases

The desired state itself may be incorrect.

---

Reconciliation ensures consistency.

It does not guarantee correctness.

---

# Layered Reconciliation Model

Modern platforms operate with multiple layers of reconciliation.

```text
Git
 │
 ▼
GitOps Controller
 │
 ▼
Kubernetes API
 │
 ▼
Controllers & Operators
 │
 ▼
Running Workloads
```

Each layer continuously attempts to align the system with its desired state.

This layered approach is one of the reasons Kubernetes and GitOps platforms are highly resilient and self-healing.

---

# Key Takeaway

Kubernetes introduced reconciliation by continuously comparing the desired state stored in etcd with the actual runtime state of resources inside the cluster.

GitOps extends this model by treating Git as the source of truth and continuously reconciling the declarative state stored in Git with the state running inside Kubernetes.

Together, Kubernetes Controllers, Operators, and GitOps Controllers create a layered reconciliation model that enables consistency, drift detection, self-healing, and operational reliability across modern cloud-native platforms.
