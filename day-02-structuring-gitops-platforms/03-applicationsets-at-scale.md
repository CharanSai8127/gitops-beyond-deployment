# ApplicationSets at Scale

## Introduction

In the previous chapter, we learned how the App of Apps pattern helps platform teams manage a product composed of multiple Argo CD Applications as a single operational unit.

While App of Apps helps manage product complexity, it does not solve the challenge of scaling applications across environments and clusters.

Modern platforms often require the same application to be deployed across multiple environments such as:

```text
Development
Staging
Production
```

and in some cases across multiple clusters.

Managing these deployments by manually creating Argo CD Applications quickly becomes difficult and introduces duplication.

ApplicationSets were introduced to solve this problem.

---

# Why ApplicationSets Exist

In Argo CD, an Application is the fundamental deployment unit.

Consider a Backend application deployed across three environments:

```text
Backend
    ├── Dev
    ├── Stage
    └── Prod
```

Without ApplicationSets, each environment requires a separate Application definition.

```text
backend-dev
backend-stage
backend-prod
```

As the number of applications and environments grows, the number of Application definitions increases rapidly.

For example:

```text
10 Applications
3 Environments
```

results in:

```text
30 Argo CD Applications
```

Managing these definitions manually becomes repetitive and error-prone.

ApplicationSets solve this challenge through dynamic Application generation.

---

# What is an ApplicationSet?

ApplicationSet is a native Argo CD component that dynamically generates and manages Applications at scale.

Rather than manually creating Application definitions, platform teams define rules that describe how Applications should be generated.

ApplicationSets focus on:

- Environment Scaling
- Multi-Cluster Deployments
- Dynamic Application Creation
- Reducing Configuration Duplication

This allows GitOps platforms to scale while maintaining consistency.

---

# Core Components of an ApplicationSet

An ApplicationSet consists of two primary components:

```text
ApplicationSet
       │
       ├── Generator
       │
       └── Template
```

Together, these components determine:

```text
Where Applications Should Be Created
```

and

```text
How Applications Should Be Created
```

---

# Understanding Generators

The Generator defines the input parameters used to generate Applications.

A Generator answers the question:

> Where should Applications be created?

The Generator discovers inputs that are later consumed by the Template.

Examples include:

- Environments
- Clusters
- Directories
- Files
- Static Lists

The Generator acts as the source of information for Application creation.

---

# List Generator

The List Generator uses a predefined set of values.

Example:

```text
dev
stage
prod
```

The Generator provides these values as inputs to the Template.

This approach is useful when the list of environments or targets is relatively static.

---

# Git Directory Generator

The Git Directory Generator iterates through directories within a Git repository.

Consider the following repository structure:

```text
overlays/

├── dev
├── stage
└── prod
```

The Generator discovers:

```text
dev
stage
prod
```

These discovered values become inputs to the Template.

This is one of the most commonly used generators when working with Kustomize overlays.

---

# Cluster Generator

The Cluster Generator iterates through clusters registered within Argo CD.

Example:

```text
cluster-a
cluster-b
cluster-c
```

Applications can then be automatically generated for each cluster.

This enables GitOps workflows to scale across multiple Kubernetes clusters.

---

# Git File Generator

The Git File Generator reads structured data from files stored in Git.

Example:

```yaml
environments:
  - dev
  - stage
  - prod
```

The data contained within the file becomes input for Application generation.

This approach is useful when environment definitions or deployment targets are maintained as structured data.

---

# Matrix Generator

The Matrix Generator combines multiple Generators.

For example:

```text
Clusters
      ×
Environments
```

Result:

```text
dev-cluster-a
stage-cluster-a
prod-cluster-a

dev-cluster-b
stage-cluster-b
prod-cluster-b
```

This allows Applications to be generated across multiple dimensions.

The Matrix Generator is particularly useful for large-scale multi-cluster GitOps deployments.

---

# Understanding Templates

While the Generator defines the inputs, the Template defines the Application that should be created.

A Template answers the question:

> How should the Application be created?

The Template acts as a blueprint for generating Applications.

It typically defines:

```text
Repository
Path
Destination Cluster
Namespace
Project
Sync Policy
```

Every generated Application follows the structure defined within the Template.

---

# How Generators and Templates Work Together

The relationship between Generators and Templates is the foundation of ApplicationSets.

```text
Generator
      │
      ▼
Input Parameters
      │
      ▼
Template
      │
      ▼
Generated Applications
```

The Generator discovers values.

The Template consumes those values.

The ApplicationSet controller combines both components to create Applications dynamically.

Conceptually, the controller performs:

```text
For Each Input
        │
        ▼
Generate Application
```

without requiring multiple Application definitions.

---

# ApplicationSets with Kustomize

One of the most common ApplicationSet implementations uses Kustomize overlays.

Repository structure:

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

The Template defines how the Application should be created.

The resulting Applications become:

```text
backend-dev
backend-stage
backend-prod
```

All Applications are generated automatically.

No duplication of Application definitions is required.

This approach simplifies environment management and ensures consistency across deployments.

---

# Scaling Across Environments and Clusters

ApplicationSets enable platforms to scale in two important dimensions.

### Environment Scaling

```text
Dev
Stage
Prod
```

Applications are generated automatically for each environment.

---

### Multi-Cluster Scaling

```text
Cluster-A
Cluster-B
Cluster-C
```

Applications are generated automatically for each cluster.

---

By combining environments and clusters, ApplicationSets provide a scalable deployment model suitable for large GitOps platforms.

---

# ApplicationSet vs App of Apps

Although both patterns help platforms scale, they solve different problems.

### App of Apps

Focuses on:

```text
Managing Applications
```

Provides:

```text
Hierarchy
Organization
Lifecycle Management
Operational Visibility
```

---

### ApplicationSet

Focuses on:

```text
Generating Applications
```

Provides:

```text
Automation
Environment Scaling
Cluster Scaling
Dynamic Application Creation
```

App of Apps helps manage products.

ApplicationSets help scale products.

Both patterns are commonly used together in modern GitOps platforms.

---

# Key Takeaway

ApplicationSets solve the scaling problem in GitOps.

Instead of manually creating and maintaining Application definitions, platform teams define:

```text
Generator
        +
Template
```

The Generator defines the input parameters and determines where Applications should be created.

The Template defines how Applications should be created.

The ApplicationSet controller combines both components to dynamically generate Applications across environments and clusters.

This approach:

- Reduces Duplication
- Simplifies Operations
- Enables Environment Scaling
- Enables Multi-Cluster Deployments
- Improves Consistency Across Platforms

ApplicationSets are one of the most powerful mechanisms for operating GitOps platforms at scale.
