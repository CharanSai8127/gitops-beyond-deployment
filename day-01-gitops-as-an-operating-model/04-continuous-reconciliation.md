# Continuous Reconciliation

## Introduction

One of the most important concepts in GitOps is continuous reconciliation.

GitOps is often misunderstood as an automated deployment process. In reality, GitOps is built around continuous reconciliation.

Deployments are simply one outcome of reconciliation.

To understand why reconciliation is important in GitOps, we must first understand how Kubernetes operates.

---

# Reconciliation in Kubernetes

Kubernetes is fundamentally a reconciliation-based system.

When a resource is created inside the cluster, Kubernetes controllers continuously work to ensure the resource remains in its intended state.

Consider the following Deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
```

The Deployment Controller continuously observes the state of the cluster and attempts to maintain:

```text
Desired Pods = 3
```

If one pod crashes or is deleted, the controller automatically creates a replacement pod.

```text
Desired Pods = 3
Actual Pods  = 2

Action:
Create Missing Pod
```

This process never stops.

Kubernetes controllers are continuously reconciling the desired state of resources with their actual state in the cluster.

---

# GitOps Extends Reconciliation

GitOps did not invent reconciliation.

Kubernetes already relied on reconciliation long before GitOps tools existed.

GitOps extends the reconciliation model by introducing another layer above Kubernetes resources.

Instead of only reconciling Kubernetes resources, GitOps controllers continuously reconcile:

```text
Desired State In Git
          vs
Actual State In Cluster
```

This creates an additional reconciliation layer.

```text
Git Repository
       │
       ▼
GitOps Controller
       │
       ▼
Kubernetes Resources
       │
       ▼
Kubernetes Controllers
       │
       ▼
Running Workloads
```

The GitOps controller ensures the cluster matches the desired state stored in Git.

The Kubernetes controllers ensure the running workloads match the desired state stored in Kubernetes resources.

Together, these layers create a fully reconciled system.

---

# Why Reconciliation Matters

Modern production environments are constantly changing.

During normal operations:

- Pods restart
- Nodes fail
- Resources scale
- Configurations change
- Engineers make manual modifications

Over time, these changes can cause the running environment to differ from what was originally intended.

This difference is known as drift.

Without continuous reconciliation, environments gradually become inconsistent.

As the number of applications and clusters grows, managing these differences becomes increasingly difficult.

GitOps solves this problem through continuous reconciliation.

---

# The Reconciliation Process

GitOps controllers continuously execute a reconciliation loop.

The process consists of three major steps.

## Step 1: Detect Drift

The controller continuously observes both:

```text
Git Repository
       vs
Cluster State
```

Any difference between the two is considered drift.

Examples include:

- Manual configuration changes
- Missing resources
- Modified resource definitions
- Deleted workloads

The controller continuously monitors for these differences.

---

## Step 2: Compare Desired State with Actual State

After drift is detected, the controller compares:

```text
Desired State
       vs
Actual State
```

The desired state is retrieved from Git.

The actual state is retrieved from the Kubernetes cluster.

Example:

```text
Git Repository

replicas = 3
```

```text
Cluster State

replicas = 1
```

The controller identifies that the current cluster state does not match the intended state.

---

## Step 3: Apply Changes

Once the difference has been identified, the controller applies corrective actions.

```text
Current State
       │
       ▼
Corrective Action
       │
       ▼
Desired State Restored
```

Continuing the previous example:

```text
Desired Replicas = 3
Actual Replicas  = 1
```

The controller updates the Deployment and restores:

```text
Actual Replicas = 3
```

The application is automatically returned to its intended state.

---

# Self-Healing Through Reconciliation

One of the most valuable outcomes of continuous reconciliation is self-healing.

Suppose an engineer manually modifies a Deployment using:

```bash
kubectl edit deployment nginx
```

and changes:

```yaml
replicas: 3
```

to:

```yaml
replicas: 1
```

The cluster state now differs from the desired state stored in Git.

The GitOps controller detects the drift and restores the original configuration.

```text
Git State
replicas = 3

Cluster State
replicas = 1

Action:
Restore replicas = 3
```

This automatic correction is commonly referred to as self-healing.

---

# Continuous Reconciliation vs Deployment

Traditional deployment systems are event-driven.

They perform an action and then stop.

```text
Build
   │
   ▼
Deploy
   │
   ▼
Finished
```

GitOps systems are state-driven.

They continuously observe and enforce the desired state.

```text
Observe
   │
   ▼
Compare
   │
   ▼
Reconcile
   │
   ▼
Repeat
```

The reconciliation loop never ends.

This is one of the most important differences between deployment automation and GitOps.

---

# Key Takeaway

Kubernetes continuously reconciles resources within the cluster.

GitOps extends this model by continuously reconciling the state stored in Git with the state running in the cluster.

The reconciliation process follows three steps:

1. Detect Drift
2. Compare Desired State with Actual State
3. Apply Changes

This continuous reconciliation enables:

- Drift Detection
- Self-Healing
- Operational Consistency
- Automated State Enforcement

Reconciliation is the engine that powers GitOps and allows platforms to continuously maintain their intended state.
