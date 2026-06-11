# Sync Policies and Automation

## Introduction

In the previous chapter, we learned how GitOps extends Kubernetes reconciliation by continuously comparing the desired state stored in Git with the actual state running inside the cluster.

The reconciliation loop continuously:

```text
Observe
    ↓
Compare
    ↓
Detect Drift
```

However, detecting drift is only part of the process.

The next question becomes:

> Once drift is detected, how should the cluster be brought back to the desired state?

This is the responsibility of Sync Policies.

Reconciliation identifies the difference.

Sync Policies determine how that difference should be corrected.

---

# Understanding Synchronization

Synchronization is the process of comparing:

```text
Desired State (Git)
         VS
Actual State (Cluster)
```

and applying the changes required to make them match.

The purpose of synchronization is simple:

```text
Desired State
      =
Actual State
```

Synchronization focuses on state consistency.

It ensures that the cluster reflects the state declared within Git.

---

# Synchronization and Operational Drift

Operational drift occurs whenever the live cluster state differs from the desired state stored in Git.

Examples include:

```text
Manual Scaling
Manual Configuration Changes
Resource Deletion
```

For example:

Git:

```text
Replicas = 3
```

Cluster:

```text
Replicas = 10
```

The cluster has drifted away from the desired state.

Synchronization is responsible for correcting this drift.

---

# Reconciliation Interval

GitOps controllers continuously monitor the cluster.

The reconciliation process runs periodically.

A common default interval is:

```text
3 Minutes
```

During each reconciliation cycle:

```text
Observe
    ↓
Compare
    ↓
Detect Drift
    ↓
Synchronize
```

The controller determines whether corrective action is required.

This continuous process ensures that the cluster remains aligned with Git.

---

# Manual Synchronization

One approach is Manual Synchronization.

Workflow:

```text
Detect Drift
      ↓
OutOfSync
      ↓
Wait For Operator
      ↓
Manual Sync
```

In this model, Argo CD detects drift but does not automatically apply changes.

An operator decides when synchronization should occur.

### Advantages

```text
Greater Control
Explicit Approval
Safer Production Changes
```

### Disadvantages

```text
Slower Recovery
Additional Operational Effort
```

Manual synchronization is often used in highly controlled production environments.

---

# Automated Synchronization

Automated Synchronization allows Argo CD to automatically apply changes when drift is detected.

Workflow:

```text
Detect Drift
      ↓
Automatic Sync
      ↓
Restore Desired State
```

In this model, Git becomes the deployment trigger.

Changes committed to Git automatically propagate to the cluster.

### Advantages

```text
Reduced Manual Intervention
Faster Recovery
Fully Git Driven Operations
```

### Disadvantages

```text
Reduced Human Control
Potentially Faster Propagation Of Mistakes
```

Automated synchronization is commonly used for platform components and lower environments.

---

# Self-Heal

Self-Heal extends Automated Synchronization.

Its purpose is to automatically revert unauthorized changes made directly to the cluster.

Example:

Git:

```text
Replicas = 3
```

Cluster:

```text
Replicas = 10
```

caused by:

```text
kubectl scale deployment
```

The Self-Heal workflow becomes:

```text
Detect Drift
      ↓
Restore Desired State
      ↓
Replicas = 3
```

This ensures that the cluster continuously converges toward the desired state stored in Git.

---

# Prune

Prune handles stale resources.

Consider the following scenario:

Git:

```text
Service Removed
```

Cluster:

```text
Service Still Exists
```

Without Prune:

```text
Orphaned Resource
```

remains within the cluster.

With Prune enabled:

```text
Resource Removed From Git
            ↓
Resource Removed From Cluster
```

Prune helps maintain consistency by removing resources that are no longer defined within the desired state.

---

# Understanding Sync Status

One of the most misunderstood concepts in GitOps is Sync Status.

Sync Status answers the question:

> Does the live state match the desired state stored in Git?

Examples include:

```text
Synced
OutOfSync
Unknown
```

A resource is considered:

```text
Synced
```

when the desired state has been successfully applied.

---

# Understanding Health Status

Health Status answers a different question.

> Is the application actually healthy?

Examples include:

```text
Healthy
Progressing
Degraded
Missing
```

Health Status evaluates the operational state of resources after synchronization.

---

# Sync Status vs Health Status

These concepts are related but fundamentally different.

### Sync Status

Answers:

```text
Was Git Applied Successfully?
```

---

### Health Status

Answers:

```text
Are The Resources Functioning Correctly?
```

Consider the following example:

```text
Deployment Applied Successfully
```

Sync Status:

```text
Synced
```

However:

```text
CrashLoopBackOff
```

may still exist.

Health Status:

```text
Degraded
```

The desired state was applied successfully, but the application itself is unhealthy.

This distinction is critical when operating GitOps platforms.

---

# What Sync Success Actually Means

Many engineers incorrectly assume that:

```text
Sync Successful
```

means:

```text
Application Working Correctly
```

This is not true.

A successful synchronization only guarantees:

```text
Desired State
       =
Applied State
```

It does not guarantee:

```text
Application Availability
Business Logic Correctness
Successful User Requests
```

GitOps guarantees consistency.

It does not guarantee correctness.

---

# Bringing Everything Together

The complete workflow can be visualized as:

```text
Git
 │
 ▼
Desired State
 │
 ▼
Reconciliation
 │
 ▼
Detect Drift
 │
 ▼
Sync Policy
 │
 ▼
Synchronization
 │
 ▼
Cluster State
```

After synchronization:

```text
Sync Status
```

determines whether Git and the cluster match.

While:

```text
Health Status
```

determines whether the application is functioning correctly.

Both are required to successfully operate GitOps platforms.

---

# Key Takeaway

Reconciliation detects drift, while Sync Policies determine how drift should be corrected.

Synchronization ensures that the live state of the cluster matches the desired state stored in Git.

Features such as Automated Sync, Self-Heal, and Prune help maintain consistency and automatically recover from operational drift.

However, synchronization alone does not guarantee application health.

Sync Status confirms that Git has been applied successfully, while Health Status confirms whether the resulting application is healthy and operational.

Understanding the distinction between these two concepts is essential for operating GitOps platforms effectively.
