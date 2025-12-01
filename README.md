# illuminati_gitops

This repository contains ArgoCD Application manifests for deploying Illuminati applications to Kubernetes. It works together with the [Illuminati Helm Charts repository](https://github.com/Core5-team/illuminati_helm_charts), which contains the Helm charts and default values for each application.

Applications are deployed to the illuminati namespace and synced automatically with ArgoCD. This repo supports multiple environments (dev, stage, prod) by specifying environment-specific values files.

**Note: Cluster resources such as databases, secrets and DNS must already exist. See the cluster [infrastructure repository](https://github.com/Core5-team/illuminati_eks)
 for details.**

## Repository Structure

- ```apps/<env>/<app>.yaml``` – ArgoCD Application manifest for a specific app and environment
- ```envs/<env>/<app>-values.yaml``` – Environment-specific Helm values overriding defaults from [illuminati_helm_charts](https://github.com/Core5-team/illuminati_helm_charts)

Examples of existing applications:

- **backend** – backend Deployment, Service, HPA; references database secrets
- **frontend** – frontend Deployment, Service, HPA; includes DNS configuration

Each manifest contains:
- sources pointing to both:
  - Helm charts repo ([illuminati_helm_charts](https://github.com/Core5-team/illuminati_helm_charts)) with default and environment-specific values
  - This GitOps repo ([illuminati_gitops](https://github.com/Core5-team/illuminati_gitops)) for environment overrides
- destination namespace (```illuminati```)
- Automated sync policy with prune and selfHeal enabled

## Deployment
### Prerequisites
- Kubernetes cluster with illuminati namespace:
- kubectl create namespace illuminati
- ArgoCD installed and accessible ([see ArgoCD docs](https://argo-cd.readthedocs.io/en/stable/getting_started/))
- Cluster resources required by applications (databases, secrets, DNS)

#### Using ArgoCD UI

1. Open ArgoCD.
2. Create a new Application pointing to the folder of the app in this repository (```apps/<env>/<app>.yaml```).
3. Set the namespace to ```illuminati```.
4. Configure project and sync options if needed.
5. Sync the application to deploy it.

#### Using ```kubectl apply```

You can also apply the ArgoCD Application manifests directly:

```kubectl apply -f apps/<env>/<app>.yaml```

After applying, ArgoCD will automatically deploy the Helm charts specified in the sources section.

## Adding New Applications

1. Add a new folder for the app under apps/<env>/ with its ArgoCD manifest.
2. Add environment-specific values under envs/<env>/.
3. Update the sources section to reference the Helm chart in illuminati_helm_charts and optionally environment overrides in this repo.
4. Deploy via ArgoCD UI or ```kubectl``` as described above.
All applications sync automatically and prune removed resources.

Environment-specific overrides allow different build versions, replica counts, CPU/memory thresholds, and DNS settings.

Existing apps in this repo: backend and frontend (dev, stage, prod).
