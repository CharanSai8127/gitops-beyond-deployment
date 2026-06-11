# Drift Detection and Self-Healing

## Introduction

One of the primary promises of GitOps is maintaining consistency between the desired state stored in Git and the actual state running inside the cluster.

However, synchronization is not a one-time activity.

Even after a successful deployment, changes can occur within the cluster that cause the running state to diverge from the desired state.

This divergence is known as drift.

Drift Detection and Self-Healing are the mechanisms that allow GitOps platforms to continuously identify and correct these differences.

---

# Understanding Drift

Drift occurs whenever:

```text
Desired State
       ≠
Actual State
```

In a GitOps model:

```text
Desired State
```

is stored in Git.

```text
Actual State
```

is what currently exists in the Kubernetes cluster.

Whenever these states differ, drift exists.

---

# Why Drift Happens

Even after a successful synchronization, the cluster is not static.

Resources may be modified by operators, controllers, administrators, or emergency operational activities.

Common sources of drift include:

```text
Manual Modifications
Resource Deletion
Version Changes
Operator Actions
Emergency Fixes
```

---

# Manual Modifications

One of the most common forms of drift occurs when resources are modified directly.

Examples:

```bash
kubectl scale deployment backend --replicas=10
```

or

```bash
kubectl edit deployment backend
```

Git may contain:

```text
Replicas = 3
```

while the cluster now contains:

```text
Replicas = 10
```

The cluster has drifted away from the desired state.

---

# Resource Deletion

Resources can also be deleted manually.

Example:

```bash
kubectl delete deployment backend
```

Git still contains:

```text
Deployment Exists
```

but the cluster contains:

```text
Deployment Missing
```

This creates resource drift.

---

# Understanding Operator-Induced Drift

Not all drift is caused by humans.

Many Kubernetes operators continuously modify resources as part of their normal reconciliation process.

Examples include:

```text
cert-manager
external-secrets
vault-operator
prometheus-operator
```

These operators are themselves reconciliation systems.

Consider cert-manager:

```text
Certificate
       ↓
cert-manager
       ↓
Secret
```

The Secret is continuously updated by the operator.

Similarly:

```text
ExternalSecret
        ↓
external-secrets
        ↓
Secret
```

The generated Secret changes whenever the external secret source changes.

Argo CD may detect:

```text
Git
 ≠
Cluster
```

even though the change is expected.

This type of drift is often referred to as:

```text
Controller-Induced Drift
```

or

```text
Managed Drift
```

because another controller owns and manages the resource.

---

# Understanding Emergency Fixes

Another common source of drift occurs during production incidents.

Consider a critical outage.

An engineer may temporarily apply a direct fix:

```bash
kubectl set image deployment/backend backend=v1.2.1-hotfix
```

or

```bash
kubectl edit deployment backend
```

The service becomes available again.

However Git still contains:

```text
backend:v1.2.0
```

while the cluster contains:

```text
backend:v1.2.1-hotfix
```

Drift now exists.

These changes are often referred to as:

```text
Hotfixes
```

or

```text
Emergency Fixes
```

because they are applied directly to the cluster to restore service quickly.

---

# Types of Drift

Drift can occur in several forms.

---

## Configuration Drift

The resource exists but its configuration differs.

Example:

```text
CPU
Memory
Replicas
Environment Variables
```

have been modified.

---

## Resource Drift

The resource itself differs.

Examples:

```text
Resource Deleted
Resource Created
Resource Missing
```

---

## Version Drift

The application version differs between Git and the cluster.

Example:

Git:

```text
backend:v1.0
```

Cluster:

```text
backend:v1.1
```

---

## Controller-Induced Drift

Changes introduced by operators.

Examples:

```text
cert-manager
external-secrets
vault-operator
```

These changes are often expected and intentional.

---

## Infrastructure Drift

Changes made to platform infrastructure.

Examples:

```text
Storage Classes
Network Policies
Load Balancers
Cloud Resources
```

These changes may occur outside normal GitOps workflows.

---

# Understanding Drift Detection

Drift Detection is the process of continuously comparing:

```text
Git State
      VS
Cluster State
```

The reconciliation workflow becomes:

```text
Observe
    ↓
Compare
    ↓
Detect Drift
```

When differences are found, Argo CD marks resources as:

```text
OutOfSync
```

This provides visibility into configuration changes occurring within the platform.

---

# Understanding Self-Healing

Self-Healing is the ability of Argo CD to automatically restore resources back to the desired state stored in Git.

Workflow:

```text
Detect Drift
      ↓
Synchronize
      ↓
Restore Desired State
```

The cluster is automatically returned to the state declared in Git.

---

# Self-Healing Example

Git:

```text
Replicas = 3
```

Cluster:

```text
Replicas = 10
```

Argo CD detects drift.

Self-Healing performs:

```text
Replicas = 3
```

restoring the desired state.

---

# Benefits of Self-Healing

Self-Healing provides several advantages.

### Consistency

Git remains the source of truth.

---

### Reliability

Clusters continuously converge toward the desired state.

---

### Reduced Human Error

Unauthorized modifications are corrected automatically.

---

### Security

Unexpected configuration changes are removed.

---

### Operational Stability

The platform behaves predictably.

---

# Why Self-Healing Is Not Always Desirable

Although Self-Healing is powerful, it is not always appropriate.

Production environments often require careful consideration.

---

## Incident Response

During a production outage, engineers may apply temporary fixes directly to the cluster.

Example:

```bash
kubectl edit deployment backend
```

If Self-Healing is enabled:

```text
Detect Drift
      ↓
Revert Change
```

The temporary fix disappears.

The outage may return.

---

## Live Troubleshooting

During debugging, engineers may temporarily change resources.

Example:

```bash
kubectl scale deployment backend --replicas=10
```

to investigate application behavior.

Self-Healing may immediately restore:

```text
Replicas = 3
```

making troubleshooting difficult.

---

## Operator-Owned Resources

Operators continuously modify resources they own.

Examples:

```text
cert-manager
external-secrets
vault-operator
```

Aggressive Self-Healing can create reconciliation conflicts between Argo CD and operators.

---

## Progressive Delivery Platforms

Systems such as:

```text
Argo Rollouts
Flagger
```

modify resources dynamically during deployments.

Self-Healing may interfere with these deployment strategies if not configured correctly.

---

# Drift Detection vs Self-Healing

These concepts solve different problems.

### Drift Detection

Answers:

```text
Is There A Difference?
```

---

### Self-Healing

Answers:

```text
How Should The Difference Be Corrected?
```

Drift Detection identifies.

Self-Healing restores.

---

# A Mature GitOps Perspective

One of the biggest misconceptions in GitOps is:

```text
Drift Detected
      ↓
Always Self-Heal
```

In reality:

```text
Not All Drift Is Bad
```

Some drift represents:

```text
Unauthorized Changes
```

and should be corrected.

Other drift represents:

```text
Operator Actions
Incident Response
Temporary Operational Activities
```

and may need to be tolerated temporarily.

Successful GitOps implementations focus not only on detecting drift but also on understanding the nature of the drift before deciding how it should be handled.

---

# Key Takeaway

GitOps extends reconciliation beyond deployment by continuously comparing the desired state stored in Git with the actual state running inside Kubernetes.

Drift Detection provides visibility into differences between these states, while Self-Healing enables automatic restoration of the desired state.

Although Self-Healing improves consistency and reliability, not all drift should be corrected automatically. Production environments often require careful consideration of operator-managed resources, incident response procedures, and operational workflows.

The goal of GitOps is not simply to eliminate drift, but to manage drift intelligently while preserving Git as the authoritative source of truth.
