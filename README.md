# gitops

The GitOps source of truth for my homelab Kubernetes cluster. Application
manifests live here; ArgoCD watches this repo and continuously reconciles the
cluster to match it. I don't `kubectl apply` to deploy — I change Git, and the
cluster follows.


## How it works

push manifest change  →  ArgoCD detects drift  →  ArgoCD syncs cluster to match Git

ArgoCD runs in the cluster with automated sync enabled (`selfHeal: true`,
`prune: true`). If the live cluster ever drifts from what's declared here — a
manually deleted pod, an out-of-band change — ArgoCD pulls it back to match
this repo. Git is the single source of truth.


## Structure
.

├── apps/

│   ├── status-page/        # multi-region status page (stateless, 3 replicas)

│   │   ├── deployment.yaml

│   │   └── service.yaml

│   └── uptime-kuma/        # uptime monitor (stateful, persistent volume)

│       ├── deployment.yaml

│       ├── service.yaml

│       └── pvc.yaml

├── status-page-app.yaml    # ArgoCD Application definitions

└── uptime-kuma-app.yaml

Each `*-app.yaml` is an ArgoCD `Application` that points ArgoCD at the
corresponding folder under `apps/`.


## What this demonstrates

- GitOps application delivery with ArgoCD (declarative, auto-synced, self-healing)
- Stateless workloads (status-page) and stateful workloads with persistent
  storage (uptime-kuma, K3s local-path PVC)
- Multi-architecture images pulled from GitHub Container Registry (ghcr.io)
- A deliberate separation: this repo holds app manifests; infrastructure lives
  in a separate Terraform repo (homelab-iac)


## Stack

K3s (Kubernetes) · ArgoCD · GitHub Container Registry · local-path storage
