# Kubernetes Pod Configuration — YAML-Based Setup

## Table of Contents
- [Overview](#overview)
- [The Four Top-Level Fields](#the-four-top-level-fields)
  - [apiVersion](#1-apiversion)
  - [kind](#2-kind)
  - [metadata](#3-metadata)
  - [spec](#4-spec)
- [YAML Structure & Indentation Rules](#yaml-structure--indentation-rules)
- [Quick Reference Table](#quick-reference-table)
- [Sample Pod Definition File](#sample-pod-definition-file)

---

## Overview

Kubernetes uses **YAML files** as inputs for creating objects such as:

- Pods
- ReplicaSets
- Deployments
- Services

All of these follow a **similar structure**. Every Kubernetes definition file must contain **four top-level (root-level) fields** — these are required and must always be present.

---

## The Four Top-Level Fields

```yaml
apiVersion:
kind:
metadata:
spec:
```

---

### 1. `apiVersion`

The version of the Kubernetes API being used to create the object.

| Object | apiVersion |
|--------|-----------|
| Pod | `v1` |
| Service | `v1` |
| ReplicaSet | `apps/v1beta` |
| Deployment | `apps/v1beta` |

For creating a **Pod**, use:
```yaml
apiVersion: v1
```

---

### 2. `kind`

The type of Kubernetes object you want to create.

| kind | Description |
|------|-------------|
| `Pod` | A single pod |
| `ReplicaSet` | Manages multiple pod replicas |
| `Deployment` | Manages deployments with rolling updates |
| `Service` | Exposes pods over the network |

For a pod:
```yaml
kind: Pod
```

---

### 3. `metadata`

Data **about** the object — its name, labels, etc. Unlike `apiVersion` and `kind` (which are plain strings), `metadata` is a **dictionary**.

```yaml
metadata:
  name: my-app-pod
  labels:
    app: MyApp
    tier: front-end
```

**Key rules for `metadata`:**

- `name` and `labels` are children of `metadata` — they must be indented to the right.
- `name` and `labels` are siblings — they must have the **same indentation level**.
- You can **only** use fields that Kubernetes expects under `metadata` (e.g., `name`, `labels`, `annotations`). You cannot add arbitrary fields here.
- Under `labels`, however, you **can** add any key-value pair you want.

**Why labels matter:**

Labels help you group and filter pods later. For example, if you have hundreds of front-end pods and hundreds of back-end pods running, you can filter them easily using labels:

```yaml
labels:
  tier: front-end   # or back-end, database, etc.
```

---

### 4. `spec`

The **specification** section — this is where you describe *what* goes inside the object. For a pod, this means defining the container(s) and the image to use.

The content of `spec` **varies depending on the object type**, so always refer to the [Kubernetes documentation](https://kubernetes.io/docs/) for the correct format.

```yaml
spec:
  containers:
    - name: nginx-container
      image: nginx
```

---

## YAML Structure & Indentation Rules

Indentation in YAML defines the parent-child relationship between fields. Getting it wrong will cause errors.

**Correct — siblings at the same level:**
```yaml
metadata:
  name: my-app-pod      # same indent = siblings
  labels:               # same indent = siblings
    app: MyApp
```

**Incorrect — unequal sibling indentation:**
```yaml
metadata:
  name: my-app-pod
      labels:           # wrong! labels becomes a child of name
    app: MyApp
```

**Incorrect — child not indented from parent:**
```yaml
metadata:
name: my-app-pod        # wrong! name is at same level as metadata
labels:                 # wrong! should be under metadata
  app: MyApp
```

> **Rule of thumb:** Child fields must always have *more* spaces than their parent. Sibling fields must have the *same* number of spaces.

---

## Quick Reference Table

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `apiVersion` | String | Yes | Kubernetes API version |
| `kind` | String | Yes | Type of object (Pod, Deployment, etc.) |
| `metadata` | Dictionary | Yes | Name, labels, and other object metadata |
| `spec` | Dictionary | Yes | Object-specific configuration |

---

## Sample Pod Definition File

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: MyApp
    tier: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
```

**To create the pod using this file:**
```bash
kubectl apply -f pod-definition.yaml
```

**To verify the pod is running:**
```bash
kubectl get pods
kubectl describe pod my-app-pod
```

---

> **Next Steps:** In the upcoming lectures, we will explore the `spec` section in detail and learn how to configure containers, resource limits, environment variables, and more.
