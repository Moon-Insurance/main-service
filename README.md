
---

# 🌙 Main Service - Moon Insurance Management System

This is the **Main Service** repository for the **Moon Insurance Management System**, a cloud-native, microservices-based platform built to manage agents, product sales, branch performance, and analytics across MoonInsurance. This repository contains the **reusable GitHub Actions CI/CD workflow** used by all service repositories in the system.

---

## 📁 Repository Structure

```
.github/
  └── workflows/
        └── ci_c_pipeline.yaml         # Reusable CI/CD workflow
k8s/
  └── deployment-blue.yaml             # Blue deployment manifest
  └── deployment-green.yaml            # Green deployment manifest
  └── service.yaml                     # Kubernetes Service manifest
  └── integration-test-cron.yaml       # Cron job for integration testing
  └── aggregator_cron_job.yaml         # Aggregator scheduled job
```

---

## 🚀 Purpose

The **Main Service** acts as the central CI/CD orchestration point across all microservices. It enables:

* Shared CI/CD logic across services (no duplication)
* Secure and automated deployments to GKE (Google Kubernetes Engine)
* Blue-Green deployment strategy to maintain zero downtime
* Integration testing as part of the deployment lifecycle

---

## 🛠️ Features

* ✅ Docker image builds and push to Google Artifact Registry
* ✅ Kubernetes deployment to GKE Autopilot cluster
* ✅ Blue-Green deployment with version switching
* ✅ Secret injection via Kubernetes secrets
* ✅ Post-deployment integration test via cron jobs

---

## 📡 How to Use the Code (for Other Services)

1. **Each microservice** (Agent, Integration, Product, Notification, Aggregator) should include a workflow that calls this reusable pipeline.

2. **Example Usage:**

```yaml
jobs:
  call-pipeline:
    uses: Moon-Insurance/main-service/.github/workflows/ci_c_pipeline.yaml@main
    with:
      service_name: "agent-service"          # Change per service
      aggregator_service: false              # true only for aggregator
    secrets:
      GCP_SA_KEY: ${{ secrets.GCP_SA_KEY }}
      GCP_PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}
      GKE_CLUSTER_NAME: ${{ secrets.GKE_CLUSTER_NAME }}
      GKE_REGION: ${{ secrets.GKE_REGION }}
      DATABASE_USER: ${{ secrets.DATABASE_USER }}
      DATABASE_PASSWORD: ${{ secrets.DATABASE_PASSWORD }}
      DATABASE_ENDPOINT: ${{ secrets.DATABASE_ENDPOINT }}
      DATABASE_NAME: ${{ secrets.DATABASE_NAME }}
      REDSHIFT_USER: ${{ secrets.REDSHIFT_USER }}              # (only for aggregator)
      REDSHIFT_PASSWORD: ${{ secrets.REDSHIFT_PASSWORD }}
      REDSHIFT_HOST: ${{ secrets.REDSHIFT_HOST }}
      REDSHIFT_DATABASE: ${{ secrets.REDSHIFT_DATABASE }}
```

3. **Trigger:** This workflow runs automatically when a commit is pushed to the `master` branch in the microservice repo.

---

## 🔐 Security Considerations

* All secrets (e.g., DB credentials, Redshift info, service keys) are stored in **GitHub Secrets**.
* Secrets are **securely passed to workflows** and injected into pods via **Kubernetes Secrets**.
* Docker images are stored in **Google Artifact Registry**, not DockerHub.
* Redshift connectivity is established using a **VPN tunnel**, keeping endpoints private.

---

## 📘 Documentation & Context

This repository belongs to the **Moon Insurance GitHub Organization**, which hosts all microservices that make up the **Moon Insurance Management System**, including:

* **Agent Service** – Manage agent records
* **Integration Service** – Receive sales data from core systems
* **Notification Service** – Trigger alerts and reminders
* **Product Service** – Manage product info
* **Aggregator Service** – Aggregate and sync insights with AWS Redshift

Each microservice uses this **Main Service** to build, test, and deploy code reliably and securely.

---
