# Desired State Management

## Introduction

At the heart of GitOps lies a simple but powerful concept:

> Desired State

GitOps is not primarily concerned with deployments.

GitOps is concerned with continuously maintaining the desired state of a system.

Every operational activity performed by a GitOps platform revolves around comparing what should exist with what actually exists.

Understanding desired state is essential to understanding GitOps.

---

# Desired State vs Actual State

A GitOps workflow operates using two states.

## Desired State

Desired state represents how the system should look.

Examples include:

- Number of application replicas
- Container images
- Services
- Ingress resources
- ConfigMaps
- Secrets references
- Platform components

Desired state is defined by engineers and stored in Git.

For example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 3
```

The above definition expresses the desired state.

The expectation is:

> Three frontend pods should be running.

---

## Actual State

Actual state represents what is currently running inside the cluster.

Examples:

```text
Desired State: 3 Replicas

Actual State: 3 Replicas
```

or

```text
Desired State: 3 Replicas

Actual State: 1 Replica
```

Whenever the actual state differs from the desired state, drift occurs.

GitOps continuously attempts to eliminate this drift.

The ultimate goal is:

```text
Desired State = Actual State
```

---

# Why Desired State Must Live in Git

Git serves as the single source of truth in a GitOps workflow.

Storing desired state in Git provides several advantages.

## Collaboration

Multiple engineers can contribute through pull requests and reviews.

Changes become visible to the entire team before reaching production.

---

## Ownership

Every change is associated with a specific commit and author.

Teams can easily determine:

- Who made the change
- When it was made
- Why it was made

This creates clear ownership and accountability.

---

## Traceability

Git maintains a complete history of desired state changes.

Every modification becomes part of the operational history of the platform.

---

# Version History and Recoverability

One of the most valuable characteristics of storing desired state in Git is recoverability.

Every change creates a new version of the desired state.

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

Suppose Version 4 introduces a configuration change that breaks the application.

The team can simply restore a previous version:

```text
Version 4
      ↓
Rollback
      ↓
Version 3
```

Because the desired state is versioned, recovery becomes straightforward.

The objective is not simply to deploy changes.

The objective is to reliably restore known working states whenever necessary.

---

# Consistency Across Environments

Modern organizations typically operate multiple environments.

Examples include:

```text
Development
      ↓
Staging
      ↓
Production
```

Maintaining consistency across these environments is often challenging.

GitOps addresses this problem by ensuring that every environment is derived from version-controlled definitions.

Whether using:

- Kustomize
- Helm
- ApplicationSets
- Environment overlays

the desired state remains stored in Git.

This creates a consistent operational model across teams and environments.

Rather than manually configuring environments, Git becomes the authoritative definition of how each environment should behave.

---

# How Desired State Drives Operations

Traditional deployment systems focus on executing actions.

Examples include:

```text
Deploy
Update
Patch
Restart
```

GitOps shifts the focus from actions to outcomes.

Instead of asking:

> What operation should we perform?

GitOps asks:

> What should the system look like?

Engineers modify the desired state stored in Git.

The GitOps controller continuously observes:

```text
Desired State
      vs
Actual State
```

When differences are detected:

- Drift is identified
- Changes are applied
- State is reconciled

Operations become driven by desired state rather than manual execution.

This is one of the most important differences between traditional deployment workflows and GitOps workflows.

---

# Key Takeaway

Desired state is the foundation of GitOps.

Git stores the intended state of the system.

The cluster represents the actual state of the system.

GitOps continuously compares these states and reconciles any differences.

By storing desired state in Git, organizations gain:

- Collaboration
- Ownership
- Traceability
- Recoverability
- Consistency

GitOps is ultimately the practice of continuously enforcing desired state across the platform.
