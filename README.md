# Loan-prediction-approval-deployment
GitOps repo of Loan-prediction-approval project - ENSAE MlOps Class 2026

## How it works
This repo is the single source of truth for what runs in the cluster.
1. The [main repo](https://github.com/kellybourbon2/loan-api) CI builds a new Docker image on every push to `main`
2. CI updates `deployment/deployment.yaml` with the new image sha tag via a PAT
3. ArgoCD detects the change and syncs the cluster automatically
4. A healthcheck workflow verifies the deployment succeeded, and rolls back if not

## Services
| Service | Description | Port |
|---------|-------------|------|
| `loan-api` | FastAPI ML inference service | 8000 |
| `prometheus` | Metrics scraping | 9090 |
| `grafana` | Dashboard UI | 3000 |

## Monitoring
Grafana is pre-provisioned with a **Loan Approval API** dashboard tracking:
- Request rate and latency (p50/p95)
- Approval rate over time
- Prediction probability distribution
- Data drift indicators (income, loan amount, LTI ratio)
- Prediction errors

## ArgoCD setup
The ArgoCD application points to this repo:
```yaml
source:
  repoURL: https://github.com/kellybourbon2/Loan-prediction-approval-deployment
  targetRevision: main
  path: deployment/
destination:
  namespace: user-kbourbon
```

To check the live state of the application, go to the ArgoCD service on SSPCloud, click **"New App"**, and paste the full content of the `application.yaml` file at the root of this repository. ArgoCD will connect to the cluster and display the real-time status of all deployed resources (pods, services, ingress).

## Related
- [Main repo](https://github.com/kellybourbon2/loan-api) — model training, API code, CI/CD
