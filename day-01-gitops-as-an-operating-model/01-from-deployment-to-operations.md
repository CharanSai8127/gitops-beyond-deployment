# From Deployment to Operations

## Introduction

A secure and fully validated artifact does not guarantee a healthy production system.

Modern CI pipelines are capable of performing extensive validation before an application reaches production. They can build artifacts, execute tests, perform static analysis, validate dependencies, and scan for vulnerabilities.

However, even a fully validated artifact can still create issues in production.

Applications can fail because of:

- Incorrect configurations
- Infrastructure changes
- Deployment failures
- Dependency mismatches
- Environment inconsistencies
- Manual modifications to running systems

As Kubernetes adoption increased, organizations realized that deploying software and operating software are two different concerns.

This realization led to the adoption of GitOps.

---

# Traditional CI/CD Deployments

Traditionally, CI/CD systems were responsible for both building and deploying applications.

A common workflow looked like:

```text
Developer
    │
    ▼
Source Code
    │
    ▼
Build
    │
    ▼
Test
    │
    ▼
Security Scan
    │
    ▼
Package
    │
    ▼
Deploy
    │
    ▼
Cluster
```

The deployment pipeline was responsible for:

- Building the application
- Running tests
- Validating security
- Creating deployment artifacts
- Deploying workloads

Once deployment completed successfully, the responsibility of the pipeline ended.

The assumption was simple:

> If deployment succeeds, the application is running successfully.

For modern Kubernetes platforms, this assumption is often incorrect.

---

# CI and CD Solve Different Problems

As systems became increasingly distributed and cloud-native, organizations began separating Continuous Integration from Continuous Deployment.

The primary responsibility of a CI system is to validate artifacts before they reach production.

A CI pipeline answers questions such as:

- Does the application build successfully?
- Do automated tests pass?
- Are dependencies secure?
- Are vulnerabilities detected?
- Is the artifact ready for release?

The output of CI is a trusted artifact.

A CI system should focus on:

```text
Build
Test
Validate
Scan
Package
Publish
```

Deploying and operating applications introduces a completely different set of concerns.

---

# Security Challenges in Deployment Workflows

When a CI tool is responsible for deploying applications, it requires access to the target environment.

For Kubernetes deployments, the CI platform must be authenticated and authorized to interact with the cluster.

A typical deployment workflow looks like:

```text
Developer
    │
    ▼
CI Pipeline
    │
    ▼
Kubernetes API
    │
    ▼
Cluster
```

This introduces several security concerns.

## Cluster Credentials

The CI platform requires credentials capable of interacting with the cluster.

These credentials become highly sensitive because they can directly modify production resources.

## Expanded Attack Surface

Compromising the CI platform may provide an attacker with access to production environments.

The deployment system becomes part of the critical security boundary.

## Ownership Challenges

When deployments are executed through a shared CI account, identifying ownership becomes difficult.

From the cluster's perspective, the change is often performed by:

```text
ci-user
```

rather than the individual engineer who initiated the change.

This makes auditing and accountability more difficult as organizations scale.

---

# Operational Challenges

Deploying an application is only a single event.

Operating an application is a continuous process.

Kubernetes is designed around reconciliation.

When a Deployment specifies:

```yaml
replicas: 3
```

Kubernetes continuously attempts to maintain that state.

Controllers constantly compare:

```text
Desired State
      vs
Actual State
```

and take corrective action whenever differences are detected.

The cluster continues operating long after a deployment has completed.

During normal operation:

- Pods restart
- Nodes fail
- Resources scale
- Configurations change
- Dependencies evolve

The deployment pipeline has no responsibility for managing these ongoing operational concerns.

Its job ended when deployment succeeded.

---

# Why Deployment Alone Is Insufficient

Deployment answers an important question:

> How do changes reach production?

However, modern platforms require an answer to a different question:

> How do we continuously ensure production remains in the desired state?

Consider the following scenario:

```text
Deploy Application
       │
       ▼
Deployment Successful
       │
       ▼
Manual Change
       │
       ▼
Configuration Drift
```

The deployment succeeded.

The running system is now different from what was originally intended.

Over time, environments begin to drift from their expected configuration.

Traditional deployment systems are not designed to continuously detect and correct this drift.

Organizations needed a model that could continuously manage operational state rather than simply execute deployments.

---

# The Emergence of GitOps

GitOps emerged to address these operational challenges.

GitOps is an operational model that uses Git as the single source of truth for managing the lifecycle of infrastructure and applications.

Rather than treating deployment as the final objective, GitOps focuses on continuously maintaining the desired state of a system.

The desired state is defined in Git.

Software agents running inside the cluster continuously compare:

```text
OAOAOADesired State
      vs
Actual State
```

When differences are detected, corrective actions are taken automatically to restore the intended state.

This approach provides several benefits:

## Single Source of Truth

Git becomes the authoritative definition of the system.

## Drift Detection

Differences between desired and actual state are detected automatically.

## Improved Security

Deployment credentials are no longer required inside CI systems.

## Auditability

Every change is recorded and traceable through Git history.

## Ownership

Changes are associated with Git commits and pull requests rather than shared deployment accounts.

## Consistency

All environments are managed using the same operational workflow.

---

# Key Takeaway

Continuous Integration and GitOps solve different operational problems.

Continuous Integration focuses on producing trusted and validated artifacts.

GitOps focuses on continuously operating systems using those artifacts.

CI answers:

> Is this artifact safe to release?

GitOps answers:

> Is the running system continuously matching its intended state?

This separation of responsibilities is one of the primary reasons organizations adopted GitOps as their preferred operating model for Kubernetes platforms.
