# Environment Promotion Strategies

## Introduction

In the previous chapter, we explored how Kustomize helps manage multiple environments through a combination of Bases and Overlays.

Using Kustomize, we can define a single application and customize it for different environments such as:

```text
Development
Staging
Production
```

However, managing environments is only part of the challenge.

The next question becomes:

> How do we safely move changes from Development to Staging and eventually to Production?

This process is known as Environment Promotion.

Environment promotion is one of the most important operational workflows in modern GitOps platforms because it ensures that changes are validated before reaching production.

---

# Why Environment Promotion Exists

Production environments serve real users and business workloads.

Any change deployed directly to production carries risk.

Consider the following workflow:

```text
Developer
      │
      ▼
Production
```

A single mistake can immediately impact users.

To reduce risk, organizations introduce multiple environments.

```text
Developer
      │
      ▼
Development
      │
      ▼
Staging
      │
      ▼
Production
```

Each environment provides an opportunity to validate changes before they reach production.

This creates a safer and more controlled deployment process.

---

# Understanding Environment Responsibilities

Each environment exists for a specific purpose.

## Development

The Development environment is used for:

```text
Feature Development
Rapid Iteration
Initial Validation
```

This environment changes frequently and is optimized for speed.

---

## Staging

The Staging environment acts as a production-like testing environment.

Its purpose includes:

```text
Integration Testing
User Acceptance Testing
Release Validation
```

Changes are validated here before moving to production.

---

## Production

Production hosts real business workloads.

The primary goals are:

```text
Reliability
Availability
Stability
```

Changes should only reach production after they have been validated in lower environments.

---

# What is Environment Promotion?

Environment promotion is the process of moving a validated change from one environment to the next.

A typical promotion workflow looks like:

```text
Development
      │
      ▼
Staging
      │
      ▼
Production
```

The application itself does not fundamentally change.

What changes is the confidence level associated with the release.

As validation increases, the application moves closer to production.

---

# Traditional Promotion Approaches

Historically, promotion was handled through deployment pipelines.

```text
Build
   │
   ▼
Deploy Dev
   │
   ▼
Deploy Stage
   │
   ▼
Deploy Prod
```

The deployment system controlled how applications moved through environments.

Although this approach works, it introduces several challenges.

### Limited Auditability

Understanding who promoted a change can become difficult.

### Environment Drift

Different environments can gradually diverge from one another.

### Complex Rollbacks

Recovering previous states often requires manual intervention.

### Deployment-Centric Operations

The focus remains on deployment execution rather than desired state management.

---

# GitOps Promotion

GitOps changes the promotion model.

Instead of promoting deployments, GitOps promotes desired state.

```text
Desired State
      │
      ▼
Development
      │
      ▼
Staging
      │
      ▼
Production
```

Git becomes the mechanism through which changes move across environments.

Every environment derives its state from Git.

This provides a consistent and auditable promotion workflow.

---

# Promotion Through Git

A common GitOps workflow looks like:

```text
Change
   │
   ▼
Development
   │
   ▼
Validation
   │
   ▼
Promotion
   │
   ▼
Staging
   │
   ▼
Validation
   │
   ▼
Promotion
   │
   ▼
Production
```

The Git repository becomes the central control point for promotion.

Changes are reviewed, approved, and promoted through Git operations.

This aligns naturally with GitOps principles.

---

# Environment Promotion with Kustomize

Kustomize provides a natural structure for managing promotions.

Consider the following repository:

```text
base/

overlays/
├── dev
├── stage
└── prod
```

The Base defines the common application structure.

Each Overlay defines environment-specific modifications.

A typical promotion workflow becomes:

```text
Apply Change To Dev
         │
         ▼
Validate
         │
         ▼
Promote To Stage
         │
         ▼
Validate
         │
         ▼
Promote To Prod
```

The same application progresses through environments while preserving consistency.

---

# Promotion and ApplicationSets

ApplicationSets automate the deployment of applications across environments.

For example:

```text
base/

overlays/
├── dev
├── stage
└── prod
```

The Git Directory Generator discovers:

```text
dev
stage
prod
```

and creates:

```text
backend-dev
backend-stage
backend-prod
```

Applications automatically.

As changes are promoted through Git, the corresponding Applications reconcile automatically.

No manual deployment steps are required.

The GitOps controller continuously ensures that every environment matches its desired state.

---

# Benefits of Environment Promotion

Environment promotion provides several operational advantages.

## Reduced Risk

Changes are validated before reaching production.

---

## Improved Auditability

Every promotion is tracked through Git history.

Teams can easily determine:

```text
Who Made The Change
What Changed
When It Changed
Why It Changed
```

---

## Easier Rollbacks

If a problem is discovered, a previous Git revision can be restored.

GitOps controllers automatically reconcile the platform back to the desired state.

---

## Environment Consistency

Development, Staging, and Production all follow the same operational workflow.

This reduces drift and improves reliability.

---

## Controlled Releases

Changes move through environments in a predictable and governed manner.

This improves operational confidence.

---

# Promotion Strategies

Organizations implement promotion using different approaches.

## Directory-Based Promotion

```text
overlays/
├── dev
├── stage
└── prod
```

Changes are promoted between environment directories.

---

## Branch-Based Promotion

```text
dev branch
      │
      ▼
stage branch
      │
      ▼
prod branch
```

Each branch represents a specific environment.

---

## Pull Request-Based Promotion

```text
Change
   │
   ▼
Pull Request
   │
   ▼
Review
   │
   ▼
Approval
   │
   ▼
Promotion
```

This approach provides strong governance and auditability.

---

# Bringing Everything Together

By combining the concepts introduced throughout Day 2, a scalable GitOps platform emerges.

```text
Repository Architecture
          │
          ▼
App of Apps
          │
          ▼
ApplicationSets
          │
          ▼
Kustomize
          │
          ▼
Environment Promotion
```

Each component solves a specific operational challenge.

Together, they provide a complete platform management workflow.

---

# Key Takeaway

Environment promotion is the process of moving validated changes through Development, Staging, and Production environments.

Traditional deployment systems promote deployments.

GitOps promotes desired state.

By combining Kustomize Overlays, ApplicationSets, and Git-based workflows, platform teams can safely move changes through environments while maintaining consistency, auditability, and operational control.

This completes the GitOps platform structure introduced in Day 2 and establishes the foundation for operating GitOps platforms at scale.
