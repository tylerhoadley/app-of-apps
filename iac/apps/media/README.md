# Kargo Lifecycle Orchestration & Promotion Pipeline Layer

This directory configures the promotional plumbing. It tracks structural files to generate Kargo custom resources (`Project`, `ProjectConfig`, `Warehouse`, `Stage`, and `PromotionTask`) to govern how artifacts move between stages safely.

## 🧪 Injecting Values into the Kargo Chart

The underlying engine parses variables provided within your local `.kargoProject_config.yaml` file to populate the schema values for Kargo objects.

### Standard Infrastructure Hook Configuration


```yaml
authorized_stage:
  - app-revision-sync
```
