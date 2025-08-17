# Coworking Space Service – Analytics API Deployment

This repository contains the **deployment artifacts** for the **Coworking Space Service Analytics API**.  
The service provides **business analysts** with insights into coworking space usage by exposing analytics APIs.  

The application is deployed using a **containerized microservices approach** with **Kubernetes on Amazon EKS**, following modern CI/CD and DevOps best practices.

---

## Architecture & Workflow

The deployment process integrates the following components:

- **Amazon EKS** – Managed Kubernetes cluster for container orchestration  
- **PostgreSQL** – Persistent relational database backend deployed as a Kubernetes workload  
- **Amazon ECR** – Private Docker registry for hosting application images  
- **AWS CodeBuild** – Continuous Integration (CI) service to build and push container images  
- **Kubernetes ConfigMaps & Secrets** – Manage environment variables and credentials  
- **CloudWatch Container Insights** – Centralized logging and observability  

### 🗄️ Database Setup

This project requires a PostgreSQL database.  If one does not already exist, create it first and populate it with data using create table and seed .sql scripts in "/db" folder

**High-Level Flow:**
1. Developer pushes changes → GitHub triggers AWS CodeBuild  
2. CodeBuild builds Docker image → tags with `$CODEBUILD_BUILD_NUMBER` → pushes to Amazon ECR  
3. Kubernetes Deployment manifest references the new image from ECR  
4. EKS rolls out the updated service, exposed via a Kubernetes LoadBalancer  
5. CloudWatch collects logs & metrics for monitoring  

---

## Build & Release Strategy

### Dockerization
- The **Dockerfile** builds an image with `app.py` as the entrypoint.  
- Containers run the API service on port **5153**  

### Continuous Integration (CI)
- **CodeBuild** pipeline defined in `buildspec.yaml`  
- Steps:  
  - Authenticate to Amazon ECR  
  - Build Docker image & tag with build number($CODEBUILD_BUILD_NUMBER)  
  - Push to ECR  

### Kubernetes Deployment
- **PostgreSQL** runs inside the cluster with persistent storage  
- **ConfigMaps** provide DB host, port, user, name  
- **Secrets** store sensitive credentials (DB password)  
- **Deployment YAML** defines replicas, probes and container settings  
- **Service of type LoadBalancer** exposes the API externally  

### Observability
- Logs and metrics collected via **CloudWatch Container Insights**  
- Health checks via `health_check` and `readiness_check`  

---

## Deploying Changes

For an experienced developer, releasing new builds follows this workflow:

1. Update application code in `app.py`  
2. Commit and push changes → triggers **CodeBuild**  
3. CodeBuild builds & pushes new Docker image to ECR  
4. Update Kubernetes manifests with new image tag (or use `:latest`)  
5. Apply manifests