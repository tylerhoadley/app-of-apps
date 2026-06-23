# GitOps Core Infrastructure & Application Blueprint Stacks

This repository provides a standardized, enterprise-grade declarative control plane for onboarding applications, environments, and promotional lifecycles via GitOps. It maps architectural boundaries dynamically into production-ready infrastructure resources by matching structured directories with powerful template engines.

## 🏗 Repository Structural Layers

```text
📁 
iac/<project>/<kargo-stack>/<app-component>/
    └─────────├─────────────├────────── .appProject_config.yaml      # ArgoCD Project + RBAC via injection 
              └─────────────├────────── .appProject_config.yaml      # Kargo Project, Kargo workload Values + RBAC via injection
                            ├────────── .applicationSets_config.yaml # ApplicationSet Configuration Value Injector for Kargo labels
                            └────────── values.yaml                  # Target applicationset generators, and default overrides

```

## 🧭 Architecture & Sub-System References
* **Argo CD ApplicationSet Engine (`/iac/<project>/`):** Controls automatic application instantiation, multi-source Helm declarations, custom target namespaces, sync policies, and template patches.
* **Kargo Lifecycle Orchestration Layer (`/iac/<project>/<kargo-stack>/`):** Manages Warehouses, Stages, Freight criteria, Promotion policies, and customizable multi-step tasks to coordinate continuous promotion safely across environments.
## 🚀 Quick-Start Template Blueprint
DevOps and Developer teams can onboard a component by copying an existing structure path to a new location. Keep the path structure to the 3-tier layout: `iac/<project>/<kargo-stack>/<app-component>/`.

