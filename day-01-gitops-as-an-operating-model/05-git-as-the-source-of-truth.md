# Git as the Source of Truth

## Introduction

Throughout this chapter, we have explored why GitOps emerged, how desired state is managed, the principles that define GitOps, and how continuous reconciliation enables platforms to maintain consistency.

This leads to one important question:

> Where should the desired state of a system live?

GitOps answers this question with a simple principle:

> Git is the single source of truth.

In GitOps, Git is not merely a location where Kubernetes manifests are stored.

Git becomes the authoritative definition of how applications and infrastructure should exist and operate.

The cluster is no longer the source of truth.

Git is.

---

# Why Git?

Git was originally designed as a version control system for software development.

However, Git provides several characteristics that make it an ideal platform for managing desired state.

These characteristics include:

- Version Control
- Change Tracking
- Collaboration
- Auditability
- Recoverability
- Consistency

Together, these capabilities allow Git to become the operational database of a GitOps platform.

---

# Version Control

Every modification made to a Git repository creates a new version of the desired state.

Consider the following example:

Version 1:

```yaml
replicas: 3
```

Version 2:

```yaml
replicas: 5
```

Both versions are preserved within the repository.

This allows teams to understand how the platform has evolved over time.

Every state transition becomes part of the platform history.

---

# Change Tracking

One of the biggest operational challenges in traditional environments is understanding who changed what.

Git provides complete visibility into every modification.

Each commit records:

```text
Who made the change
What was changed
When the change occurred
Why the change was made
```

Combined with pull requests and code reviews, Git creates a transparent change management process.

Instead of relying on manual documentation, the platform history is maintained automatically.

---

# Collaboration

Modern platforms are rarely managed by a single engineer.

Application teams, platform teams, security teams, and operations teams often work on the same environment.

Git enables collaboration through:

- Branching
- Pull Requests
- Reviews
- Approval Workflows

Multiple teams can safely contribute changes while maintaining governance and control.

This becomes increasingly important as platform complexity grows.

---

# Recoverability

Failures are inevitable in production systems.

Applications can become unstable.

Configurations can be misconfigured.

Platform upgrades can introduce unexpected issues.

Without version control, restoring a previous working state can be difficult and error-prone.

Git provides a complete history of the desired state.

If an issue occurs, teams can restore a known working version.

For example:

Current version:

```yaml
replicas: 1
```

Previous version:

```yaml
replicas: 3
```

Restoring the previous version becomes a Git operation rather than a manual modification inside the cluster.

Once Git is updated, the GitOps controller automatically reconciles the cluster back to the desired state.

---

# Git as the Operational Database

One of the most important mindset shifts in GitOps is understanding that Git is not simply a repository of configuration files.

Git becomes the operational database of the platform.

Traditional operations often treat the cluster as the source of truth.

```text
Cluster
   │
   ▼
Source of Truth
```

This approach creates challenges when manual changes occur.

Over time, the cluster state may differ from documentation, repositories, or deployment definitions.

GitOps reverses this model.

```text
Git
   │
   ▼
Source of Truth
```

The cluster becomes a realization of the desired state stored in Git.

If there is ever a disagreement between Git and the cluster, Git wins.

This principle is fundamental to GitOps.

---

# What Should Be Stored in Git?

GitOps is not limited to application deployments.

Anything required to operate the platform should be represented in Git.

Examples include:

## Applications

```text
Deployments
Services
ConfigMaps
Secrets References
Ingresses
HTTPRoutes
```

## Platform Components

```text
Argo CD
cert-manager
Prometheus
Grafana
Gateway API
Vault
CSI Drivers
```

## Security Policies

```text
RBAC
Network Policies
Resource Quotas
```

## Environment Configurations

```text
Development
Staging
Production
```

The objective is simple:

> If the platform is expected to exist, its desired state should exist in Git.

---

# Consistency Across Environments

Git enables organizations to manage multiple environments using the same operational workflow.

For example:

```text
Git
 │
 ├── Dev
 ├── Stage
 └── Prod
```

Each environment is derived from version-controlled configuration.

This ensures:

- Consistent deployments
- Repeatable operations
- Standardized platform management

As organizations scale to multiple clusters and environments, this consistency becomes increasingly valuable.

---

# What Happens When Git Is Not the Source of Truth?

Consider the following situation:

```text
Git Repository
      │
      ▼
replicas = 3
```

```text
Cluster
      │
      ▼
replicas = 1
```

Now two different versions of reality exist.

Questions immediately arise:

- Which version is correct?
- Who made the change?
- Should the change be preserved?
- How should future deployments behave?

Without a clear source of truth, operational complexity increases rapidly.

GitOps eliminates this ambiguity by establishing Git as the authoritative definition of the system.

---

# Key Takeaway

GitOps is not about storing Kubernetes manifests in a repository.

GitOps is about treating Git as the authoritative definition of platform state.

Git provides:

- Version Control
- Change Tracking
- Collaboration
- Recoverability
- Auditability
- Consistency

The desired state of applications and infrastructure is stored in Git.

GitOps controllers continuously reconcile the cluster to match that desired state.

When conflicts occur between Git and the cluster:

```text
Git Wins
```

This principle is what allows GitOps platforms to maintain consistency, recoverability, and operational control at scale.

---

## What's Next?

In Day 1, we learned:

- Why GitOps emerged
- How desired state is managed
- The four principles of GitOps
- How continuous reconciliation works
- Why Git becomes the source of truth

In Day 2, we will move from concepts to implementation and explore how GitOps platforms are structured using repositories, Kustomize overlays, ApplicationSets, and the App-of-Apps pattern.
