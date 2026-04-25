# 🛡️ BlogHub — DevSecOps Platform

A Gen-Z vibe blog platform demonstrating a complete DevSecOps lifecycle. Built with a 3-tier architecture (React, Node.js, PostgreSQL) and deployed using modern Infrastructure as Code, Kubernetes, and automated security pipelines.

![Tech Stack](https://img.shields.io/badge/React-18-61DAFB?style=flat-square&logo=react)
![Tech Stack](https://img.shields.io/badge/Node.js-20-339933?style=flat-square&logo=node.js)
![Tech Stack](https://img.shields.io/badge/PostgreSQL-16-4169E1?style=flat-square&logo=postgresql)
![Tech Stack](https://img.shields.io/badge/Kubernetes-EKS_Auto_Mode-326CE5?style=flat-square&logo=kubernetes)
![Tech Stack](https://img.shields.io/badge/Terraform-IaC-7B42BC?style=flat-square&logo=terraform)
![Tech Stack](https://img.shields.io/badge/GitHub_Actions-CI%2FCD-2088FF?style=flat-square&logo=github-actions)

---

## ✨ Features

**Application Features:**
- 📝 Create, edit, and delete blog posts with emoji vibes
- 💬 Interactive commenting system
- 🎨 Gen-Z dark UI with glassmorphism and gradients

**DevSecOps Features:**
- **Automated CI/CD**: Full GitHub Actions pipeline with build, test, and GitOps deployments.
- **Security Scanning**:
  - Container image scanning via **Trivy**
  - Infrastructure as Code (IaC) scanning via **Checkov**
  - Dependency auditing via `npm audit` (SCA)
  - Dockerfile linting via **Hadolint**
- **Kubernetes Security**: Strict `NetworkPolicies`, non-root containers, dropped capabilities, and read-only root filesystems.
- **Infrastructure as Code**: AWS EKS (Auto Mode) and VPC provisioning via Terraform.

## 🏗️ Architecture

```
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│  Blog Frontend  │─────▶│  Blog Backend   │─────▶│  PostgreSQL DB  │
│  (React + Vite) │◀─────│ (Node.js + Exp) │◀─────│   (EBS Volume)  │
│   Port 8080     │      │    Port 5000    │      │    Port 5432    │
└─────────────────┘      └─────────────────┘      └─────────────────┘
         │                        │                        │
         ▼                        ▼                        ▼
┌───────────────────────────────────────────────────────────────────┐
│                       Kubernetes (AWS EKS)                        │
│ 🔒 Strict NetworkPolicies | 🛡️ runAsNonRoot | 📦 EKS Auto Mode    │
└───────────────────────────────────────────────────────────────────┘
```

## 📁 Project Structure

```
BlogHub/
├── frontend/                # React (Vite) frontend application
├── backend/                 # Node.js Express API
├── k8s/                     # Kubernetes manifests (Deployments, NetworkPolicies)
├── terraform/               # Terraform IaC for AWS VPC and EKS Auto Mode
├── .github/workflows/       # GitHub Actions DevSecOps Pipeline
├── docker-compose.yml       # Local development orchestration
└── README.md
```

---

## 🔒 Security Implementation Details

### Continuous Integration (CI/CD)
Our `.github/workflows/ci-cd.yml` enforces security at every phase:
1. **Linting & Testing**: Code quality checks.
2. **Software Composition Analysis (SCA)**: Blocks builds on high/critical vulnerabilities.
3. **Container Build**: Generates SBOMs and provenance attestations.
4. **Image Scanning**: Trivy scans the Docker images before deployment.
5. **IaC Scanning**: Checkov ensures Terraform and K8s manifests adhere to best practices.
6. **GitOps**: Automatically updates the K8s manifest with the newly secured image tags.

### Kubernetes Security (Zero Trust)
- **NetworkPolicies**: The database only accepts traffic from the backend. The backend only accepts traffic from the frontend.
- **Pod Security**:
  - `runAsNonRoot: true` and `runAsUser: 1000` enforced on backend.
  - `readOnlyRootFilesystem: true` to prevent container drift.
  - `allowPrivilegeEscalation: false` to mitigate exploit risks.
  - Dropped all Linux capabilities (`ALL`).

---

## 🚀 Deployment (AWS EKS)

### 1. Provision Infrastructure (Terraform)
We use **EKS Auto Mode** to automatically manage node groups and system components.

```bash
cd terraform
terraform init
terraform apply
```

### 2. Deploy Application (Kubernetes)
Once the cluster is up, apply the manifests:

```bash
aws eks update-kubeconfig --region <your-region> --name <cluster-name>
kubectl apply -f k8s/manifest.yml
```

This sets up:
- Namespaces and Secrets
- Persistent Volume Claims (AWS EBS CSI)
- Deployments & Services
- Network Policies

---

## 🧑‍💻 Local Development

Use Docker Compose for an isolated local environment:

```bash
# Start Database, Backend, and Frontend
docker-compose up --build

# Run in detached mode
docker-compose up -d
```

- **Frontend**: http://localhost:8888
- **Backend API**: http://localhost:5000
- **Database**: `localhost:5432`

---

## 📡 API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/health` | Health check |
| GET | `/api/posts` | Get all posts |
| GET | `/api/posts/:id` | Get single post with comments |
| POST | `/api/posts` | Create a new post |
| PUT | `/api/posts/:id` | Update a post |
| DELETE | `/api/posts/:id` | Delete a post |
| GET | `/api/comments/post/:postId` | Get comments for a post |
| POST | `/api/comments` | Create a comment |
| DELETE | `/api/comments/:id` | Delete a comment |
