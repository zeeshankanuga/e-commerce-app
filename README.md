# DevOps CI/CD Pipeline - End-to-End AWS EKS GitOps Architecture

## 1. Overview

This document describes a complete end-to-end DevOps architecture for deploying a cloud-native application on AWS using CI/CD, GitOps, Kubernetes, Service Mesh, and observability stack.

Tools used:
- GitHub
- Jenkins
- Docker
- DockerHub
- Terraform
- AWS (EKS, VPC, IAM, etc.)
- Istio
- ArgoCD
- Prometheus
- Grafana
- FluentBit
- OpenSearch / ELK
- AlertManager

---

## 2. High-Level Architecture Flow

Developer → GitHub → Jenkins → Build/Test/Scan → Docker Build → DockerHub → GitOps Repo → ArgoCD → AWS EKS → Istio → Application → Monitoring Stack

---

## 3. Application Onboarding

- Understand application architecture
- Identify tech stack
- Define environment variables
- Define secrets
- Identify dependencies (DB, cache, APIs)
- Define scaling requirements
- Define deployment strategy (Rolling/Blue-Green/Canary)
- Define monitoring and logging requirements

---

## 4. GitHub Repository

Repo structure:
- src/
- tests/
- Dockerfile
- Jenkinsfile
- helm/
- k8s/

Branching:
- main
- develop
- feature/*
- release/*
- hotfix/*

---

## 5. CI/CD Pipeline (Jenkins)

Stages:
- Checkout code from GitHub
- SonarQube code quality check
- Unit testing
- Build application
- Docker image build
- Trivy security scan
- Push image to DockerHub
- Update GitOps repository

---

## 6. DockerHub Registry

- Stores Docker images
- Maintains versioned images
- Used by Kubernetes deployments

Flow:
Code → Docker Build → Scan → Push → DockerHub

---

## 7. Terraform (Infrastructure as Code)

Provision:
- VPC (public/private subnets)
- Internet Gateway
- NAT Gateway
- Route Tables
- Security Groups
- IAM roles & policies
- EKS cluster
- Node groups
- Load balancers
- Route53
- ACM
- CloudWatch
- Secrets Manager

State management:
- S3 backend
- DynamoDB locking

---

## 8. AWS EKS (Kubernetes)

Resources:
- Namespace
- Deployment
- Service
- ConfigMap
- Secret
- Ingress
- HPA
- PDB
- ServiceAccount
- NetworkPolicy

Add-ons:
- Metrics Server
- Cluster Autoscaler
- AWS Load Balancer Controller
- ExternalDNS

---

## 9. Istio Service Mesh

Features:
- Traffic routing
- Canary deployment
- Blue-green deployment
- mTLS security
- Circuit breaking
- Retry policies
- Rate limiting

Components:
- Istiod
- Envoy proxy
- Ingress gateway

---

## 10. ArgoCD (GitOps)

Flow:
Jenkins → GitOps Repo Update → ArgoCD → Sync → EKS Deployment

Features:
- Auto sync
- Self-healing
- Drift detection
- Rollback

---

## 11. Monitoring (Prometheus + Grafana)

Prometheus:
- Cluster metrics
- Node metrics
- Pod metrics
- Application metrics

Grafana:
- Cluster dashboard
- Node dashboard
- Application dashboard
- Istio dashboard
- API latency dashboard

---

## 12. Logging (FluentBit + OpenSearch)

- FluentBit collects logs
- Sends to OpenSearch/ELK
- Used for search and debugging

---

## 13. Alerting (AlertManager)

Alerts:
- Node down
- Pod crash
- High CPU/memory
- Deployment failure
- High latency
- High error rate
- Certificate expiry

---

## 14. Security

AWS:
- IAM least privilege
- Security groups
- KMS encryption
- Secrets Manager

Kubernetes:
- RBAC
- Network policies
- Namespace isolation

Container:
- Trivy image scanning

Service Mesh:
- mTLS
- Authorization policies

---

## 15. End-to-End Deployment Flow

Developer → GitHub → Jenkins → Docker Build → DockerHub → GitOps Repo → ArgoCD → EKS → Istio → App → Monitoring

---

## 16. Summary

This is a fully automated, production-grade DevOps architecture using GitOps, Kubernetes, CI/CD, and observability stack on AWS.