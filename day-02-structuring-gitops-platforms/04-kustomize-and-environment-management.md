# Kustomize and Environment Management

## Introduction

Modern applications are rarely deployed to a single environment.

A typical deployment strategy includes:

```text
Development
Staging
Production
```

Although the application remains fundamentally the same, each environment often requires specific customizations.

Examples include:

- Replica Counts
- Resource Limits
- Storage Classes
- Network Policies
- Configuration Values
- External Endpoints

Managing these environments manually quickly becomes difficult.

Each environment requires its own application definition, increasing duplication and operational overhead.

As the number of resources grows, maintaining consistency becomes challenging and the risk of human error increases.

Kustomize was introduced to solve this problem.

---

# The Multi-Environment Challenge

Consider a Backend application deployed across three environments:

```text
Backend
    ├── Dev
    ├── Stage
    └── Prod
```

Without a configuration management approach, teams often create separate manifests for each environment.

```text
backend-dev.yaml
backend-stage.yaml
backend-prod.yaml
```

Initially this may appear manageable.

As the application grows, however, additional resources are introduced.

```text
Deployment
Service
ConfigMap
Secret
HPA
NetworkPolicy
```

Every change must now be replicated across multiple environment-specific manifests.

This introduces several problems:

### Manifest Duplication

The same application definition exists multiple times.

### Environment Drift

Environments slowly become inconsistent.

### Operational Overhead

Changes must be maintained in multiple locations.

### Human Error

Updating one environment while forgetting another becomes common.

A better approach is required.

---

# What is Kustomize?

Kustomize is a Kubernetes-native configuration management tool built directly into kubectl.

Its purpose is to customize Kubernetes manifests without duplicating resources.

Instead of maintaining multiple copies of the same application, Kustomize separates:

```text
Common Configuration
```

from

```text
Environment Specific Configuration
```

This separation enables scalable environment management.

The core Kustomize model is:

```text
Base
    +
Overlay
```

---

# Understanding the Base

The Base contains the common application definition shared across all environments.

Typical resources include:

```text
Deployment
Service
ConfigMap
```

The Base defines:

```text
How the Application Works
```

rather than:

```text
How the Environment Differs
```

A typical structure looks like:

```text
base/

├── deployment.yaml
├── service.yaml
└── kustomization.yaml
```

The Base acts as the single source of application configuration.

Every environment inherits from this definition.

---

# Understanding Overlays

Overlays contain environment-specific modifications.

Rather than redefining the application, Overlays only describe what changes for a particular environment.

Example:

### Development

```text
replicas = 1
```

### Staging

```text
replicas = 2
```

### Production

```text
replicas = 5
```

The Overlay focuses only on the differences.

This significantly reduces duplication and improves maintainability.

A typical structure looks like:

```text
overlays/

├── dev
├── stage
└── prod
```

Each Overlay inherits the Base and applies environment-specific customizations.

---

# Base and Overlay Together

The relationship between Base and Overlay can be visualized as:

```text
Base
 │
 ├── Deployment
 ├── Service
 └── ConfigMap

        +
        ▼

Overlay

├── Dev
├── Stage
└── Prod
```

Result:

```text
Dev Manifest
Stage Manifest
Prod Manifest
```

All generated from the same application definition.

This ensures consistency while allowing environment-specific behavior.

---

# Generators in Kustomize

Kustomize also provides Generators that help create configuration resources dynamically.

Generators simplify the management of application configuration.

Two commonly used generators are:

```text
ConfigMap Generator
Secret Generator
```

---

# ConfigMap Generator

The ConfigMap Generator is used to create non-sensitive configuration data.

Examples include:

```text
Application Mode
Feature Flags
API Endpoints
Environment Variables
```

Instead of manually creating ConfigMap resources, Kustomize can generate them automatically.

This improves consistency and simplifies configuration management.

---

# Secret Generator

The Secret Generator is used to create sensitive configuration data.

Examples include:

```text
Passwords
Tokens
API Keys
Certificates
```

Kustomize generates Kubernetes Secret resources from the provided input.

This provides a structured way to manage sensitive application configuration.

---

# Kustomize and GitOps

Kustomize integrates naturally with GitOps workflows.

A common repository structure looks like:

```text
application/

├── base
│
└── overlays
    ├── dev
    ├── stage
    └── prod
```

The desired state remains versioned within Git.

Environment-specific customizations remain isolated within Overlays.

This makes GitOps repositories easier to manage and scale.

---

# Kustomize with ApplicationSets

ApplicationSets and Kustomize are commonly used together.

Consider the following structure:

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

The ApplicationSet Template then generates:

```text
backend-dev
backend-stage
backend-prod
```

Applications automatically.

This approach eliminates the need to manually create individual Application definitions for every environment.

---

# Benefits of Kustomize

Kustomize provides several advantages for GitOps platforms.

### Reduced Duplication

One application definition can support multiple environments.

### Reduced Human Error

Only environment-specific changes are maintained.

### Environment Consistency

All environments inherit from the same Base.

### Easier Maintenance

Application updates occur in a single location.

### GitOps Friendly

All configuration remains declarative and version controlled.

---

# Key Takeaway

Managing applications across multiple environments manually becomes increasingly difficult as platforms grow.

Kustomize solves this challenge by separating:

```text
Base
```

from

```text
Environment Specific Overlays
```

The Base defines the common application structure, while Overlays define only the changes required for individual environments.

Combined with ApplicationSets, Kustomize enables platform teams to manage applications across development, staging, and production environments without duplicating manifests.

This approach reduces operational overhead, minimizes human error, and ensures consistency across environments.
