# RBAC and Access Control

## Introduction

Security is one of the primary concerns of any platform.

As platforms grow, the number of applications, environments, clusters, and teams managing those resources also increases.

Without proper boundaries, platforms become vulnerable to:

```text
Unauthorized Access
Accidental Modifications
Resource Deletion
Privilege Escalation
Operational Risks
```

To prevent these issues, organizations implement security controls and guardrails that define who can access resources and what actions they can perform.

One of the most important security mechanisms in Kubernetes and GitOps platforms is Role-Based Access Control (RBAC).

---

# Security Through Boundaries

Security is fundamentally about defining boundaries.

As organizations scale, multiple teams interact with the platform:

```text
Platform Team
Application Team
Security Team
Operations Team
Developers
```

Each team requires a different level of access.

For example:

```text
Application Team
      ↓
Manage Applications

Platform Team
      ↓
Manage Clusters

Security Team
      ↓
Manage Policies

Developers
      ↓
View Resources
```

Not every user should have administrative access to every component of the platform.

RBAC exists to enforce these boundaries.

---

# Authentication and Authorization

Before discussing RBAC, it is important to understand two foundational security concepts.

---

## Authentication

Authentication answers the question:

```text
Who Are You?
```

Authentication verifies the identity of a user.

Examples include:

```text
GitHub
GitLab
OIDC
LDAP
SSO
Azure AD
Google Workspace
```

Authentication establishes identity.

---

## Authorization

Authorization answers the question:

```text
What Are You Allowed To Do?
```

Examples:

```text
View Resources
Create Resources
Update Resources
Delete Resources
Synchronize Applications
Manage Clusters
```

Authorization determines permissions after identity has been verified.

---

# Kubernetes RBAC

Kubernetes introduced Role-Based Access Control to manage access to cluster resources.

RBAC allows administrators to define:

```text
Who Can Access Resources
Who Can Modify Resources
Who Can Delete Resources
```

Examples of protected resources include:

```text
Pods
Deployments
Secrets
ConfigMaps
Namespaces
Services
```

RBAC is one of the primary security controls used to secure Kubernetes clusters.

---

# Understanding Roles

RBAC is built around roles.

A role defines:

```text
Allowed Actions
```

for a specific set of resources.

Examples:

```text
Read Only
Developer
Operator
Administrator
```

Each role receives a different set of permissions.

---

# Users, Groups and Service Accounts

Permissions can be assigned to:

```text
Users
Groups
Service Accounts
```

This enables organizations to manage permissions at scale.

Instead of assigning permissions to every individual user, permissions can be granted to groups.

Example:

```text
Developers Group
```

receives:

```text
Read
Create
Update
```

permissions.

---

# Limitations of Kubernetes RBAC

Kubernetes RBAC controls access inside the cluster.

It answers:

```text
Who Can Access Kubernetes Resources?
```

However, modern platforms extend beyond Kubernetes.

Organizations must also control:

```text
Who Can Deploy Applications
Who Can Synchronize Applications
Who Can Manage Repositories
Who Can Access Clusters
```

This is where GitOps platforms extend the security model.

---

# How GitOps Extends Security

Traditional deployment workflows often follow:

```text
Developer
      ↓
CI Pipeline
      ↓
Cluster
```

The CI system owns deployment credentials and performs deployments.

As organizations scale, this creates challenges around:

```text
Ownership
Auditability
Access Control
Operational Governance
```

GitOps introduces a different model.

```text
Developer
      ↓
Git Repository
      ↓
Argo CD
      ↓
Kubernetes Cluster
```

The deployment process becomes declarative and security boundaries can be enforced across multiple layers.

---

# Argo CD RBAC

Argo CD extends authorization beyond Kubernetes resources.

Argo CD introduces RBAC for managing:

```text
Applications
Repositories
Clusters
Projects
Deployment Operations
```

This allows organizations to define who can:

```text
View Applications
Create Applications
Delete Applications
Synchronize Applications
Manage Repositories
Manage Clusters
```

without granting direct administrative access to Kubernetes.

---

# Layered Security Model

A mature GitOps platform typically consists of multiple layers of security.

---

## Git Repository Permissions

Controls:

```text
Who Can Commit
Who Can Review
Who Can Merge
```

changes into Git.

---

## Argo CD RBAC

Controls:

```text
Who Can Operate Applications
Who Can Synchronize
Who Can Manage Deployments
```

within the GitOps platform.

---

## Kubernetes RBAC

Controls:

```text
Who Can Access Cluster Resources
Who Can Modify Resources
Who Can Delete Resources
```

inside the cluster.

---

Together these layers create:

```text
Git Security
      +
Argo CD Security
      +
Kubernetes Security
```

forming a complete platform security model.

---

# Benefits of RBAC in GitOps Platforms

RBAC provides several operational and security benefits.

---

## Team Isolation

Different teams receive different permissions.

---

## Controlled Deployments

Only authorized users can deploy applications.

---

## Reduced Operational Risk

Accidental modifications become less likely.

---

## Auditability

Actions can be traced to specific users and groups.

---

## Principle of Least Privilege

Users receive only the permissions required to perform their responsibilities.

---

# Real-World Example

Consider a platform managed by multiple teams.

```text
Platform Team
      ↓
Manage Clusters

Application Team
      ↓
Manage Applications

Developers
      ↓
View Applications

Security Team
      ↓
Manage Security Policies
```

Each team operates within clearly defined boundaries.

This reduces risk while enabling collaboration at scale.

---

# RBAC as a Foundation for GitOps Governance

As GitOps adoption grows, organizations require mechanisms to safely manage:

```text
Applications
Environments
Clusters
Teams
```

RBAC provides the foundation for these security boundaries.

However, controlling users alone is not enough.

Organizations must also control:

```text
Where Applications Can Deploy
What Resources Can Be Managed
Which Clusters Can Be Targeted
```

This is where AppProjects and multi-tenancy become important.

---

# Key Takeaway

Security in GitOps is built around clearly defined operational boundaries.

Kubernetes introduced RBAC to control access to cluster resources, while GitOps platforms such as Argo CD extend authorization controls to applications, repositories, deployment operations, and platform governance.

Together, Git permissions, Argo CD RBAC, and Kubernetes RBAC create a layered security model that enables organizations to safely operate applications, clusters, and teams at scale while maintaining security, auditability, and operational control.
