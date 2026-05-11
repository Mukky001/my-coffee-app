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

---

## License

MIT License