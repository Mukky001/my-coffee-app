# ☕ Coffee Store — DevOps Portfolio Project

> A production-grade Docker implementation of the 
> [Coffee Store E-Commerce Application](./FRONTEND.md)
> originally developed by [siddami](https://github.com/siddami).

---

## Project Overview

This repository takes an existing Next.js e-commerce application and 
containerises it using Docker, following production best practices including 
multi-stage builds, minimal image size, and standalone output.

---

## Tech Stack

| Technology | Purpose |
|---|---|
| Next.js 15 | React framework |
| TypeScript | Type-safe JavaScript |
| Tailwind CSS | Utility-first styling |
| Docker | Containerisation |
| Docker Hub | Container registry |

---

## Getting Started

### Prerequisites

- [Git](https://git-scm.com/)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)

---

## Running the App

### Option 1 — Pull from Docker Hub

```bash
docker pull mukky99/coffee-app:v1
docker run -p 3000:3000 mukky99/coffee-app:v1
```

Open your browser at `http://localhost:3000`

---

### Option 2 — Build Locally

```bash
# Clone the repo
git clone https://github.com/mukky99/my-coffee-app.git
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
- Builds the Next.js app
- Generates standalone output

**Stage 2 — Runner:**
- Starts fresh from `node:20-alpine`
- Copies only the built output from Stage 1
- No source code or dev dependencies included
- Runs as a production Node.js server

**Result:**
