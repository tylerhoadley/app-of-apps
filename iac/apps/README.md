# Argo CD Dynamic AppProject Engine
This directory contains the dynamic-projects ApplicationSet configuration. The engine dynamically scans the infrastructure repository to automatically discover platform domains, provision native Argo CD AppProject boundaries, and inject external Identity Provider (IdP) groups directly into local Role-Based Access Control (RBAC) policies.

🛠 How It Works

```
Discovery Phase                Extraction Phase                   Generation Phase
[iac/*/.appProject_config.yaml] ──► Sourced by Git Generator ──► [AppProject: <basename>]
                                  • .groups.nonprod                • Enforces Developer Group
                                  • .groups.prod                   • Enforces Promoter Group
```

1. Path-Based Auto-Discovery: The ApplicationSet utilizes a Git File Generator targeting files matching `iac/*/.appProject_config.yaml`.

2. Metadata Binding: The folder name holding the configuration file `{{ .path.basename }}` automatically defines the name of the resulting Argo CD AppProject.

3. Values Injection: The contents of the discovered configuration file are parsed by the Go Template engine. Its structural variables map directly into the underlying argocd-apps Helm release chart parameters to generate project-scoped roles.

🔐 RBAC & Identity Provider (IdP) Group Injection
Instead of statically managing role bindings in a global configuration map, this engine delegates authentication parameters to regional platform teams. Groups defined in the developer config map directly into pre-configured Argo CD project roles.

1. Developer Value File Specification (.appProject_config.yaml)
To register an application domain, create an entry at iac/<your-platform-domain>/.appProject_config.yaml matching this structural layout:

```yaml
---
groups:
  nonprod: Argo_Media_NonProd
  prod: Argo_Media_Prod
```

2. Evaluated RBAC Role Matrices
The generator processes the two distinct identity targets to automatically construct project boundaries:

🔹 pilot-dev-sync Role
Assigned Identities: Binds to both groups.nonprod and groups.prod.

Scope Limits: Intended for active development operations. Grants complete read/write access to test boundaries, enabling destructive hooks (sync, delete, restart, exec) on non-production lifecycles while strictly isolating production workloads.

Target Filters: Matches matching app patterns ending with -feature, -dev, -pilot, or -preview.

🔹 prod-sync Role
Assigned Identities: Binds exclusively to groups.prod.

Scope Limits: A read-heavy, low-privilege fallback role allowing elevated production management groups to view or verify full-scope configurations (*) within the project boundary.

Target Filters: Matches all apps under the project domain namespace.

---

⚠️ Configuration Fail-Safes
Zero Loose Space Validation: The inline template parsing block handles multi-line parameter injections inside values: |. Do not insert arbitrary whitespace or standard comment tags (#) within the data fields of .appProject_config.yaml, as they corrupt dynamic indentation strings.

Enforced Key Enforcement: The execution engine passes missingkey=error. If a directory file fails to supply either the nonprod or prod parameter strings under the groups header block, compilation fails immediately to prevent unassigned RBAC policies from being deployed.