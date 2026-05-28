---
title: Docker and Containerization
date: 2026-05-28
tags: [tech, devops, docker, containers, infrastructure, deployment]
source: research
last_updated: 2026-05-28
---

## Summary
Containerization is a method of packaging an application and all its dependencies into a self-contained, portable unit called a container. Docker, released in 2013, made containerization widely accessible and became the de facto standard for deploying applications. Containers solve the classic "works on my machine" problem by ensuring consistent environments across development, testing, and production.

## Key Points
- Containers are lightweight, portable, and isolated environments — faster and more resource-efficient than virtual machines
- Docker is the dominant containerization platform, providing tooling to build, ship, and run containers
- Images are immutable blueprints; containers are running instances of images
- Docker Hub is the central registry for sharing public container images
- Docker Compose orchestrates multi-container applications on a single host
- Kubernetes (K8s) extends containerization to large-scale orchestration across clusters of machines
- Containers share the host OS kernel — unlike VMs which virtualize the entire OS

## Details

### The Problem Containers Solve

Before containers, deploying software meant fighting dependency conflicts. An app that ran perfectly on a developer's laptop might fail in production because of different OS versions, library versions, environment variables, or system configuration. The solution historically was either careful documentation and manual configuration, or virtual machines — but VMs are heavy (each VM runs a full OS), slow to start, and resource-intensive.

Containers take a different approach: package the application code, its runtime (e.g., Python 3.11), its libraries (e.g., specific numpy version), and configuration into a single artifact. This artifact runs identically everywhere.

### Containers vs. Virtual Machines

| Aspect | Virtual Machine | Container |
|---|---|---|
| Isolation | Full OS virtualization | Process-level isolation |
| Size | GBs (includes full OS) | MBs (shares host kernel) |
| Startup time | Minutes | Seconds or milliseconds |
| Resource overhead | High | Low |
| Portability | Moderate | High |
| Security isolation | Strong | Good (weaker than VMs) |

Containers share the host OS kernel via Linux kernel features (namespaces for isolation, cgroups for resource limits). This is why containers are fast and lightweight — they don't boot an OS, they just start a process in an isolated environment.

### Core Docker Concepts

**Images**
A Docker image is an immutable, read-only template that defines what a container will look like. Images are built in layers — each instruction in a Dockerfile adds a layer. Layers are cached, making rebuilds fast when only upper layers change.

An image might contain: an OS base layer (e.g., ubuntu:22.04), a runtime (e.g., python:3.11), installed libraries, application code, and configuration.

**Containers**
A container is a running instance of an image. When you start a container, Docker adds a thin writable layer on top of the image. Multiple containers can run from the same image simultaneously, each with their own writable layer. Containers are ephemeral by default — when stopped and removed, their writable layer is discarded.

**Dockerfile**
A Dockerfile is a text file with instructions for building an image. Example:

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

Key instructions:
- `FROM`: Base image to build upon
- `RUN`: Execute commands during build (installs packages, etc.)
- `COPY`: Copy files from host into the image
- `CMD`/`ENTRYPOINT`: Default command to run when container starts
- `ENV`: Set environment variables
- `EXPOSE`: Document which ports the container listens on

**Registries**
Container images are stored in registries. Docker Hub is the default public registry with millions of official and community images. Private registries (AWS ECR, Google Container Registry, GitHub Container Registry) are used for proprietary images. Images are referenced as `registry/repository:tag`, e.g., `nginx:1.25` or `mycompany.azurecr.io/api:v2.3`.

### Docker Networking

Containers can communicate with each other and the outside world through Docker's networking layer:
- **Bridge network** (default): Containers on the same bridge network can talk to each other by container name; isolated from host network
- **Host network**: Container shares the host's network stack directly (better performance, less isolation)
- **Overlay network**: Spans multiple Docker hosts, used by Docker Swarm and Kubernetes
- **Port mapping**: Expose a container port to the host with `-p 8080:80` (host:container)

### Docker Volumes and Data Persistence

Since containers are ephemeral, data stored inside a container's writable layer is lost when the container is removed. For persistent data, use:
- **Volumes**: Managed by Docker, stored at `/var/lib/docker/volumes/`. Best practice for databases and persistent application data.
- **Bind mounts**: Mount a specific host directory into the container. Useful for development (live code reloading).
- **tmpfs mounts**: In-memory storage for sensitive temporary data.

### Docker Compose

Docker Compose is a tool for defining and running multi-container applications. A `docker-compose.yml` file describes multiple services (e.g., web app + database + cache), their images, environment variables, volumes, and network connections.

```yaml
version: '3.9'
services:
  web:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgres://db/myapp
    depends_on:
      - db
  db:
    image: postgres:15
    volumes:
      - pgdata:/var/lib/postgresql/data
volumes:
  pgdata:
```

Running `docker compose up` spins up the entire stack. This makes local development of complex, multi-service applications dramatically simpler.

### Container Security Considerations

Containers provide process isolation but are not as secure as VMs. Key security practices:
- **Principle of least privilege**: Run containers as non-root users
- **Minimal base images**: Use slim or distroless images to reduce attack surface
- **Image scanning**: Scan images for known vulnerabilities (Trivy, Snyk, AWS ECR scanning)
- **Read-only filesystems**: Mount application containers with read-only root filesystems where possible
- **Secrets management**: Never bake secrets into images; use environment variables or secret management systems (Vault, K8s Secrets)
- **Network policies**: Restrict inter-container communication to only what's necessary

### The Path to Kubernetes

Docker is excellent for single-host deployments, but production workloads often need:
- Running across multiple servers for high availability
- Automatic restarts of failed containers
- Load balancing across container instances
- Rolling deployments with zero downtime
- Auto-scaling based on traffic

**Kubernetes (K8s)** addresses these needs. It is an open-source container orchestration system (originally developed by Google, based on their internal Borg system) that manages clusters of machines running containers.

Key Kubernetes concepts:
- **Pod**: The smallest deployable unit — one or more containers running together
- **Deployment**: Manages a set of identical pods, handles rolling updates
- **Service**: Exposes pods to network traffic (internal or external)
- **ConfigMap/Secret**: Inject configuration and secrets into pods
- **Namespace**: Virtual cluster within a cluster for environment isolation

Managed Kubernetes offerings (AWS EKS, Google GKE, Azure AKS) handle the control plane, making K8s accessible without deep infrastructure expertise.

### The Container Ecosystem

Containers have become the foundation of modern software delivery:
- **CI/CD pipelines**: Build Docker images on every commit, push to registry, deploy to staging/production
- **Microservices**: Each service runs in its own container, enabling independent deployment and scaling
- **Serverless**: Platforms like AWS Lambda run user code in containers behind the scenes
- **MLOps**: Model serving and training jobs are containerized for reproducibility
- **Platform engineering**: Internal developer platforms built on Kubernetes abstract infrastructure from application teams

### Multi-Stage Builds — Optimizing Production Images

Multi-stage builds are one of the most impactful Dockerfile optimizations for production deployments. They allow using a large, feature-rich build image to compile/build, then copying only the artifacts into a minimal runtime image:

```dockerfile
# Stage 1: Build
FROM golang:1.22 AS builder
WORKDIR /app
COPY . .
RUN go build -o /app/server ./cmd/server

# Stage 2: Runtime (minimal)
FROM gcr.io/distroless/static:nonroot
COPY --from=builder /app/server /server
ENTRYPOINT ["/server"]
```

The final image contains only the compiled binary in a distroless base — no shell, no package manager, no build tools. Result: images of 5–30 MB instead of 500–1000 MB, dramatically reducing attack surface and registry storage costs.

**Distroless images** (Google's contribution): Container images with no shell or OS utilities — only the language runtime and application. They cannot be accessed interactively, which is a security advantage in production (an attacker who achieves code execution has no shell to drop into).

### WebAssembly (WASM/WASI) — A Container Alternative?

**WebAssembly (WASM)** was originally designed for browser performance but has emerged as a potential alternative to containers for certain workloads via **WASI** (WebAssembly System Interface) — a standard for running WASM outside the browser.

**Why WASM is interesting as a container alternative**:
- **Near-native performance** with a smaller startup overhead than containers (microseconds vs. milliseconds)
- **Language-agnostic**: Compile to WASM from Rust, Go, C/C++, Python (experimental)
- **Stronger sandboxing**: No kernel sharing — WASM modules cannot access host resources without explicit capability grants, making them more secure than containers by design
- **Truly portable**: Run the same WASM binary on any OS/CPU architecture that has a WASM runtime (Docker still needs platform-specific images for ARM vs. x86)

**Current limitations**: Mature tooling exists only for Rust and Go; the ecosystem is not production-ready for most workloads; network stacks and file I/O are less mature than containers.

**Docker + WASM**: Docker has added experimental WASM support, allowing WASM modules to run as first-class containers. Solomon Hykes (Docker's creator) has called WASM what Docker would have been if it existed in 2008. Watch this space.

### Service Mesh — Microservices Communication Layer

As organizations scale Kubernetes deployments to hundreds of services, direct service-to-service communication becomes a management challenge: security (TLS between services), observability (tracing requests across services), reliability (retries, circuit breaking), and traffic management (canary deployments).

A **service mesh** addresses this by deploying a lightweight proxy sidecar alongside every container, transparently handling all service-to-service communication:

**Istio** (Google/IBM/Lyft): The most feature-rich service mesh. Deploys Envoy sidecars via automatic injection. Provides:
- **mTLS**: Mutual TLS between all services automatically, without changing application code
- **Traffic management**: Canary deployments, blue/green rollouts, A/B testing via routing rules
- **Observability**: Distributed tracing (Jaeger/Zipkin), metrics, and service topology visualization
- **Circuit breaking**: Automatic failure isolation when a downstream service becomes unhealthy

**Linkerd** (CNCF): Lighter-weight alternative to Istio with simpler configuration; uses Rust-based proxies with very low overhead.

**Cilium** (eBPF-based): Uses Linux eBPF (extended Berkeley Packet Filter) instead of sidecar proxies — higher performance, lower overhead. Becoming the preferred approach for performance-sensitive deployments.

**Tradeoff**: Service meshes add significant operational complexity. Only justified at sufficient scale (typically 10+ microservices with traffic management requirements). Teams under this threshold should use simpler approaches (gateway-based routing, HTTP middleware).

## Related
- [[Cloud Computing Fundamentals]]
- [[REST APIs and HTTP]]
- [[Cybersecurity Principles]]
- [[Machine Learning Fundamentals]]
