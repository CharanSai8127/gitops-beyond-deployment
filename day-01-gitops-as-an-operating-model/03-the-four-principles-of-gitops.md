# The Four Principles of GitOps

## Introduction

GitOps is an operational model for managing infrastructure and applications using Git as the single source of truth.

While different GitOps tools exist, including Argo CD and FluxCD, all GitOps implementations are built around a common set of principles.

These principles define how desired state is managed, delivered, and enforced across environments.

A platform can only be considered GitOps-based if it follows these principles.

The four principles of GitOps are:

1. Declarative
2. Versioned and Immutable
3. Pulled Automatically
4. Continuously Reconciled

Together, these principles provide the foundation for operating modern Kubernetes platforms.

---

# 1. Declarative

The first principle of GitOps is that the desired state of the system must be expressed declaratively.

A declarative definition describes the intended outcome rather than the steps required to achieve that outcome.

For example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 3
```

This definition describes the desired state:

> Three replicas of the frontend application should be running.

It does not describe how Kubernetes should create the pods.

The responsibility of achieving the desired state belongs to the platform.

Examples of declarative resources include:

- Deployments
- Services
- ConfigMaps
- Ingresses
- Network Policies
- Platform Components

By storing declarative definitions in Git, teams can clearly describe how systems should behave.

---

# 2. Versioned and Immutable

The second principle of GitOps is that the desired state must be stored in a version-controlled system.

Git provides a complete history of every change made to the desired state.

Consider the following sequence:

```text
Version 1
    ↓
Version 2
    ↓
Version 3
    ↓
Version 4
```

Each modification creates a new version of the desired state.

This provides several benefits:

## Auditability

Teams can identify:

- Who made the change
- When the change was made
- What was changed

## Traceability

Every operational change becomes part of the repository history.

## Recoverability

If a change introduces problems, the system can be restored to a previous working version.

For this reason, Git becomes the historical record of the platform.

When discussing immutability in GitOps, it does not mean changes cannot be made.

It means every change creates a new version rather than modifying history without traceability.

---

# 3. Pulled Automatically

Traditional deployment systems typically follow a push-based model.

```text
CI Pipeline
      ↓
Cluster
```

The deployment system pushes changes into the environment.

GitOps follows a pull-based model.

```text
Git Repository
       ↓
GitOps Controller
       ↓
Cluster
```

Software agents running inside the cluster continuously observe the repository.

When changes are detected:

1. The updated desired state is retrieved.
2. The desired state is compared against the actual state.
3. Required changes are applied.

This approach provides several advantages.

## Improved Security

External systems no longer require direct deployment access to production clusters.

## Reduced Credential Exposure

Cluster credentials remain within the platform boundary.

## Clear Separation of Responsibilities

CI systems focus on producing trusted artifacts.

GitOps systems focus on operating the platform.

This separation is one of the primary reasons organizations adopted GitOps.

---

# 4. Continuously Reconciled

The final principle of GitOps is continuous reconciliation.

This is the capability that distinguishes GitOps from traditional deployment workflows.

GitOps continuously compares:

```text
Desired State
      vs
Actual State
```

The objective is simple:

```text
Desired State = Actual State
```

Whenever differences are detected, corrective actions are performed automatically.

Examples include:

## Resource Deletion

Suppose an engineer accidentally deletes a deployment.

```text
Deployment Deleted
```

The desired state still exists in Git.

The GitOps controller detects the drift and restores the deployment.

---

## Configuration Changes

Suppose a resource is manually modified inside the cluster.

```text
Git:
replicas: 3

Cluster:
replicas: 1
```

The GitOps controller identifies the difference and restores the intended state.

---

## Drift Detection

Manual changes, emergency fixes, and configuration updates can cause environments to diverge over time.

Continuous reconciliation ensures that these differences are detected and corrected automatically.

This guarantees that the platform continuously moves toward its intended state.

---

# Why These Principles Matter

Each principle solves a specific operational challenge.

| Principle | Benefit |
|------------|------------|
| Declarative | Defines the intended state of the system |
| Versioned and Immutable | Provides history, auditing, and recovery |
| Pulled Automatically | Improves security and separation of concerns |
| Continuously Reconciled | Detects and corrects drift |

Individually, these principles provide value.

Together, they form the foundation of GitOps.

---

# Key Takeaway

GitOps is not defined by a specific tool.

Whether an organization uses Argo CD, FluxCD, or another implementation, the same principles apply.

A GitOps platform must:

- Define state declaratively
- Store state in a version-controlled system
- Pull changes automatically
- Continuously reconcile desired and actual state

These four principles transform Git from a source code repository into the operational source of truth for the entire platform.
