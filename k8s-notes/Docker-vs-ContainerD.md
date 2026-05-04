# Container Runtimes & Kubernetes — A Complete Guide

## Table of Contents
- [Background: Docker & Kubernetes](#background-docker--kubernetes)
- [Container Runtime Interface (CRI)](#container-runtime-interface-cri)
- [OCI Standards](#oci-standards)
- [Docker Shim](#docker-shim)
- [What is Docker, Really?](#what-is-docker-really)
- [containerd](#containerd)
- [Command Line Tools](#command-line-tools)
  - [ctr](#1-ctr)
  - [nerdctl](#2-nerdctl)
  - [crictl](#3-crictl)
- [Comparison Table](#comparison-table)
- [Summary](#summary)

---

## Background: Docker & Kubernetes

Docker's user experience made working with containers extremely simple, which is why it became the dominant container tool. Kubernetes was originally built to orchestrate Docker specifically — the two were tightly coupled, and early Kubernetes only worked with Docker.

As Kubernetes grew in popularity, other container runtimes like **Rocket** wanted to integrate. Kubernetes needed to support runtimes beyond just Docker.

---

## Container Runtime Interface (CRI)

To solve this, Kubernetes introduced the **Container Runtime Interface (CRI)** — a standard interface that allows any vendor to work as a container runtime for Kubernetes, as long as they adhere to **OCI standards**.

---

## OCI Standards

**OCI** stands for **Open Container Initiative**. It consists of two specifications:

- **Image Spec** — Defines how a container image should be built.
- **Runtime Spec** — Defines the standards for how any container runtime should be developed.

Any runtime built to these standards can work with Kubernetes via the CRI. Rocket and other OCI-compliant runtimes were now supported.

---

## Docker Shim

Docker was built before CRI was introduced and did **not** support the CRI standards natively. Since Docker was still the dominant tool, Kubernetes had to continue supporting it.

To do so, Kubernetes introduced **Docker Shim** — a hacky but temporary compatibility layer that allowed Docker to work outside the CRI. While all other runtimes worked through the CRI, Docker continued to work without it.

Eventually, maintaining Docker Shim became unnecessary overhead. In **Kubernetes v1.24**, Docker Shim was removed and Docker support was dropped from Kubernetes.

> **Note:** All Docker images continue to work because Docker followed the OCI image spec. Images built by Docker are fully compatible with `containerd` and other OCI-compliant runtimes.

---

## What is Docker, Really?

Docker is not just a container runtime — it's a collection of multiple tools bundled together:

- Docker CLI
- Docker API
- Build tools (for building images)
- Volume support
- Networking
- Security features
- Container runtime: **runc**
- Daemon managing runc: **containerd**

---

## containerd

`containerd` is CRI-compatible and can work directly with Kubernetes, just like any other supported runtime.

Key facts:
- Although originally part of Docker, `containerd` is now a **separate project**.
- It is a member of **CNCF (Cloud Native Computing Foundation)** with **graduated status**.
- It can be **installed independently** — without installing Docker.
- If you don't need Docker's other features, you can install `containerd` alone.

---

## Command Line Tools

### 1. `ctr`

| Property | Detail |
|----------|--------|
| **Made by** | containerd community |
| **Works with** | containerd |
| **Purpose** | Debugging only |
| **User-friendly?** | No |

`ctr` is a basic CLI that ships with containerd. It supports a limited set of features and is **not recommended** for production use.

**Example commands:**
```bash
# Pull an image
ctr images pull docker.io/library/redis:latest

# Run a container
ctr run docker.io/library/redis:latest my-redis
```

---

### 2. `nerdctl`

| Property | Detail |
|----------|--------|
| **Made by** | containerd community |
| **Works with** | containerd |
| **Purpose** | General purpose (like Docker CLI) |
| **User-friendly?** | Yes |

`nerdctl` is a **Docker-like CLI for containerd**. It supports almost all Docker options and even provides access to newer containerd features not yet available in Docker.

**Additional features over Docker:**
- Encrypted container images
- Lazy pulling of images
- P2P image distribution
- Image signing and verification
- Kubernetes namespaces support

**Example commands:**
```bash
# Run a container (same as docker run)
nerdctl run nginx

# Run with port mapping
nerdctl run -p 8080:80 nginx
```

Simply replace `docker` with `nerdctl` — that's it!

---

### 3. `crictl`

| Property | Detail |
|----------|--------|
| **Made by** | Kubernetes community |
| **Works with** | All CRI-compatible runtimes |
| **Purpose** | Debugging & inspection |
| **User-friendly?** | Limited |

`crictl` is a CLI utility for interacting with **any CRI-compatible container runtime** — not just containerd. It must be installed separately and is maintained by the Kubernetes community.

> **Warning:** Do not use `crictl` to create containers in production. The `kubelet` is unaware of containers created via `crictl` and will delete them.

**Example commands:**
```bash
# Pull an image
crictl pull nginx

# List images
crictl images

# List running containers
crictl ps

# Execute a command inside a container
crictl exec -it <container-id> bash

# View logs
crictl logs <container-id>

# List pods (unique to crictl — Docker didn't support this)
crictl pods
```

**Setting a specific runtime endpoint:**
```bash
# Via flag
crictl --runtime-endpoint unix:///run/containerd/containerd.sock ps

# Via environment variable
export CONTAINER_RUNTIME_ENDPOINT=unix:///run/containerd/containerd.sock
```

**Default endpoint connection order:**
1. Docker Shim
2. containerd
3. CRI-O
4. cri-dockerd

---

## Comparison Table

| Feature | `ctr` | `nerdctl` | `crictl` |
|--------|-------|-----------|---------|
| **Made by** | containerd community | containerd community | Kubernetes community |
| **Works with** | containerd only | containerd only | All CRI runtimes |
| **Purpose** | Debugging | General purpose | Debugging / Inspection |
| **Docker-like CLI?** | No | Yes | Mostly |
| **Pod awareness?** | No | No | Yes |
| **Use in production?** | No | Yes | No |

### Docker vs crictl Command Mapping

| Action | Docker | crictl |
|--------|--------|--------|
| List containers | `docker ps` | `crictl ps` |
| Exec into container | `docker exec -it` | `crictl exec -it` |
| View logs | `docker logs` | `crictl logs` |
| List images | `docker images` | `crictl images` |
| Inspect | `docker inspect` | `crictl inspect` |
| Stats | `docker stats` | `crictl stats` |
| List pods | Not supported | `crictl pods` |

---

## Summary

- **`ctr`** — Ships with containerd, debugging only, very limited features. Ignore for most use cases.
- **`nerdctl`** — Docker-like CLI for containerd, use this for general container management going forward.
- **`crictl`** — Kubernetes community tool, works across all CRI runtimes, use this for debugging on Kubernetes worker nodes.

> **Practical tip:** If your cluster previously had Docker installed, you were using `docker` commands to troubleshoot on worker nodes. Now that clusters run `containerd`, switch to **`crictl`** for troubleshooting.
