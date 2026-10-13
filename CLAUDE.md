# public-cluster

Kustomize manifests for the internet-facing applications of a three-cluster Talos homelab. The cluster is deployed and operated from the `management-cluster` repo; this repo holds only what runs on it. Public repo; manifests and tooling only: no design notes or working documents, and never a secret value.

## Topology

- ArgoCD on the management cluster deploys every app here. Each app's Application CR lives in this repo under `argocd/apps/<app>/`; a root Application in `management-cluster` recurses that directory, and the `public` AppProject already allows every namespace on this cluster. A new app is a Kustomize base under `apps/<app>/` plus its CR under `argocd/apps/<app>/`, both here.
- `apps/<app>/` is one self-contained Kustomize base per app, one canonical file per concern (namespace, deployment, service, secrets, database, configmap, cloudflare, networkpolicy, barman-object-store, scheduled-backup), listed in that app's `kustomization.yaml`. The README lists the apps.
- Apps reach the internet through a per-app `cloudflared` Deployment (`cloudflare.yaml`) rather than an ingress, and every pod carries a `network-policy-type` label that the app's network policies select on.

## Working in this repo

- **Deploys track `main`.** The Application CRs pin `targetRevision: HEAD`, so a feature-branch commit pushes fine and never deploys. Check the branch before committing.
- **Operate the cluster from the management-cluster environment** with `kubectl --context`. The tools pinned in `mise.toml` here are break-glass only, for when that environment is unavailable, and their credentials are not kept fresh.
- **Secrets are ExternalSecrets.** No Secret values in this repo, ever.
- **Versions live in the manifests**, pinned and Renovate-managed. Docs point at them rather than repeating them.

## Documentation style

Docs describe current state and its reasons, never the change that produced it.
