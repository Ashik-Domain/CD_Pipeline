# GitOps-Based CD Pipeline with ArgoCD, AWS EKS, and Istio

**Continuous deployment of microservices to Kubernetes using GitOps principles, with advanced traffic management and observability**

## 🎯 Overview

Kubernetes-based deployment of the Bookinfo microservices application to AWS EKS, managed declaratively using ArgoCD. Implements GitOps principles for infrastructure management, with Istio service mesh providing traffic management, security, and comprehensive observability through Grafana, Prometheus, and Kiali.

## 🏗️ Architecture

```
Git Repository (Source of Truth)
    ↓
ArgoCD (Continuous Sync)
    ↓
AWS EKS Cluster
    ├─→ Bookinfo Microservices
    ├─→ Istio Service Mesh
    └─→ Observability Stack
        ├─ Prometheus (Metrics)
        ├─ Grafana (Visualization)
        └─ Kiali (Service Mesh Dashboard)
```

## 📸 Deployment Evidence

### Application Traffic Flow
![Internal Traffic Flow](live_snapshots/app%20internal%20traffic%20flow.png)
*Istio service mesh managing microservices communication*

### Service Mesh Monitoring
![Kiali Dashboard](live_snapshots/Kiali_dashboard.png)
*Real-time visualization of service topology and health*

![Kiali Service Graph](live_snapshots/Kiali_graph.png)
*Traffic flow patterns and request rates between microservices*

### Metrics and Observability
![Prometheus Metrics](live_snapshots/Prometheus.png)
*Prometheus scraping and storing application metrics*

![Prometheus Graph](live_snapshots/Prometheus_graph.png)
*Time-series metrics visualization*

![Grafana Dashboard](live_snapshots/Grafana_prometheus.png)
*Grafana dashboards with Prometheus data source*

### Application Access
![Public Static Page](live_snapshots/Public_static_page.png)
*Productpage microservice exposed via Istio Gateway*

![Private Static Page](live_snapshots/Private_static_page.png)
*Internal service pages accessible within the mesh*

## 🔧 Technologies Used

| Category | Tools |
|----------|-------|
| GitOps | ArgoCD |
| Container Orchestration | Kubernetes (AWS EKS) |
| Service Mesh | Istio |
| Monitoring | Prometheus, Grafana, Kiali |
| Cloud Platform | AWS (EKS, EC2, VPC) |
| Application | Bookinfo Microservices |

## ✨ Key Features

- ✅ **GitOps Workflow**: Git repository as single source of truth for deployments
- ✅ **Automated Sync**: ArgoCD continuously monitors and deploys changes
- ✅ **Traffic Management**: Istio-based traffic splitting and routing
- ✅ **Observability**: Full-stack monitoring with Prometheus, Grafana, Kiali
- ✅ **Service Mesh**: Istio handling service-to-service communication
- ✅ **Canary Deployments**: Weighted traffic distribution between service versions

## 🚀 Deployment Workflow

### GitOps Principle
1. **Source of Truth**: All Kubernetes manifests stored in Git
2. **Automated Sync**: ArgoCD continuously ensures manifests are applied to the cluster
3. **Declarative Deployment**: Desired state defined in YAML manifests
4. **Self-Healing**: ArgoCD ensures cluster state matches Git state
5. **Audit Trail**: All changes tracked via Git commits

## Image Update Behavior

The Productpage deployment references the `latest` container image tag (`userashik187/productpage-app:latest`).

When the CI pipeline builds and pushes a new image to Docker Hub, Kubernetes pulls the updated image when the Productpage pod is recreated. This behavior demonstrates Kubernetes image lifecycle management rather than a fully versioned GitOps rollout.

**Learning Point**: A production enhancement would implement versioned image tags (e.g., `v1.0.0`, `v1.0.1`) or ArgoCD Image Updater for deterministic Git-driven deployments with proper rollback capabilities.

### Integration with CI Pipeline
- CI pipeline builds and pushes the Productpage container image to Docker Hub using the `latest` tag
- Kubernetes deployments reference this image in the manifests
- When Productpage pods are recreated, Kubernetes pulls the updated image automatically
- ArgoCD ensures that the Kubernetes manifests remain consistent with the Git repository

## 📊 Istio Traffic Management

The deployment includes advanced traffic management configurations:

### Virtual Service (vs.yml)
Implements weighted traffic distribution across review service versions:
- **v1**: 6% traffic
- **v2**: 17% traffic  
- **v3**: 77% traffic

Enables canary deployments and A/B testing without code changes.

### Destination Rules (ds.yml)
Defines service subsets for traffic routing:
- Version-based routing (v1, v2, v3)
- Load balancing policies
- Connection pool settings

### Gateway (bookinfo-gateway.yaml)
Exposes the application externally via Istio Ingress Gateway:
- HTTP traffic on port 8080
- Route configuration for productpage endpoints
- External access control

## 🔐 Security and Observability

**Service Mesh Security:**
- Istio-managed service-to-service communication with support for mTLS
- Service-to-service authentication capabilities
- Network policy enforcement via Istio

**Monitoring Stack:**
- **Prometheus**: Metrics collection from all pods
- **Grafana**: Custom dashboards for visualization  
- **Kiali**: Service mesh topology and health checks

## 📁 Repository Contents

```
.
├── bookinfo.yaml              # Main application manifests
├── bookinfo-gateway.yaml      # Istio Gateway configuration
├── vs.yml                     # Virtual Service for traffic routing
├── ds.yml                     # Destination Rules for subsets
├── live_snapshots/            # Screenshots of running deployment
└── README.md                  # This file
```

## 🛠️ Setup Instructions

### Prerequisites
- AWS Account with EKS access
- kubectl configured for EKS cluster
- ArgoCD installed on cluster
- Istio installed with demo profile

### Deploy Application
1. Configure ArgoCD application pointing to this repository
2. Set sync policy to automatic
3. ArgoCD will deploy all manifests to the cluster
4. Access application via Istio Ingress Gateway

### Access Observability Tools
```bash
# Kiali dashboard
istioctl dashboard kiali

# Grafana dashboard
istioctl dashboard grafana

# Prometheus UI
istioctl dashboard prometheus
```

## 🎓 What I Learned

- Implementing GitOps workflows with ArgoCD for declarative infrastructure management
- Managing service mesh traffic with Istio VirtualServices and DestinationRules
- Configuring comprehensive observability using Prometheus, Grafana, and Kiali
- Deploying and managing microservices on AWS EKS
- Implementing canary deployments with weighted traffic distribution
- Understanding service-to-service communication patterns in mesh architecture
- **Key Insight**: Difference between declarative infrastructure (GitOps) and versioned deployments (proper CD)

## 🔄 Production Improvements

This project demonstrates foundational concepts. For production use, I would implement:

- **Versioned Image Tags**: Replace `:latest` with semantic versioning (e.g., `v1.0.0`)
- **ArgoCD Image Updater**: Automate manifest updates when new images are pushed
- **Automated Rollbacks**: Configure health checks to trigger automatic rollbacks on failure
- **Secret Management**: Use AWS Secrets Manager or Sealed Secrets instead of plain Kubernetes secrets
- **Multi-environment Strategy**: Separate dev/staging/prod with environment-specific configurations
- **CI/CD Integration**: Add automated testing and deployment gates between environments

## 🔗 Related Projects

- [CI Pipeline with Jenkins](https://github.com/Ashik-Domain/DevOps_Automation_Pipeline) - Builds container images deployed by this CD pipeline

## 📝 Technical Details

**Application**: Bookinfo microservices (4 services: productpage, details, reviews, ratings)  
**Cluster**: AWS EKS (Kubernetes 1.x)  
**Service Mesh**: Istio 1.x (demo profile)  
**GitOps Tool**: ArgoCD  
**Deployment Strategy**: Rolling updates with traffic shifting  

---

**Built with** ❤️ **demonstrating modern cloud-native deployment practices**
