# Apps

Each directory is one self-contained application, deployed by ArgoCD from the management repo (`applications/public/<app>` there defines the Application; the app's namespace must also be listed in the `public` AppProject destinations).

## Layout

Plain-YAML Kustomize, one canonical file per concern, listed in `kustomization.yaml` in this order:

```
namespace → service → storage → deployment → configmap → database
→ secrets → cloudflare → networkpolicy → barman-object-store → scheduled-backup
```

Stateless apps drop the database/backup files (`social-to-mealie` is the minimal example; `mealie` is the full stateful example).

## Conventions

- **Explicit `metadata.namespace` on every document**, including every document of multi-doc files, even though `kustomization.yaml` also sets it — manifests stay unambiguous when read or applied in isolation.
- **Naming derives from the app name:** `<app>-configmap`, `<app>-app-secrets`, `<app>-db-creds`, `<app>-data-pvc`, `<app>-db-barman-store`, `<app>-db` (the CNPG managed rw service). The CNPG Cluster is `<app>-db-prod-cnpg-vN`: the suffix increments on every recovery bootstrap while `serverName` stays `-v0`, because a Cluster's bootstrap is immutable and all versions archive to the same blob server path.
- **Secrets are ExternalSecrets** pulling from the cluster secret store; no Secret values in this repo, ever.
- **Every pod carries `network-policy-type: app|database|cloudflared`** — the three CiliumNetworkPolicies per app select on it.
- **Databases are CNPG** with barman backups to blob and a daily ScheduledBackup; app PVCs and DB storage deliberately use different storage classes (node-local vs iSCSI) for their different durability needs.
- **Formatting:** 2-space indent, a blank line between top-level `spec` stanzas, a comment above every network-policy rule, ConfigMap numerics quoted.

Image tags are pinned and Renovate-managed; current values live in the manifests, not here.
