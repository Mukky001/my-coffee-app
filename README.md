# ☕ Coffee Store — DevOps Portfolio Project

> A production-grade Docker implementation of the
> [Coffee Store E-Commerce Application](./FRONTEND.md)
> originally developed by [siddami](https://github.com/siddami).

---

## Project Overview

This repository takes an existing Next.js e-commerce application and wraps 
it in a production-grade DevOps setup — containerisation with Docker and 
automated CI/CD with GitHub Actions.

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

---

## Getting Started

### Prerequisites

- [Git](https://git-scm.com/)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)

---

## Running the App

### Option 1 — Pull from Docker Hub

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

### Option 3 — Build Locally

```bash
# Clone the repo
git clone https://github.com/Mukky001/my-coffee-app.git
cd my-coffee-app

# Build the image
docker build -t coffee-app:v1 .

# Run the container
docker run -p 3000:3000 coffee-app:v1
```

Open your browser at `http://localhost:3000`

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

**Result:**

---

## CI/CD Pipeline

This project uses GitHub Actions to automatically build and push 
the Docker image on every push to the `main` branch.

**Workflow file:** `.github/workflows/docker.yml`

### Pipeline Steps

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

## Project Structure

---

## Acknowledgements

- Original application developed by [siddami](https://github.com/siddami)
- Built as part of a DevOps learning journey

---

## License

MIT License