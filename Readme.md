## GOB

Below is a **coach-level, end-to-end, step-by-step guide** tailored specifically for **your MacBook Pro** and your stated goal:

> **Practice SRE from scratch → advance → AI-augmented SRE (production mindset)**

This is written so you can **execute it exactly as-is**, without guesswork, and then **progress systematically**.

---

# PART 1 — STEP-BY-STEP: Deploy Google Online Boutique on MacBook Pro

## Phase 0 — Mindset & Ground Rules (Important)

Before touching commands:

* This deployment is your **golden baseline**
* **No observability, no chaos, no AI yet**
* Treat this like a **production service Day-0**
* You will **intentionally break it later**

---

## Phase 1 — MacBook Pro One-Time Setup

### Step 1.1 — Docker Desktop

1. Install **Docker Desktop for Mac (Apple Silicon)**
2. Docker Settings → Resources:

   * **Memory:** 10–12 GB
   * **CPU:** 6–8 cores
   * **Swap:** Enabled

Restart Docker.

---

### Step 1.2 — Install Required CLI Tools

```bash
brew install kubectl kind helm git
```

Verify:

```bash
kubectl version --client
kind version
helm version
docker version
```

---

## Phase 2 — Create Local Kubernetes Cluster (kind)

### Step 2.1 — Create kind Cluster Config

Create a file `kind-multinode.yaml`:

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 30080
    hostPort: 8080

- role: worker
- role: worker
```

This allows you to access the app from your browser.

---

### Step 2.2 — Create Cluster

```bash
kind create cluster --name sre-lab --config kind-sre.yaml
```

Verify:

```bash
kubectl get nodes
kubectl get pods -A
```

---

## Phase 3 — Deploy Google Online Boutique

### Step 3.1 — Clone Official Repository

```bash
git clone https://github.com/GoogleCloudPlatform/microservices-demo.git
cd microservices-demo
```

---

### Step 3.2 — Deploy Application

```bash
kubectl apply -f release/kubernetes-manifests.yaml
```

Wait until all pods are running:

```bash
kubectl get pods
```

You should see ~11 services.

---

### Step 3.3 — Expose Frontend

```bash
kubectl port-forward svc/frontend 8080:80
```

Access in browser:

```
http://localhost:8080
```


========
Run Port-Forward in the Background (Most Common)
kubectl port-forward svc/frontend 8080:80 &


You will see something like:

[1] 12345


Now:

The process runs in the background

Your terminal is free

App stays accessible

To stop it later:

jobs
kill %1


This is perfectly acceptable for local labs.
========





---

## Phase 4 — Baseline Validation (Critical for SRE)

### Step 4.1 — Functional Validation

Manually test:

* Browse products
* Add to cart
* Checkout

If this fails → **do not proceed**

---

### Step 4.2 — Baseline Resource Snapshot

```bash
kubectl top pods
kubectl top nodes
```

Save this output.
This is your **“healthy baseline”**.

---

### Step 4.3 — Save State

```bash
kubectl get all -o wide > baseline_state.txt
```

You will compare against this later during incidents.

---

### ✅ At this point:

You have a **clean, production-like microservices system** running locally.

Do **not** modify it yet.

---