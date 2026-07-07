# Mattermost Production Deployment

Production-grade deployment of Mattermost (open-source Slack alternative) on Kubernetes (k3s) on AWS EC2. Demonstrates end-to-end DevOps workflow on a real-world SaaS application — infrastructure provisioning, CI/CD, container orchestration, and full observability stack.

## Live Demo
- **Mattermost:** http://63.183.194.63:30065
- **Grafana:** http://63.183.194.63:30300 (admin/admin123)
- **Prometheus:** http://63.183.194.63:30090

## Architecture

```
GitHub Push
    │
    ▼
GitHub Actions CI/CD
    │
    ▼
AWS EC2 t3.small
    └── k3s Kubernetes Cluster
            └── Namespace: mattermost
                    ├── Mattermost :30065 (NodePort)
                    ├── PostgreSQL :5432
                    ├── Prometheus :30090 (NodePort)
                    └── Grafana    :30300 (NodePort)
```

## Features
- Mattermost team messaging platform (Slack alternative)
- PostgreSQL database backend
- Prometheus metrics collection
- Grafana dashboards and visualization
- GitHub Actions CI/CD — auto deploy on every push
- k3s lightweight Kubernetes on AWS EC2
- Health checks and readiness probes
- Terraform infrastructure (via portfolio-infrastructure repo)

## Tech Stack
- **Mattermost** - Team messaging SaaS platform
- **Kubernetes (k3s)** - Container orchestration
- **PostgreSQL** - Database
- **Prometheus** - Metrics collection
- **Grafana** - Monitoring dashboards
- **GitHub Actions** - CI/CD pipeline
- **AWS EC2 t3.small** - Cloud hosting
- **Terraform** - Infrastructure as Code

## Kubernetes Manifests

| File | Description |
|------|-------------|
| k8s/namespace.yml | mattermost namespace |
| k8s/postgres.yml | PostgreSQL deployment + service |
| k8s/mattermost.yml | Mattermost deployment + NodePort service |
| monitoring/prometheus.yml | Prometheus deployment + ConfigMap |
| monitoring/grafana.yml | Grafana deployment + NodePort service |

## CI/CD Pipeline

Every push to `main` branch:
1. Connects to k3s cluster via kubeconfig secret
2. Applies all Kubernetes manifests
3. Waits for PostgreSQL rollout
4. Waits for Mattermost rollout
5. Waits for Prometheus and Grafana rollout
6. Reports pod status and URLs

## Quick Start

```bash
# Provision infrastructure (via portfolio-infrastructure)
cd portfolio-infrastructure/environments/mattermost
terraform apply -auto-approve

# Clone and deploy
git clone https://github.com/IvanLuketic2002/mattermost-production.git
cd mattermost-production

# Add KUBECONFIG secret to GitHub
# Push to main triggers deployment automatically
git push origin main

# Check status
kubectl get pods -n mattermost
```

## GitHub Secrets Required

| Secret | Description |
|--------|-------------|
| KUBECONFIG | k3s kubeconfig with public EC2 IP |

## Cost
~$0.02/hour for EC2 t3.small. Destroy after demo to save costs.
