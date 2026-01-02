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

# PART 2 — SRE ROADMAP (FROM SCRATCH → ADVANCED)

This roadmap is **deliberately layered**.
Each layer assumes mastery of the previous one.

---

## PHASE 1 — SRE Foundations (Weeks 1–3)

### Goal

Understand **how the system behaves when healthy**.

### What You Implement

1. **Observability (No AI yet)**

   * Prometheus
   * Grafana
   * OpenTelemetry
   * Distributed tracing

2. **Golden Signals**

   * Latency
   * Traffic
   * Errors
   * Saturation

3. **Dashboards**

   * Service-level dashboards
   * Dependency visualization

### Skills You Gain

* Reading production signals
* Baseline vs degradation
* Telemetry literacy

---

## PHASE 2 — Core SRE Practices (Weeks 4–6)

### Goal

Operate the system like a real SRE team.

### What You Implement

1. **SLIs / SLOs**

   * Per-service SLOs
   * Error budgets

2. **Alerting**

   * Burn rate alerts
   * Alert routing
   * Alert fatigue reduction

3. **Load Testing**

   * k6 / Locust
   * Traffic spikes
   * Sustained load

### Skills You Gain

* Reliability math
* Alert quality judgment
* User-centric reliability thinking

---

## PHASE 3 — Failure & Resilience (Weeks 7–9)

### Goal

Learn how systems fail.

### What You Implement

1. **Chaos Engineering**

   * Pod kill
   * Network latency
   * CPU/memory pressure
   * Dependency failures

2. **Blast Radius Analysis**

   * Which service breaks first?
   * Cascading failures

3. **Manual Runbooks**

   * Restart
   * Scale
   * Rollback

### Skills You Gain

* Failure intuition
* Debugging speed
* Incident response confidence

---

## PHASE 4 — AI-Augmented SRE (Weeks 10–14)

### Goal

Replace **human toil** with **intelligence**.

### AI Capabilities You Add

1. **Anomaly Detection**

   * Metrics seasonality
   * Multivariate anomalies

2. **Intelligent Alerting**

   * Alert deduplication
   * Noise suppression
   * Severity scoring

3. **RCA Automation**

   * Trace-based graph analysis
   * Probabilistic root cause ranking
   * LLM-generated incident summaries

### Skills You Gain

* ML applied to reliability
* Signal correlation
* AI-assisted decision making

---

## PHASE 5 — Autonomous SRE (Weeks 15–20)

### Goal

Build a **self-healing system**.

### What You Implement

1. **Auto-Remediation Engine**

   * Restart vs scale vs rollback logic
   * Confidence thresholds
   * Guardrails

2. **Predictive Scaling**

   * Traffic forecasting
   * Proactive HPA

3. **Learning Loop**

   * Success/failure feedback
   * Model improvement

### Skills You Gain

* Closed-loop systems
* Reliability automation
* AI trust & safety

---

# PART 3 — What This Gives You (Career Value)

After completing this:

You can confidently say:

* “I built and operated a **self-healing Kubernetes platform**”
* “I applied **AI to real SRE problems**, not demos”
* “I understand **reliability at system scale**”

This is **Staff / Principal / AI-SRE interview-grade experience**.

---

## Next Coach Step

If you want, I will continue **one layer at a time**, starting with:

**Option A:**
“Next: Install Observability (exact commands + dashboards)”

**Option B:**
“Next: Turn this into a GitHub portfolio repo”

Reply with **A or B**, and I’ll proceed exactly step by step.
