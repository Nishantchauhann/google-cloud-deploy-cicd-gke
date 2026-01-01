# Production-Grade CI/CD Pipeline on Google Cloud

🚀 End-to-end Continuous Delivery platform using Google Cloud Deploy and GKE with approval-gated production releases.

![GCP](https://img.shields.io/badge/Cloud-Google%20Cloud-blue)
![Kubernetes](https://img.shields.io/badge/Platform-Kubernetes-blue)
![CI/CD](https://img.shields.io/badge/CI%2FCD-Cloud%20Deploy-green)
![Status](https://img.shields.io/badge/Status-Production--Ready-success)


## Overview

This repository demonstrates a **production-grade Continuous Delivery (CD) pipeline** built on Google Cloud.
The system automates application promotions across **test, staging, and production** Kubernetes environments using
Google Cloud Deploy with approval-controlled production rollouts.

The project follows **enterprise DevOps and SRE best practices** including:
- Immutable artifacts
- Environment isolation
- Declarative release orchestration
- Audit-friendly deployment workflows


## Architecture

Developer
   |
   |  git push
   v
Skaffold + Cloud Build
   |
   |  build & tag images
   v
Artifact Registry (Immutable Images)
   |
   |  release creation
   v
Google Cloud Deploy
   |
   |  promote
   v
TEST GKE  →  STAGING GKE  →  PROD GKE (Manual Approval)


## Demo Walkthrough

### 1. Build Container Images
```bash
skaffold build \
  --default-repo=$REGION-docker.pkg.dev/$PROJECT_ID/web-app \
  --file-output artifacts.json

### 2. Create a Release
``` bash
gcloud deploy releases create web-app-001 \
  --delivery-pipeline web-app \
  --build-artifacts web/artifacts.json \
  --source web/

### 3. Promote to Staging
``` bash
gcloud deploy releases promote \
  --delivery-pipeline web-app \
  --release web-app-001

### 4. Approve & Deploy to Production
``` bash
gcloud deploy rollouts approve web-app-001-to-prod-0001 \
  --delivery-pipeline web-app \
  --release web-app-001
