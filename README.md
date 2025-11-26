# Fullstack CI/CD Infrastructure — Docker, GitHub Actions, Terraform, AWS Fargate & CloudFront

This repository contains a complete, production-ready CI/CD pipeline and cloud infrastructure for a fullstack application (FastAPI backend + React frontend). The architecture is automated end-to-end using Docker, GitHub Actions, and Terraform, deploying to AWS Fargate (backend) and AWS S3 + CloudFront (frontend).

## Architecture Overview
### Backend (FastAPI → AWS Fargate)

#### Dockerized FastAPI backend

#### GitHub Actions performs:

Lint & Unit tests

Build Docker image

Tag image with commit SHA

Push to Docker Hub

#### Terraform builds:

VPC

Subnets

ECS Cluster

Task Definition

Fargate Service

Application Load Balancer (ALB)

Security groups

IAM roles

### Frontend (React → S3 + CloudFront)

#### React app built with npm run build

#### Terraform provisions:

Public S3 static hosting bucket

Website settings

CloudFront Distribution

S3 bucket policy (public read)

#### Frontend deployed by:

Manual: aws s3 sync build/ s3://<bucket>

OR via CI/CD

##🔧 CI/CD Workflows
### 1. Backend CI/CD (GitHub Actions)

Runs on every push:

Install Python dependencies

Run backend tests

Build Docker backend image

Tag (SHA, latest, main)

Push to Docker Hub

Trigger Terraform apply to update Fargate deployment

### 2. Frontend CI/CD

Install Node dependencies

Run tests

Build production React bundle

Sync build to S3 bucket

Invalidate CloudFront cache

###🐳 Docker Setup
Backend Dockerfile

Uses multi-stage build for optimized runtime image:

Stage 1: Install requirements

Stage 2: Copy minimal runtime dependencies

Run FastAPI app via Uvicorn

Exposes container on port 8000

Frontend Dockerfile (optional)

Not used for AWS S3 deployment but included for local Docker Compose setups.

###🧪 Testing

Backend: pytest
Frontend: React Testing Library + Jest

Both run automatically in CI.

###🌩️ AWS Infrastructure (Terraform)

The repo contains:

terraform/
 ├── backend-fargate/
 │    ├── vpc.tf
 │    ├── ecs-task.tf
 │    ├── ecs-service.tf
 │    ├── ecr.tf
 │    ├── iam.tf
 │    ├── outputs.tf
 │    ├── provider.tf
 │    ├── variables.tf
 │    ├── versions.tf
 │
 └── frontend-s3-cloudfront/
      ├── s3.tf
      ├── cloudfront.tf
      ├── outputs.tf
      ├── variables.tf
      ├── provider.tf
      ├── versions.tf

Backend Deployment Variables

container_image: Docker Hub image tag to deploy

environment: blue | green (for blue/green pattern)

service_suffix: service suffix

Frontend Deployment Variables

bucket_name: unique S3 bucket for static hosting

### Deployment Flow
Backend
cd terraform/backend-fargate
terraform init
terraform plan -var="container_image=<docker-tag>" -var="environment=blue" -var="service_suffix=blue"
terraform apply -auto-approve

Frontend
cd frontend
npm run build
aws s3 sync build/ s3://<bucket-name> --delete

###🌐Outputs

After Terraform apply:

Backend:

ALB URL

ECS service ARN

Task definition ARN

Frontend:

S3 website URL

CloudFront domain

###🛡️Blue/Green Ready

The backend infrastructure supports blue/green:

Deploy blue service (service_suffix = "blue")

Deploy green service next (service_suffix = "green")

Shift ALB target group

Destroy inactive service

### ✔️ Status

This project successfully demonstrates:

Docker containerization

Git branching strategy

CI pipelines for backend & frontend

Terraform IaC

AWS production-grade deployment

CloudFront CDN distribution

Infrastructure separation (backend vs frontend)
