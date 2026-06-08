# GitOps Beyond Deployment

Most GitOps discussions focus on deploying applications.

Push code.
Sync manifests.
Deploy to Kubernetes.

But production GitOps is much more than application deployment.

GitOps is an operating model that uses Git as the single source of truth for managing platform state. By continuously reconciling the desired state stored in Git with the actual state running in Kubernetes, GitOps enables consistency, governance, security, auditability, and recovery at scale.

This repository explores GitOps from a platform engineering perspective.

Across five days, we move beyond deployment pipelines and examine how modern platforms are operated through desired state management, reconciliation loops, repository architecture, drift detection, platform lifecycle management, and disaster recovery.

## Learning Path

### Day 1 — GitOps Is an Operating Model

Understanding desired state, reconciliation, and Git as the source of truth.

### Day 2 — Repository Design Determines Scalability

Designing repositories and ownership models that scale across teams and environments.

### Day 3 — Drift Is the Real Enemy

Understanding configuration drift, self-healing systems, and operational consistency.

### Day 4 — GitOps for Platform Components

Managing applications, networking, security, observability, and platform services through GitOps.

### Day 5 — Disaster Recovery Starts in Git

Using Git as the foundation for platform restoration, recovery, and operational resilience.

## Key Themes

* Git as the Source of Truth
* Desired State Management
* Reconciliation Loops
* Drift Detection
* Platform Operations
* Governance and Auditability
* Security and Compliance
* Disaster Recovery

If Terraform provisions the platform, GitOps operates the platform.
