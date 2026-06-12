# AppProjects and Multi-Tenancy

## Introduction

Applications are the primary deployment unit within Argo CD.

An Application defines:

```text
Source Repository
Destination Cluster
Destination Namespace
```

and acts as the mechanism through which workloads are deployed into Kubernetes.

For small environments, Applications alone are often sufficient.

However, as organizations grow, multiple teams begin sharing the same GitOps platform.

This introduces new challenges around:

```text
Ownership
Security
Governance
Deployment Boundaries
Operational Isolation
```

To address these challenges, Argo CD introduces AppProjects.

---

# Applications Solve Deployment, Not Governance

An Application answers:

```text
What Should Be Deployed?
```

and

```text
Where Should It Be Deployed?
```

However, Applications do not answer:

```text
Who Owns The Deployment?
Who Can Deploy?
Which Repository Can Be Used?
Which Cluster Can Be Targeted?
Which Resources Can Be Managed?
```

As the platform grows, these questions become increasingly important.

---

# The Scaling Problem

Consider an organization with:

```text
Multiple Teams
Multiple Applications
Multiple Repositories
Multiple Clusters
Multiple Environments
```

Without deployment boundaries, any Application may potentially:

```text
Deploy From Any Repository
Deploy To Any Cluster
Deploy To Any Namespace
Manage Any Allowed Resource
```

This creates operational and security risks.

---

# Understanding the Default Project

Every Argo CD installation contains a built-in project called:

```text
default
```

The default project is intentionally permissive.

It allows Applications to operate with very few restrictions.

---

## Uncontrolled Deployment Destinations

Applications can target:

```text
Any Cluster
Any Namespace
```

provided the cluster is registered with Argo CD.

---

## Unrestricted Source Repositories

Applications can deploy from:

```text
Any Git Repository
```

that Argo CD has access to.

---

## No Resource-Level Safety

Applications can manage resources without additional project-level restrictions.

Examples:

```text
Deployment
Service
ConfigMap
Secret
Namespace
```

and potentially other resource types.

---

## Missing Application-Level Boundaries

Multiple Applications operate within the same project.

This creates a shared deployment model without clear ownership boundaries.

---

# Why the Default Project Becomes a Problem

The default project works well for:

```text
Learning
Development
Proof of Concepts
```

However, production environments introduce new requirements.

Example:

```text
Payments Team
Customer Team
Observability Team
Platform Team
```

all sharing the same Argo CD instance.

Without deployment boundaries:

```text
Accidental Deployments
Cross-Team Modifications
Namespace Misuse
Operational Risks
```

become possible.

The platform requires stronger governance.

---

# Introducing AppProjects

To solve these challenges, Argo CD introduces:

```text
AppProject
```

An AppProject acts as a logical boundary around Applications.

Think of AppProjects as:

```text
Application Governance Layer
```

between:

```text
Application
      ↓
Cluster
```

AppProjects introduce ownership, governance, and deployment boundaries.

---

# Repository Boundaries

AppProjects define:

```text
Which Repositories
Can Be Used
```

for Application deployments.

Example:

```text
Payments Project
```

may only deploy from:

```text
payments-git-repository
```

and not from:

```text
customer-git-repository
```

This prevents Applications from consuming resources outside their ownership domain.

---

# Cluster Boundaries

AppProjects define:

```text
Which Clusters
Can Be Targeted
```

Example:

Allowed:

```text
payments-dev
payments-stage
payments-prod
```

Restricted:

```text
customer-prod
```

This ensures Applications remain within approved deployment environments.

---

# Namespace Boundaries

AppProjects define:

```text
Which Namespaces
Can Be Targeted
```

Example:

Allowed:

```text
payments
payments-dev
payments-prod
```

Restricted:

```text
customer
platform-system
```

This prevents Applications from being deployed into unauthorized namespaces.

---

# Resource Boundaries

AppProjects can control which Kubernetes resources Applications are allowed to manage.

Examples:

Allowed:

```text
Deployment
Service
ConfigMap
Secret
```

Restricted:

```text
ClusterRole
ClusterRoleBinding
Namespace
Custom Cluster Resources
```

This creates an additional layer of resource-level safety.

---

# AppProjects and RBAC

RBAC and AppProjects work together but solve different problems.

---

## RBAC Answers

```text
Who Can Perform Actions?
```

Examples:

```text
Create Application
Delete Application
Synchronize Application
View Application
```

---

## AppProjects Answer

```text
Where Can Actions Be Performed?
```

Examples:

```text
Repository Restrictions
Cluster Restrictions
Namespace Restrictions
Resource Restrictions
```

Together they create:

```text
Identity
      +
Boundary
```

for GitOps operations.

---

# Ownership Through AppProjects

One of the most important benefits of AppProjects is ownership.

Consider:

```text
Payments Team
```

The team owns:

```text
Payments Applications
Payments Repositories
Payments Namespaces
Payments Clusters
```

Similarly:

```text
Observability Team
```

owns:

```text
Monitoring Applications
Monitoring Repositories
Monitoring Namespaces
```

Ownership becomes explicit and enforceable.

---

# Understanding Multi-Tenancy

Multi-Tenancy refers to:

```text
Multiple Teams
      ↓
Shared Platform
      ↓
Controlled Isolation
```

Organizations often prefer:

```text
Single Argo CD Instance
```

instead of running separate Argo CD installations for every team.

AppProjects make this possible.

---

# Multi-Tenancy Example

```text
Shared Argo CD
       │
       ├── Payments Project
       │        ↓
       │    Payments Team
       │
       ├── Customer Project
       │        ↓
       │    Customer Team
       │
       ├── Observability Project
       │        ↓
       │    SRE Team
       │
       └── Platform Project
                ↓
           Platform Team
```

Each team operates independently while sharing the same GitOps platform.

---

# Benefits of AppProjects

AppProjects provide:

```text
Deployment Boundaries
Repository Boundaries
Namespace Boundaries
Cluster Boundaries
Resource Boundaries
Ownership
Multi-Tenancy
Governance
```

These capabilities become essential as GitOps adoption grows.

---

# AppProjects Are More Than Security

Although AppProjects improve security, their purpose extends beyond security.

They provide:

```text
Governance
Ownership
Operational Boundaries
Platform Organization
```

They allow organizations to model the platform around teams, products, and environments.

---

# Key Takeaway

Applications are the primary deployment unit within Argo CD, but AppProjects are the primary governance unit.

While Applications define what should be deployed and where it should be deployed, AppProjects define the boundaries within which those deployments can occur.

By introducing repository restrictions, cluster restrictions, namespace restrictions, resource restrictions, ownership, and multi-tenancy, AppProjects enable organizations to safely operate GitOps platforms at enterprise scale while maintaining security, governance, and operational control.
