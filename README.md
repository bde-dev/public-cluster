# Public Cluster

This repository contains everything that runs on the public cluster: the Kubernetes manifests for each application and the ArgoCD Application that deploys it.

## Applications

| Directory | Application |
|-----------|-------------|
| `apps/commafeed` | RSS reader |
| `apps/linkding` | Bookmark manager |
| `apps/mealie` | Recipe manager |
| `apps/social-to-mealie` | Recipe import from social media into Mealie |
| `apps/wallabag` | Read-later service |

Each directory is a self-contained Kustomize base.

## Application Deployment

ArgoCD runs on the management cluster and deploys these applications with an app-of-apps pattern.

- `argocd/apps/<app>/` holds the ArgoCD `Application` for each app. It points at that app's Kustomize base in this repository and targets the `public-cluster` destination in the `public` AppProject.
- The `management-cluster` repository holds a single root `Application` for this cluster. It syncs the whole `argocd/apps` tree recursively, so every `Application` here is created, updated, and pruned by that root.

This repository owns its application identity. Adding an application is one change here: a Kustomize base under `apps/` and an `Application` under `argocd/apps/`.

## Cluster management

This cluster is managed from the `management-cluster` repository. I use `kubectl` contexts there to switch between clusters.

The tools in this repository are for break-glass use only. I use them only when I cannot use the `management-cluster` environment, and their credentials are not kept fresh.

The devcontainer installs the break-glass tools with `mise`. The tool list is in `mise.toml`.

## Dependencies

Renovate running on the `management-cluster` updates the image tags in `apps/`, the ArgoCD `Application` manifests in `argocd/`, and the tool versions in `mise.toml`. The configuration is in `renovate.json`.
