# Kubernetes Pod — Hands-On YAML Demo

## Table of Contents
- [Goal](#goal)
- [Choosing an Editor](#choosing-an-editor)
- [Creating the Pod Definition File](#creating-the-pod-definition-file)
  - [Step 1 — Open the File](#step-1--open-the-file)
  - [Step 2 — Add Root-Level Fields](#step-2--add-root-level-fields)
  - [Step 3 — Define Metadata](#step-3--define-metadata)
  - [Step 4 — Define Spec & Containers](#step-4--define-spec--containers)
- [Final pod.yaml](#final-podyaml)
- [Verify the File](#verify-the-file)
- [Apply & Check the Pod](#apply--check-the-pod)
- [Key Tips & Gotchas](#key-tips--gotchas)

---

## Goal

Create a pod **without** using `kubectl run`. Instead, write a YAML definition file manually and use `kubectl apply` to create the pod from it.

---

## Choosing an Editor

| OS | Basic Editor | Recommended Editor |
|----|-------------|-------------------|
| Windows | Notepad | Notepad++ |
| Linux | vi | Vim |
| Any | — | VS Code (with YAML extension) |

An editor with **YAML language support** helps catch indentation errors early. For this demo, we use **Vim** on Linux.

---

## Creating the Pod Definition File

### Step 1 — Open the File

```bash
vim pod.yaml
```

---

### Step 2 — Add Root-Level Fields

Every Kubernetes definition file starts with four mandatory root-level fields:

```yaml
apiVersion:
kind:
metadata:
spec:
```

Fill them in for a pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
spec:
```

> **Note:** `kind: Pod` — the `P` is capital. Kubernetes is **case-sensitive**.

---

### Step 3 — Define Metadata

`metadata` is a dictionary. Add `name` and `labels` as children (indented under it):

```yaml
metadata:
  name: nginx
  labels:
    app: nginx
    tier: front-end
```

**Indentation rules to remember:**
- `name` and `labels` are **siblings** — they must be on the same vertical line.
- `app` and `tier` are **children of `labels`** — they must be indented further right.
- Use **2 spaces** consistently. Do **not** use tabs.

---

### Step 4 — Define Spec & Containers

`spec` is also a dictionary. It contains a `containers` key, which is a **list** (array) of container objects.

```yaml
spec:
  containers:
    - name: nginx
      image: nginx
```

**Things to note:**
- The `-` before `name` marks the start of a **list item**.
- `name` here is the name of the **container inside the pod** — not the pod itself.
- `image` is the Docker Hub image name. If using a private registry, provide the full image path.

**Adding multiple containers** (optional):

```yaml
spec:
  containers:
    - name: nginx
      image: nginx
    - name: busybox
      image: busybox
```

Each `-` starts a new container object. For this demo, we stick to a single container.

---

## Final `pod.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
    tier: front-end
spec:
  containers:
    - name: nginx
      image: nginx
```

**Save and exit Vim:**
```
Esc → :wq → Enter
```

---

## Verify the File

After saving, confirm the file contents look correct:

```bash
cat pod.yaml
```

Expected output:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
    tier: front-end
spec:
  containers:
    - name: nginx
      image: nginx
```

---

## Apply & Check the Pod

**Create the pod from the YAML file:**
```bash
kubectl apply -f pod.yaml
```

**Check if the pod is running:**
```bash
kubectl get pods
```

**Get detailed info about the pod:**
```bash
kubectl describe pod nginx
```

---

## Key Tips & Gotchas

| Rule | Detail |
|------|--------|
| `kind` is case-sensitive | Use `Pod`, not `pod` |
| Use 2 spaces for indentation | Never use tabs — YAML will break |
| Siblings must align vertically | `name` and `labels` must have the same indent under `metadata` |
| `containers` is a list | Each item starts with `-` |
| `name` under `containers` = container name | Different from the pod name under `metadata` |
| Private registry images | Specify the full image path, not just the image name |

---

> **Next:** In upcoming lectures, we'll explore IDE tips, YAML extensions, and more advanced pod configurations including resource limits, environment variables, and volume mounts.
