# Argo CD Application Generation & YAML Value Injector
This path houses the core ApplicationSet engine. The master controller automatically scans your directory paths looking for local `.applicationSets_config.yaml` files. It extracts their properties and dynamically pipes them as variables into the parent `argocd-apps` Helm release chart.
## ⚙️ Using the YAML Injector
Every directory layer **must** include a `.applicationSets_config.yaml` file at its root. If you need a standard, default deployment, the file must contain a single list with defaults set to ensure the application loads as expected:
```yaml
generators:
  - list:
      elements:
        - appName: plex
          env: live
          repoURL: https://github.com/argoproj/argocd-example-apps.git
          targetRevision: HEAD
          path: guestbook
          cluster: in-cluster
          autoSync: true
          prune: false
          selfHeal: true
        # - appName: backup
        #   env: dev
        #   repoURL: https://github.com/argoproj/argocd-example-apps.git
        #   targetRevision: HEAD
        #   path: guestbook
        #   cluster: in-cluster
        #   autoSync: true
        #   prune: false
        #   selfHeal: true

templatePatch: |
  spec:
    syncPolicy:
      {{- if .autoSync }}
      automated:
        enabled: true
        prune: {{ .prune }}
        selfHeal: {{ .selfHeal }}
      {{- else }}
      automated:
        enabled: false
        prune: {{ .prune }}
        selfHeal: {{ .selfHeal }}
      {{- end }}
template:
  metadata:
    name: '{{ .appName }}-{{ .env }}'
  spec:
    source:
      repoURL: '{{ .repoURL }}'
      targetRevision: '{{ .targetRevision }}'
      path: '{{ .path }}'
    destination:
      name: '{{ .cluster }}'
      # namespace: ${ARGOCD_APP_NAMESPACE}
    syncPolicy:
      automated:
        prune: false
        selfHeal: false
        enabled: false
      syncOptions:
        - CreateNamespace=true
        - RespectIgnoreDifferences=true # Essential for ignoreDifferences to work

```