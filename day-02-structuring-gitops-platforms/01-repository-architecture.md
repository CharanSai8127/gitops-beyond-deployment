# Repository Architecture

## Introduction

In Day 1, we learned that Git acts as the single source of truth in a GitOps operating model.

The desired state of applications and infrastructure is stored in Git and continuously reconciled with the actual state running inside the cluster.

However, adopting Git as the source of truth introduces a new challenge:

> How should the desired state be organized?

As platforms grow, Git no longer stores only application manifests.

It becomes responsible for managing:

- Applications
- Platform Components
- Infrastructure Definitions
- Security Policies
- Environment Configurations
- Operational Workflows

Without a well-defined repository structure, managing platform state quickly becomes difficult.

Repository architecture becomes a critical design decision in GitOps.

---

# Platform Operations Involve Multiple Teams

A common misconception is that only application teams interact with GitOps repositories.

In reality, modern platforms involve multiple teams working together.

```text
Application Team
Platform Team
Security Team
Infrastructure Team
Operations Team
```

Each team contributes to a different part of the platform.

### Application Team

Responsible for:

```text
Deployments
Services
Application Configuration
Release Management
```

### Platform Team

Responsible for:

```text
Argo CD
cert-manager
Gateway API
Prometheus
Grafana
Vault
```

### Security Team

Responsible for:

```text
RBAC
Network Policies
Compliance Controls
Security Standards
```

### Infrastructure Team

Responsible for:

```text
Clusters
Networking
Storage
IAM
Cloud Resources
```

Because multiple teams contribute to the same platform, ownership becomes an important consideration.

Without proper repository organization, teams begin modifying shared resources, operational boundaries become unclear, and managing the platform becomes increasingly difficult.

---

# The Monorepo Approach

One approach to organizing GitOps repositories is the Monorepo model.

In a Monorepo, all platform-related resources are stored within a single repository.

Example:

```text
gitops-platform/

├── applications/
├── platform/
├── policies/
├── infrastructure/
└── environments/
```

Everything required to operate the platform exists in one location.

This includes:

- Applications
- Platform Services
- Security Policies
- Environment Configurations

### Benefits of a Monorepo

#### Simplicity

Teams only need to manage a single repository.

#### Easy Bootstrap

A new platform can be deployed from a single source.

#### Centralized Visibility

All platform components are visible in one location.

#### Easier Change Coordination

Changes affecting multiple components can be managed through a single pull request.

---

# Challenges of a Monorepo

As organizations grow, monorepos can introduce operational challenges.

### Shared Ownership

Multiple teams work within the same repository.

### Permission Complexity

Different teams often require different levels of access.

### Larger Review Processes

Changes become harder to review as repository size increases.

### Repository Growth

The repository grows as more applications, environments, and platform services are added.

For smaller organizations, these challenges are often manageable.

For larger organizations, they become increasingly significant.

---

# The Polyrepo Approach

An alternative approach is the Polyrepo model.

In a Polyrepo architecture, responsibilities are separated across multiple repositories.

Example:

```text
platform-gitops/
security-gitops/
application-gitops/
infrastructure-gitops/
```

Each repository is owned by a specific team or domain.

### Example Ownership Model

Platform Team:

```text
platform-gitops
```

Security Team:

```text
security-gitops
```

Infrastructure Team:

```text
infrastructure-gitops
```

Application Team:

```text
application-gitops
```

This creates clear ownership boundaries.

---

# Benefits of a Polyrepo

### Clear Ownership

Each team owns its own repository.

### Better Access Control

Permissions can be applied at the repository level.

### Independent Lifecycles

Teams can evolve independently without impacting other domains.

### Improved Governance

Changes are reviewed by the teams responsible for those resources.

---

# Challenges of a Polyrepo

Although Polyrepo architectures improve ownership, they introduce new challenges.

### Repository Sprawl

The number of repositories increases significantly.

### Cross-Team Coordination

Changes spanning multiple domains may require updates across multiple repositories.

### Operational Complexity

Managing repository relationships becomes more difficult as the platform grows.

---

# The Hybrid Approach

Most modern platform teams adopt a Hybrid approach.

The Hybrid model combines the advantages of both Monorepo and Polyrepo architectures.

A common example looks like:

```text
terraform-platform/
        │
        ▼
gitops-platform/
        │
        ├── platform-components/
        ├── applicationsets/
        ├── environments/
        └── applications/
```

Application source code remains separate:

```text
backend-repository/
frontend-repository/
```

Infrastructure is managed separately:

```text
terraform-platform/
```

Platform operations are managed through GitOps:

```text
gitops-platform/
```

This model provides:

- Clear ownership
- Better security boundaries
- Easier platform management
- Reduced repository sprawl

The Hybrid approach has become the preferred model for many Kubernetes platform teams.

---

# Choosing the Right Architecture

There is no universal repository structure that fits every organization.

Smaller teams often prioritize simplicity and choose a Monorepo approach.

Larger organizations typically prioritize ownership and governance and adopt a Polyrepo approach.

Most modern Kubernetes platforms balance both requirements through a Hybrid architecture.

The repository structure should reflect:

- Team ownership
- Operational boundaries
- Security requirements
- Platform scale

Repository architecture is ultimately an organizational decision as much as it is a technical one.

---

# Key Takeaway

GitOps is not only about managing desired state.

It is also about managing ownership of that desired state.

As platforms grow, multiple teams become responsible for applications, infrastructure, security, and platform operations.

A well-designed repository architecture helps establish clear ownership, enforce security boundaries, and simplify platform management.

Monorepo, Polyrepo, and Hybrid architectures each address these challenges differently.

Today, most platform engineering teams adopt a Hybrid model that balances operational simplicity with scalable ownership and governance.

---

## What's Next?

Now that we understand how GitOps repositories are structured, the next question becomes:

> How do we manage all of these repositories, applications, and platform components from a single control plane?

In the next chapter, we will explore the **App of Apps Pattern**, a GitOps design pattern that enables Argo CD to manage entire platforms through a single parent application.
