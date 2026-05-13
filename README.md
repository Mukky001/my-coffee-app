# ☕ Coffee Store — DevOps Portfolio Project

> A production-grade DevOps implementation of the
> [Coffee Store E-Commerce Application](./FRONTEND.md)
> originally developed by [siddami](https://github.com/siddami).

---

## Project Overview

This repository takes an existing Next.js e-commerce application and wraps
it in a production-grade DevOps setup — containerisation with Docker,
automated CI/CD with GitHub Actions, and container orchestration with
Kubernetes.

---

## Tech Stack

| Technology | Purpose |
|---|---|
| Next.js 15 | React framework |
| TypeScript | Type-safe JavaScript |
| Tailwind CSS | Utility-first styling |
| Docker | Containerisation |
| Docker Hub | Container registry |
| GitHub Actions | CI/CD automation |
| Kubernetes | Container orchestration |
| Minikube | Local Kubernetes cluster |
| NGINX Ingress | External traffic routing |
| Helm | Kubernetes package manager |
| Prometheus | Metrics collection |
| Grafana | Monitoring dashboards |

---

## Getting Started

### Prerequisites

- [Git](https://git-scm.com/)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [Minikube](https://minikube.sigs.k8s.io/docs/start/) (for Kubernetes deployment)
- [kubectl](https://kubernetes.io/docs/tasks/tools/) (for managing the cluster)

---

## Running the App

### Option 1 — Docker only (Quickest)

```bash
# Always get the latest version
docker pull mukky99/coffee-app:latest
docker run -p 3000:3000 mukky99/coffee-app:latest
```

Open your browser at `http://localhost:3000`

---

### Option 2 — Run a Specific Version

Every build is tagged with its unique Git commit SHA,
so you can run any previous version:

```bash
docker pull mukky99/coffee-app:<commit-sha>
docker run -p 3000:3000 mukky99/coffee-app:<commit-sha>
```

Replace `<commit-sha>` with any tag visible on
[Docker Hub](https://hub.docker.com/r/mukky99/coffee-app/tags).

---

### Option 3 — Kubernetes (Full Setup)

**Step 1 — Start Minikube:**
```bash
minikube start --memory=3000 --cpus=2
```

**Step 2 — Enable the Ingress Controller:**
```bash
minikube addons enable ingress
```

**Step 3 — Deploy the app:**
```bash
kubectl apply -f k8s/
```

**Step 4 — Get the Minikube IP:**
```bash
minikube ip
```

**Step 5 — Add the hostname to your hosts file:**
```bash
echo "<minikube-ip> coffee-app.local" | sudo tee -a /etc/hosts
```

Replace `<minikube-ip>` with the output of Step 4.

**Step 6 — Open your browser at:http://localhost:3000**
---

### Option 4 — Build Locally

```bash
# Clone the repo
git clone https://github.com/Mukky001/my-coffee-app.git
cd my-coffee-app

# Build the image
docker build -t coffee-app:v1 .

# Run the container
docker run -p 3000:3000 coffee-app:v1
```

---

## Docker Implementation

This project uses a **multi-stage Dockerfile** to keep the final
image small and production-ready.

**Stage 1 — Builder:**
- Base image: `node:20-alpine`
- Installs dependencies with `npm ci`
- Builds the Next.js app with `npm run build`
- Generates standalone output via `output: "standalone"`
  in `next.config.ts`

**Stage 2 — Runner:**
- Starts fresh from `node:20-alpine`
- Copies only the built output from Stage 1
- No source code or dev dependencies in the final image
- Runs as a production Node.js server

---

## CI/CD Pipeline

This project uses GitHub Actions to automatically build and push
the Docker image on every push to the `main` branch.

**Workflow file:** `.github/workflows/docker.yml`

### Pipeline Steps
Push to main branch
↓

GitHub spins up free Ubuntu runner
Checks out the repository code
Logs in to Docker Hub using repository secrets
Builds the Docker image
Pushes two tags to Docker Hub:
→ latest        (always reflects the most recent build)
→ <commit-sha>  (unique tag preserving every version)

### Image Tagging Strategy

Every build produces two tags:

| Tag | Purpose |
|---|---|
| `latest` | Always points to the most recent build |
| `<git-commit-sha>` | Unique tag for every build — enables rollback |

This means no build ever overwrites a previous one. If a bad
version goes out, you can roll back to any previous image by
its SHA tag.

### Secrets Required

| Secret Name | Purpose |
|---|---|
| `DOCKERHUB_USERNAME` | Docker Hub account username |
| `DOCKERHUB_TOKEN` | Docker Hub access token (Read & Write) |

---

## Kubernetes Deployment

The app runs on Kubernetes using three manifest files located
in the `k8s/` folder.

### Manifest Files

**k8s/deployment.yaml**
- Runs 2 identical replicas of the app
- Pulls image from Docker Hub
- Sets resource requests and limits per Pod
- Self-healing — automatically replaces crashed Pods

**k8s/service.yaml**
- Type: ClusterIP
- Provides a stable internal address for the Pods
- Load balances traffic between the 2 replicas
- Maps port 80 → container port 3000

**k8s/ingress.yaml**
- Uses NGINX Ingress Controller
- Routes traffic for hostname `coffee-app.local`
- Forwards to the Service on port 80

### Resource Limits

| Resource | Request | Limit |
|---|---|---|
| Memory | 128Mi | 256Mi |
| CPU | 100m | 500m |

### Self-Healing

Kubernetes automatically replaces crashed Pods:
Pod crashes
↓
Deployment detects: only 1/2 replicas running
↓
New Pod created automatically within seconds
↓
Back to 2/2 replicas — zero manual intervention ✅


---

## Project Structure

```
my-coffee-app/
├── .github/
│   └── workflows/
│       └── docker.yml      # GitHub Actions CI/CD pipeline
├── k8s/
│   ├── deployment.yaml     # Kubernetes Deployment (2 replicas)
│   ├── service.yaml        # Kubernetes Service (ClusterIP)
│   └── ingress.yaml        # Kubernetes Ingress (NGINX)
├── app/                    # Next.js application source
├── public/                 # Static assets
├── types/                  # TypeScript type definitions
├── utils/                  # Utility functions
├── Dockerfile              # Multi-stage Docker build
├── .dockerignore           # Docker build exclusions
├── next.config.ts          # Next.js config (standalone mode)
├── FRONTEND.md             # Original frontend documentation
└── README.md               # This file

---

## Acknowledgements

- Original application developed by [siddami](https://github.com/siddami)
- Built as part of a DevOps learning journey

```
---

---

## GitOps with ArgoCD

This project uses ArgoCD to automatically sync Kubernetes deployments 
from Git — any change pushed to the `k8s/` folder is automatically 
applied to the cluster without manual kubectl commands.

### How It Works

```
Push change to k8s/ folder
↓
ArgoCD detects change in GitHub (polls every 3 minutes)
↓
Automatically applies changes to Kubernetes cluster
↓
Cluster state matches Git state ✅

```
### ArgoCD Configuration

**Application manifest:** `argocd-app.yaml`

| Setting | Value |
|---|---|
| Watched repository | `https://github.com/Mukky001/my-coffee-app.git` |
| Watched folder | `k8s/` |
| Target cluster | Same cluster ArgoCD runs in |
| Target namespace | `default` |
| Sync mode | Automated |
| Self-heal | Enabled — reverts manual cluster changes |
| Prune | Enabled — removes deleted resources |

### Installing ArgoCD

```bash
# Create namespace
kubectl create namespace argocd

# Install ArgoCD
kubectl apply -n argocd --server-side --force-conflicts -f \
https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Wait for all Pods to be Running
kubectl get pods -n argocd

# Access the UI
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Open `https://localhost:8080` in your browser.

### Getting the Admin Password

```bash
kubectl get secret argocd-initial-admin-secret -n argocd \
-o jsonpath="{.data.password}" | base64 -d
```

Login with username `admin` and the password from above.

### Deploying the Application

```bash
kubectl apply -f argocd-app.yaml
```

ArgoCD will immediately sync and deploy all manifests from the `k8s/` folder.

---

## Monitoring with Prometheus and Grafana

Full observability stack installed via Helm, collecting metrics
from every Pod and node in the cluster.

### What is Monitored

| Metric | What it shows |
|---|---|
| CPU Utilisation | Actual CPU being used across the cluster |
| CPU Requests Commitment | How much CPU Pods have reserved |
| CPU Limits Commitment | Maximum CPU Pods can consume |
| Memory Utilisation | Actual RAM being used |
| Memory Requests Commitment | How much RAM Pods have reserved |
| Memory Limits Commitment | Maximum RAM Pods can consume |

### Installing the Monitoring Stack

```bash
helm repo add prometheus-community \
  https://prometheus-community.github.io/helm-charts

helm repo update

kubectl create namespace monitoring

helm install prometheus prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --set grafana.adminPassword=admin123
```

### Accessing Grafana

```bash
kubectl port-forward -n monitoring svc/prometheus-grafana 3001:80
```

Open `http://localhost:3001` — username: `admin`, password: `admin123`

### Key Dashboards

| Dashboard | What it shows |
|---|---|
| Kubernetes / Compute Resources / Cluster | Full cluster CPU and memory overview |
| Kubernetes / Compute Resources / Namespace | Per-namespace resource usage |
| Kubernetes / Compute Resources / Pod | Per-pod CPU, memory, and throttling |
| Kubernetes / Nodes | Node-level health and resources |

### What the Metrics Mean

```
Utilisation  →  what is actually being used right now
Requests     →  what Pods have formally reserved
Limits       →  the maximum a Pod can ever consume

CPU Throttling = No data → Pod never starved of CPU ✅
Memory at requests line  → well calibrated resources ✅
```


## License

MIT License