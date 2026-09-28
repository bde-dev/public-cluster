# Public Cluster

This repository contains the Kubernetes manifests for the applications on the public cluster.

## Applications

| Directory | Application |
|-----------|-------------|
| `apps/commafeed` | RSS reader |
| `apps/linkding` | Bookmark manager |
| `apps/mealie` | Recipe manager |
| `apps/social-to-mealie` | Recipe import from social media into Mealie |
| `apps/wallabag` | Read-later service |

Each directory is a Kustomize base.

## Application Deployment

ArgoCD is deployed in the `management-cluster` which handles GitOps deployment of these applications. The ArgoCD application manifests for the `public-cluster` live on the `main` branch of the `management-cluster` repo.

## Cluster management

This cluster is managed from the `management-cluster` repository. I Use `kubectl` contexts there to switch between clusters.

The tools in this repository are for break-glass use only. I Use them only when I cannot use the `management-cluster` environment.

The devcontainer installs the break-glass tools with `mise`. The tool list is in `mise.toml`.

## Dependencies

Renovate running on the `management-cluster` updates the image tags and the tool versions. The configuration is in `renovate.json`.
