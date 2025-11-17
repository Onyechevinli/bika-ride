---
title: "Deploying a webapp to Azure using the deploy.sh scripting to automate the Terraform, GitHub Action, and other designated processes."
datePublished: Sun Nov 16 2025 15:41:26 GMT+0000 (Coordinated Universal Time)
cuid: cmi1vva8p000002jlbx0p1dd0
slug: deploying-a-webapp-to-azure-using-the-deploysh-scripting-to-automate-the-terraform-github-action-and-other-designated-processes
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1763361875908/44df5bd6-557f-4aeb-9edf-9a87a6c4643f.png
tags: docker, github, kubernetes, vscode, terraform, dockerfile, azure-devops, github-actions, bash-scripting, devops-journey, aksazure-kubernetes-services, acr

---

What we are doing is similar to what we did in the previous project. What is different is that it has been hosted on Azure AKS, and this time we will be using the `deploy.sh` scripting to automate all processes, from building the image file to creating the Terraform infrastructure code and deploying all resources on Azure, to pushing the image file to ACR and linking it to AKS.

A [`deploy.sh`](http://deploy.sh) is a shell script designed to automate the deployment of applications, code, or files to a server. It contains a series of commands that execute a deployment workflow, often using SSH to transfer files and run necessary tasks on a remote machine. To use one, you typically make it executable with `chmod +x` [`deploy.sh`](http://deploy.sh) and then run it from your terminal.

## Azure AKS Web Application with CI/CD Pipeline.

This project provides a complete end-to-end solution for deploying a containerized web application to Azure Kubernetes Service (AKS) with automated CI/CD from GitHub to Azure.

### 🏗️ Architecture Overview GitHub Repository:

↓ (Push/PR) GitHub Actions CI/CD

↓ (Build & Push) Azure Container Registry (ACR)

↓ (Pull Images) Azure Kubernetes Service (AKS)

↓ (Expose) Load Balancer / Ingress

## 📦 What's Included

### \### Infrastructure (Terraform)

\- \*\*Azure Resource Group\*\*: Container for all resources

\- \*\*Azure Kubernetes Service (AKS)\*\*: Managed Kubernetes cluster

\- \*\*Azure Container Registry (ACR)\*\*: Private container image registry

\- \*\*Service Principal\*\*: Authentication for GitHub Actions

\- \*\*RBAC Permissions\*\*: Proper role assignments for ACR and AKS integration

main.tf

```json
# Configure the Azure Provider
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~>3.0"
    }
    azuread = {
      source  = "hashicorp/azuread"
      version = "~>2.0"
    }
    random = {
      source  = "hashicorp/random"
      version = "~>3.0"
    }
  }
}

# Configure the Microsoft Azure Provider
provider "azurerm" {
  features {}
}

# Generate a random suffix for unique naming
resource "random_string" "suffix" {
  length  = 6
  special = false
  upper   = false
}

# Create a resource group
resource "azurerm_resource_group" "main" {
  name     = var.resource_group_name
  location = var.location

  tags = {
    Environment = var.environment
    Project     = var.project_name
  }
}

# Create Azure Container Registry
resource "azurerm_container_registry" "main" {
  name                = "${var.acr_name}${random_string.suffix.result}"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  sku                 = var.acr_sku
  admin_enabled       = true

  tags = {
    Environment = var.environment
    Project     = var.project_name
  }
}

# Create AKS cluster
resource "azurerm_kubernetes_cluster" "main" {
  name                = var.aks_cluster_name
  location            = azurerm_resource_group.main.location
  resource_group_name = azurerm_resource_group.main.name
  dns_prefix          = "${var.aks_cluster_name}-dns"
  

  default_node_pool {
    name       = "default"
    node_count = var.node_count
    vm_size    = "Standard_D2s_v3"

    upgrade_settings {
      max_surge = "10%"
    }
  }

  identity {
    type = "SystemAssigned"
  }

  network_profile {
    network_plugin    = "kubenet"
    load_balancer_sku = "standard"
  }

  tags = {
    Environment = var.environment
    Project     = var.project_name
  }
}

# Grant AKS pull access to ACR
resource "azurerm_role_assignment" "aks_acr_pull" {
  principal_id                     = azurerm_kubernetes_cluster.main.kubelet_identity[0].object_id
  role_definition_name             = "AcrPull"
  scope                            = azurerm_container_registry.main.id
  skip_service_principal_aad_check = true
}

# Create Azure AD application for GitHub Actions
resource "azuread_application" "github_actions" {
  display_name = "${var.project_name}-github-actions"
}

# Create service principal for the application
resource "azuread_service_principal" "github_actions" {
  client_id = azuread_application.github_actions.client_id
}

# Create password for the service principal
resource "azuread_service_principal_password" "github_actions" {
  service_principal_id = azuread_service_principal.github_actions.object_id
  end_date            = "2025-12-31T23:59:59Z"
}

# Assign Contributor role to the service principal for the resource group
resource "azurerm_role_assignment" "github_actions_contributor" {
  scope                = azurerm_resource_group.main.id
  role_definition_name = "Contributor"
  principal_id         = azuread_service_principal.github_actions.object_id
}

# Assign AcrPush role to the service principal for ACR
resource "azurerm_role_assignment" "github_actions_acr_push" {
  scope                = azurerm_container_registry.main.id
  role_definition_name = "AcrPush"
  principal_id         = azuread_service_principal.github_actions.object_id
}
```

variables.tf

```json
variable "resource_group_name" {
  description = "Name of the resource group"
  type        = string
  default     = "rg-webapp"
}

variable "location" {
  description = "Azure region for resources"
  type        = string
  default     = "East US"
}

variable "environment" {
  description = "Environment name for tagging"
  type        = string
  default     = "Development"
}

variable "project_name" {
  description = "Project name for tagging"
  type        = string
  default     = "AKS WebApp"
}

# AKS Configuration
variable "aks_cluster_name" {
  description = "Name of the AKS cluster"
  type        = string
  default     = "aks-webapp-cluster"
}

variable "kubernetes_version" {
  description = "Kubernetes version for AKS cluster"
  type        = string
  default     = "1.33.4"
}

variable "node_count" {
  description = "Number of nodes in the default node pool"
  type        = number
  default     = 2

  validation {
    condition     = var.node_count >= 1 && var.node_count <= 10
    error_message = "Node count must be between 1 and 10."
  }
}

variable "node_vm_size" {
  description = "VM size for the AKS nodes"
  type        = string
  default     = "Standard_D2s_v3"
}

# ACR Configuration
variable "acr_name" {
  description = "Name of the Azure Container Registry (will have random suffix)"
  type        = string
  default     = "acrdemoapp"

  validation {
    condition     = can(regex("^[a-zA-Z0-9]*$", var.acr_name))
    error_message = "ACR name can only contain alphanumeric characters."
  }
}

variable "acr_sku" {
  description = "SKU for the Azure Container Registry"
  type        = string
  default     = "Basic"

  validation {
    condition     = contains(["Basic", "Standard", "Premium"], var.acr_sku)
    error_message = "ACR SKU must be Basic, Standard, or Premium."
  }
}

# Application Configuration
variable "app_name" {
  description = "Name of the application"
  type        = string
  default     = "webapp-demo"
}

variable "app_namespace" {
  description = "Kubernetes namespace for the application"
  type        = string
  default     = "default"
}
```

outputs.tf

```json
output "resource_group_name" {
  description = "Name of the created resource group"
  value       = azurerm_resource_group.main.name
}

output "resource_group_location" {
  description = "Location of the resource group"
  value       = azurerm_resource_group.main.location
}

# AKS Outputs
output "aks_cluster_name" {
  description = "Name of the AKS cluster"
  value       = azurerm_kubernetes_cluster.main.name
}

output "aks_cluster_id" {
  description = "ID of the AKS cluster"
  value       = azurerm_kubernetes_cluster.main.id
}

output "aks_fqdn" {
  description = "FQDN of the AKS cluster"
  value       = azurerm_kubernetes_cluster.main.fqdn
}

output "kube_config" {
  description = "Kubernetes configuration"
  value       = azurerm_kubernetes_cluster.main.kube_config_raw
  sensitive   = true
}

# ACR Outputs
output "acr_name" {
  description = "Name of the Azure Container Registry"
  value       = azurerm_container_registry.main.name
}

output "acr_login_server" {
  description = "Login server URL for the Azure Container Registry"
  value       = azurerm_container_registry.main.login_server
}

output "acr_admin_username" {
  description = "Admin username for the Azure Container Registry"
  value       = azurerm_container_registry.main.admin_username
  sensitive   = true
}

output "acr_admin_password" {
  description = "Admin password for the Azure Container Registry"
  value       = azurerm_container_registry.main.admin_password
  sensitive   = true
}

# GitHub Actions Service Principal Outputs
output "github_actions_client_id" {
  description = "Client ID for GitHub Actions service principal"
  value       = azuread_application.github_actions.client_id
}

output "github_actions_client_secret" {
  description = "Client secret for GitHub Actions service principal"
  value       = azuread_service_principal_password.github_actions.value
  sensitive   = true
}

output "github_actions_tenant_id" {
  description = "Tenant ID for GitHub Actions service principal"
  value       = data.azurerm_client_config.current.tenant_id
}

output "github_actions_subscription_id" {
  description = "Subscription ID for GitHub Actions service principal"
  value       = data.azurerm_client_config.current.subscription_id
}

# Data source for current client configuration
data "azurerm_client_config" "current" {}
```

terraform.tfvars

```json
# Example Terraform variables file
# Copy this to terraform.tfvars and modify as needed

# Basic Configuration
resource_group_name = "rg-myapp-prod"
location           = "East US"
environment        = "Production"
project_name       = "My AKS Application"

# AKS Configuration
aks_cluster_name   = "aks-myapp-prod"
kubernetes_version = "1.27"
node_count        = 3
node_vm_size      = "Standard_DS2_v2"

# ACR Configuration
acr_name = "acrmyapp"
acr_sku  = "Standard"

# Application Configuration
app_name      = "myapp"
app_namespace = "production"
```

### \### Application

\- \*\*Node.js Web Application\*\*: Production-ready Express.js app with health checks

\- \*\*Docker Container\*\*: Optimized multi-stage build with security best practices

\- \*\*Kubernetes Manifests\*\*: Deployment, Service, and Ingress configurations

app/.dockerignore

```json
node_modules
npm-debug.log*
yarn-debug.log*
yarn-error.log*
.git
.gitignore
README.md
Dockerfile
.dockerignore
.nyc_output
coverage
.env
.env.local
.env.development.local
.env.test.local
.env.production.local
```

app/Dockerfile

```dockerfile
# Use Node.js 18 Alpine image for smaller size
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install the dependencies, and generate the package-lock.json file automatically.
RUN npm install

# Install production dependencies only
RUN npm ci --only=production && npm cache clean --force

# Create non-root user for security
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Copy application code
COPY --chown=nodejs:nodejs . .

# Switch to non-root user
USER nodejs

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node -e "require('http').get('http://localhost:3000/health', (res) => { \
    if (res.statusCode === 200) process.exit(0); else process.exit(1); \
  }).on('error', () => process.exit(1));"

# Start application
CMD ["npm", "start"]
```

app/package.json

```json
{
  "name": "webapp-demo",
  "version": "1.0.0",
  "description": "Demo web application for AKS deployment",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "test": "jest"
  },
  "keywords": [
    "nodejs",
    "express",
    "docker",
    "kubernetes",
    "azure"
  ],
  "author": "Umeokoli Vincent Tochukwu",
  "license": "MIT",
  "dependencies": {
    "express": "^4.18.2",
    "helmet": "^7.0.0",
    "morgan": "^1.10.0",
    "cors": "^2.8.5"
  },
  "devDependencies": {
    "nodemon": "^3.0.1",
    "jest": "^29.5.0",
    "supertest": "^6.3.3"
  },
  "engines": {
    "node": ">=18.0.0"
  }
}
```

app/server.js

```javascript
const express = require('express');
const helmet = require('helmet');
const morgan = require('morgan');
const cors = require('cors');
const path = require('path');

const app = express();
const PORT = process.env.PORT || 3000;

// Security middleware
app.use(helmet());
app.use(cors());

// Logging middleware
app.use(morgan('combined'));

// Parse JSON bodies
app.use(express.json());

// Serve static files
app.use(express.static(path.join(__dirname, 'public')));

// Health check endpoint
app.get('/health', (req, res) => {
  res.status(200).json({
    status: 'healthy',
    timestamp: new Date().toISOString(),
    uptime: process.uptime(),
    environment: process.env.NODE_ENV || 'development',
    version: process.env.npm_package_version || '1.0.0'
  });
});

// API endpoints
app.get('/api/info', (req, res) => {
  res.json({
    message: 'This is UMEOKOLI VINCENT TOCHUKWU, a Devops Engineer. Welcome to the AKS Web Application Demo!',
    version: '1.0.0',
    platform: process.platform,
    nodeVersion: process.version,
    environment: process.env.NODE_ENV || 'development',
    timestamp: new Date().toISOString()
  });
});

app.get('/api/users', (req, res) => {
  // Mock user data
  const users = [
    { id: 1, name: 'Umeokoli Vincent Tochukwu', email: 'umeokolivincent@gmail.com' },
    { id: 2, name: 'Okereke Glory Onyeche', email: 'umeoke63@gmail.com' },
    { id: 3, name: 'Ozumba Johnpaul Abuchi', email: 'ozumbajohnpaul@example.com' }
  ];

  res.json({
    data: users,
    total: users.length
  });
});

// Main route
app.get('/', (req, res) => {
  res.send(`
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>AKS WebApp Demo</title>
        <style>
            body {
                font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
                max-width: 800px;
                margin: 0 auto;
                padding: 20px;
                background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
                color: white;
                min-height: 100vh;
            }
            .container {
                background: rgba(255, 255, 255, 0.1);
                padding: 30px;
                border-radius: 15px;
                backdrop-filter: blur(10px);
                box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.37);
            }
            h1 {
                color: #fff;
                text-align: center;
                margin-bottom: 30px;
            }
            .info-card {
                background: rgba(255, 255, 255, 0.2);
                padding: 20px;
                margin: 15px 0;
                border-radius: 10px;
                border: 1px solid rgba(255, 255, 255, 0.1);
            }
            .btn {
                background: #4CAF50;
                color: white;
                padding: 10px 20px;
                border: none;
                border-radius: 5px;
                cursor: pointer;
                margin: 5px;
                text-decoration: none;
                display: inline-block;
            }
            .btn:hover {
                background: #45a049;
            }
            .status {
                color: #4CAF50;
                font-weight: bold;
            }
        </style>
    </head>
    <body>
        <div class="container">
            <h1>🚀 AKS WebApp Demo by umeokoli vincent</h1>
            <div class="info-card">
                <h3>Application Status: <span class="status">Running</span></h3>
                <p><strong>Platform:</strong> Azure Kubernetes Service (AKS)</p>
                <p><strong>Container Registry:</strong> Azure Container Registry (ACR)</p>
                <p><strong>CI/CD:</strong> GitHub Actions</p>
                <p><strong>Node.js Version:</strong> ${process.version}</p>
                <p><strong>Environment:</strong> ${process.env.NODE_ENV || 'development'}</p>
            </div>

            <div class="info-card">
                <h3>API Endpoints</h3>
                <a href="/api/info" class="btn">📋 App Info</a>
                <a href="/api/users" class="btn">👥 Users</a>
                <a href="/health" class="btn">💊 Health Check</a>
            </div>

            <div class="info-card">
                <h3>Features Demonstrated</h3>
                <ul>
                    <li>✅ Containerized Node.js application</li>
                    <li>✅ Kubernetes deployment with health checks</li>
                    <li>✅ Azure Container Registry integration</li>
                    <li>✅ GitHub Actions CI/CD pipeline</li>
                    <li>✅ Infrastructure as Code with Terraform</li>
                    <li>✅ Production-ready security headers</li>
                    <li>✅ Monitoring and logging</li>
                </ul>
            </div>
        </div>
    </body>
    </html>
  `);
});

// 404 handler
app.use('*', (req, res) => {
  res.status(404).json({
    error: 'Not Found',
    message: 'The requested resource was not found',
    path: req.originalUrl
  });
});

// Error handler
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({
    error: 'Internal Server Error',
    message: process.env.NODE_ENV === 'production' ? 'Something went wrong!' : err.message
  });
});

// Start server
app.listen(PORT, '0.0.0.0', () => {
  console.log(`🚀 Server running on port ${PORT}`);
  console.log(`🌍 Environment: ${process.env.NODE_ENV || 'development'}`);
  console.log(`📊 Health check: http://localhost:${PORT}/health`);
});

module.exports = app;
```

k8s/deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-demo
  namespace: webapp-demo
  labels:
    app: webapp-demo
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp-demo
  template:
    metadata:
      labels:
        app: webapp-demo
    spec:
      containers:
      - name: webapp-demo
        image: __ACR_LOGIN_SERVER__/webapp-demo:__IMAGE_TAG__
        ports:
        - containerPort: 3000
        env:
        - name: NODE_ENV
          value: "production"
        - name: PORT
          value: "3000"
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "200m"
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: webapp-demo-service
  namespace: webapp-demo
  labels:
    app: webapp-demo
spec:
  selector:
    app: webapp-demo
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
  type: LoadBalancer            # ← CHANGE to THIS, instead of ClusterIP
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: webapp-demo-ingress
  namespace: webapp-demo
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: webapp-demo.__AKS_CLUSTER_NAME__.__LOCATION__.aksapp.io
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: webapp-demo-service
            port:
              number: 80
```

k8s/namespace.yaml

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: webapp-demo
  labels:
    name: webapp-demo
    environment: development
```

### \### CI/CD Pipeline

\- \*\*GitHub Actions Workflow\*\*: Automated build, test, and deployment

\- \*\*Container Image Management\*\*: Automatic tagging and registry push

\- \*\*Blue-Green Deployments\*\*: Zero-downtime deployment strategy

.github/workflows/deploy.yml

```json
name: Build and Deploy to AKS

on:
  push:
    branches:
      - main
      - develop
  pull_request:
    branches:
      - main

env:
  AZURE_CONTAINER_REGISTRY: ${{ secrets.ACR_LOGIN_SERVER }}
  CONTAINER_NAME: webapp-demo
  RESOURCE_GROUP: ${{ secrets.RESOURCE_GROUP }}
  CLUSTER_NAME: ${{ secrets.CLUSTER_NAME }}
  IMAGE_PULL_SECRET_NAME: acr-secret

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v3

    - name: Log in to Azure Container Registry
      uses: docker/login-action@v3
      with:
        registry: ${{ secrets.ACR_LOGIN_SERVER }}
        username: ${{ secrets.ACR_USERNAME }}
        password: ${{ secrets.ACR_PASSWORD }}

    - name: Extract metadata
      id: meta
      uses: docker/metadata-action@v5
      with:
        images: ${{ secrets.ACR_LOGIN_SERVER }}/webapp-demo
        tags: |
          type=ref,event=branch
          type=ref,event=pr
          type=sha,prefix={{branch}}-
          type=raw,value=latest,enable={{is_default_branch}}

    - name: Build and push Docker image
      uses: docker/build-push-action@v5
      with:
        context: ./app
        push: true
        tags: ${{ steps.meta.outputs.tags }}
        labels: ${{ steps.meta.outputs.labels }}
        cache-from: type=gha
        cache-to: type=gha,mode=max

  deploy:
    runs-on: ubuntu-latest
    needs: build
    if: github.ref == 'refs/heads/main'

    steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up kubectl
      uses: azure/setup-kubectl@v3
      with:
        version: 'v1.27.0'

    - name: Get AKS credentials
      run: |
        az aks get-credentials --resource-group ${{ secrets.RESOURCE_GROUP }} --name ${{ secrets.CLUSTER_NAME }}

    - name: Create namespace if it doesn't exist
      run: |
        kubectl apply -f k8s/namespace.yaml

    - name: Create or update ACR secret
      run: |
        kubectl create secret docker-registry ${{ env.IMAGE_PULL_SECRET_NAME }} \
          --namespace=webapp-demo \
          --docker-server=${{ secrets.ACR_LOGIN_SERVER }} \
          --docker-username=${{ secrets.ACR_USERNAME }} \
          --docker-password=${{ secrets.ACR_PASSWORD }} \
          --dry-run=client -o yaml | kubectl apply -f -

    - name: Replace placeholders in Kubernetes manifests
      run: |
        # Get the image tag from the main branch build
        IMAGE_TAG="${GITHUB_SHA:0:7}"
        if [[ "${{ github.ref }}" == "refs/heads/main" ]]; then
          IMAGE_TAG="latest"
        fi

        # Replace placeholders in deployment.yaml
        sed -i "s|__ACR_LOGIN_SERVER__|${{ secrets.ACR_LOGIN_SERVER }}|g" k8s/deployment.yaml
        sed -i "s|__IMAGE_TAG__|${IMAGE_TAG}|g" k8s/deployment.yaml
        sed -i "s|__AKS_CLUSTER_NAME__|${{ secrets.CLUSTER_NAME }}|g" k8s/deployment.yaml
        sed -i "s|__LOCATION__|eastus|g" k8s/deployment.yaml

    - name: Deploy to AKS
      run: |
        kubectl apply -f k8s/deployment.yaml

    - name: Verify deployment
      run: |
        kubectl rollout status deployment/webapp-demo -n webapp-demo --timeout=300s
        kubectl get services -n webapp-demo

    - name: Get application URL
      run: |
        echo "Application deployed successfully!"
        echo "You can access your application at:"
        kubectl get ingress webapp-demo-ingress -n webapp-demo -o jsonpath='{.spec.rules[0].host}' || echo "Ingress not ready yet"
```

scripts/deploy.sh

```json
#!/bin/bash

# Azure AKS WebApp Deployment Script
# This script helps deploy the complete infrastructure and application

set -e

# Colors for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# Function to print colored output
print_status() {
    echo -e "${GREEN}[INFO]${NC} $1"
}

print_warning() {
    echo -e "${YELLOW}[WARNING]${NC} $1"
}

print_error() {
    echo -e "${RED}[ERROR]${NC} $1"
}

print_header() {
    echo -e "${BLUE}==== $1 ====${NC}"
}

# Check if required tools are installed
check_prerequisites() {
    print_header "Checking Prerequisites"

    local missing_tools=()

    if ! command -v az &> /dev/null; then
        missing_tools+=("azure-cli")
    fi

    if ! command -v terraform &> /dev/null; then
        missing_tools+=("terraform")
    fi

    if ! command -v kubectl &> /dev/null; then
        missing_tools+=("kubectl")
    fi

    if [ ${#missing_tools[@]} -ne 0 ]; then
        print_error "The following tools are required but not installed:"
        for tool in "${missing_tools[@]}"; do
            echo "  - $tool"
        done
        exit 1
    fi

    print_status "All prerequisites are installed"
}

# Check Azure login
check_azure_login() {
    print_header "Checking Azure Authentication"

    if ! az account show &> /dev/null; then
        print_error "Not logged into Azure. Please run 'az login' first"
        exit 1
    fi

    local subscription_id=$(az account show --query id -o tsv)
    local account_name=$(az account show --query name -o tsv)

    print_status "Logged into Azure subscription: $account_name ($subscription_id)"
}

# Deploy infrastructure
deploy_infrastructure() {
    print_header "Deploying Infrastructure with Terraform"

    if [ ! -f "terraform.tfvars" ]; then
        print_warning "terraform.tfvars not found. Creating from example..."
        cp terraform.tfvars.example terraform.tfvars
        print_warning "Please edit terraform.tfvars with your desired configuration before proceeding"
        read -p "Press Enter to continue once you've updated terraform.tfvars..."
    fi

    print_status "Initializing Terraform..."
    terraform init

    print_status "Planning Terraform deployment..."
    terraform plan -out=tfplan

    echo
    print_warning "Review the plan above. This will create real Azure resources that may incur costs."
    read -p "Do you want to proceed with the deployment? (y/N): " confirm

    if [[ $confirm =~ ^[Yy]$ ]]; then
        print_status "Applying Terraform configuration..."
        terraform apply tfplan
        print_status "Infrastructure deployment completed!"
    else
        print_status "Deployment cancelled"
        exit 0
    fi
}

# Extract Terraform outputs
extract_outputs() {
    print_header "Extracting Deployment Information"

    # Extract outputs
    RESOURCE_GROUP=$(terraform output -raw resource_group_name)
    CLUSTER_NAME=$(terraform output -raw aks_cluster_name)
    ACR_LOGIN_SERVER=$(terraform output -raw acr_login_server)
    ACR_USERNAME=$(terraform output -raw acr_admin_username)
    ACR_PASSWORD=$(terraform output -raw acr_admin_password)
    CLIENT_ID=$(terraform output -raw github_actions_client_id)
    CLIENT_SECRET=$(terraform output -raw github_actions_client_secret)
    TENANT_ID=$(terraform output -raw github_actions_tenant_id)
    SUBSCRIPTION_ID=$(terraform output -raw github_actions_subscription_id)

    print_status "Deployment information extracted successfully"
}

# Configure kubectl
configure_kubectl() {
    print_header "Configuring kubectl"

    print_status "Getting AKS credentials..."
    az aks get-credentials --resource-group "$RESOURCE_GROUP" --name "$CLUSTER_NAME" --overwrite-existing

    print_status "Testing kubectl connection..."
    kubectl cluster-info

    print_status "kubectl configured successfully"
}

# Display GitHub secrets
display_github_secrets() {
    print_header "GitHub Repository Secrets"

    echo "Configure the following secrets in your GitHub repository:"
    echo
    echo "Repository Settings > Secrets and variables > Actions > New repository secret"
    echo
    echo "Secret Name: AZURE_CREDENTIALS"
    echo "Value:"
    cat << EOF
{
  "clientId": "$CLIENT_ID",
  "clientSecret": "$CLIENT_SECRET",
  "subscriptionId": "$SUBSCRIPTION_ID",
  "tenantId": "$TENANT_ID"
}
EOF
    echo
    echo "Secret Name: ACR_LOGIN_SERVER"
    echo "Value: $ACR_LOGIN_SERVER"
    echo
    echo "Secret Name: ACR_USERNAME"
    echo "Value: $ACR_USERNAME"
    echo
    echo "Secret Name: ACR_PASSWORD"
    echo "Value: $ACR_PASSWORD"
    echo
    echo "Secret Name: RESOURCE_GROUP"
    echo "Value: $RESOURCE_GROUP"
    echo
    echo "Secret Name: CLUSTER_NAME"
    echo "Value: $CLUSTER_NAME"
    echo
    print_warning "IMPORTANT: Store these secrets securely and never commit them to version control"
}

# Deploy application manually (for testing)
deploy_application() {
    print_header "Deploying Application for Testing"

    # Create namespace
    print_status "Creating namespace..."
    kubectl apply -f k8s/namespace.yaml

    # For demo purposes, we'll build and push the image manually
    print_status "Building and pushing demo image..."

    # Login to ACR
    az acr login --name $(echo $ACR_LOGIN_SERVER | cut -d'.' -f1)

    # Build and push image
    docker build -t $ACR_LOGIN_SERVER/webapp-demo:latest ./app
    docker push $ACR_LOGIN_SERVER/webapp-demo:latest

    # Update deployment manifest with actual values
    print_status "Updating Kubernetes manifests..."
    sed "s|__ACR_LOGIN_SERVER__|$ACR_LOGIN_SERVER|g" k8s/deployment.yaml | \
    sed "s|__IMAGE_TAG__|latest|g" | \
    sed "s|__AKS_CLUSTER_NAME__|$CLUSTER_NAME|g" | \
    sed "s|__LOCATION__|eastus|g" > k8s/deployment-updated.yaml

    # Deploy application
    print_status "Deploying application..."
    kubectl apply -f k8s/deployment-updated.yaml

    # Wait for deployment
    print_status "Waiting for deployment to be ready..."
    kubectl rollout status deployment/webapp-demo -n webapp-demo --timeout=300s

    print_status "Application deployed successfully!"

    # Show access information
    echo
    print_status "Application Access Information:"
    echo "Port forward command: kubectl port-forward svc/webapp-demo-service 8080:80 -n webapp-demo"
    echo "Then access: http://localhost:8080"

    # Clean up temporary file
    rm -f k8s/deployment-updated.yaml
}

# Main execution
main() {
    echo -e "${BLUE}"
    echo "╔══════════════════════════════════════════════════════════════╗"
    echo "║                  Azure AKS WebApp Deployer                   ║"
    echo "║              Complete Infrastructure & Application           ║"
    echo "╚══════════════════════════════════════════════════════════════╝"
    echo -e "${NC}"
    echo

    check_prerequisites
    check_azure_login
    deploy_infrastructure
    extract_outputs
    configure_kubectl
    display_github_secrets

    echo
    read -p "Do you want to deploy the application for testing? (y/N): " deploy_app
    if [[ $deploy_app =~ ^[Yy]$ ]]; then
        if command -v docker &> /dev/null; then
            deploy_application
        else
            print_warning "Docker not found. Skipping application deployment."
            print_status "You can deploy via GitHub Actions once secrets are configured."
        fi
    fi

    echo
    print_header "Deployment Complete!"
    print_status "Your AKS infrastructure is ready!"
    print_status "Configure the GitHub secrets shown above to enable CI/CD"
    print_status "Push your code to the main branch to trigger automated deployment"
    echo
    print_status "Next steps:"
    echo "  1. Configure GitHub repository secrets"
    echo "  2. Push your code to trigger CI/CD pipeline"
    echo "  3. Monitor deployment in GitHub Actions"
    echo "  4. Access your application via the ingress URL or port forwarding"
}

# Run main function
main "$@"
```

.gitignore

```json
# Terraform files
.terraform/
*.tfstate
*.tfstate.*
*.tfvars
.terraform.lock.hcl
terraform.rc
.terraformrc

# Kubernetes
kubeconfig*
*.kubeconfig

# Node.js (for the sample app)
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*
package-lock.json
yarn.lock

# Environment files
.env
.env.*

# IDE files
.vscode/
.idea/
*.swp
*.swo

# OS files
.DS_Store
Thumbs.db

# Azure CLI files
.azure/

# Logs
*.log
logs/

# Docker
.dockerignore

# Sensitive files
secrets/
*.pem
*.key
```

## 🚀 Quick Start

### \### Prerequisites

1\. \*\*Azure CLI\*\* installed and authenticated

2\. \*\*Terraform\*\* (v1.5+) installed

3\. \*\*kubectl\*\* installed

4\. \*\*GitHub repository\*\* with Actions enabled

5\. \*\*Azure subscription\*\* with appropriate permissions

### \### Deploy Infrastructure

Create a git repo in the [GitHub portal](http://github.com), then open VS Code and run the following commands in the terminal:

\# Clone the repository

`git clone <your-repo-url>` like this `git clone` [`https://github.com/Onyechevinli/DEMO-AKS-MINIMAL.git`](https://github.com/Onyechevinli/DEMO-AKS-MINIMAL.git)

`cd <your-repo-name>` (this is if you are using GitBash)

---

Arrange your repo in your VS Code accordingly:

DEMO-AKS-MINIMAL

├──.github

| ├──workflows

| | ├──deploy.yml

├──app

| ├──.dockerignore

| ├──Dockerfile

| ├──package.json

| ├──server.js

├──k8s

| ├──deployment.yaml

| ├──namespace.yaml

├──scripts

| ├──deploy.sh

├──.gitignore

├──main.tf

├──outputs.tf

├──terraform.tfvars

├──variables.tf

\# **Run the following commands to deploy the infrastructure using Terraform**:

To use the automated script `./scripts/deploy.sh` to deploy infrastructure, you generally need to prepare your local environment, grant execute permissions to the script, configure authentication, and then run it from your terminal (the script can be run on the VS Code terminal or best run in Git Bash).

**Authentication**: Before you run the Bash script, your local environment needs to be authenticated with Azure (e.g., via Azure CLI using `az login`) and potentially other tools the script uses (like Terraform, kubectl).

**Dependencies**: The required tools, such as terraform, az CLI, kubectl, etc., must be installed on your local machine and accessible in your system's PATH.

**Configuration**: The script likely uses environment variables or configuration files to determine which infrastructure to deploy. Ensure these are set correctly.

\# Step-by-Step Execution

1\. Open your terminal or command prompt(preferably git-bash) or the terminal on your VS Code.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763323793161/31b55a7e-02c5-41b7-8255-540eba7192dd.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763323938654/094f3ef0-9d23-425d-90bd-0bf6d037f03a.png align="center")

2\. Navigate to the root directory of your repository (or wherever the scripts folder is located). The VS Code terminal takes you to the root directory by default, but the for git bash run `cd <the pathway>`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763325450034/a2ab43e7-42e8-4b7e-8f78-1cc02a700303.png align="center")

Run the Azure login command to authenticate the Azure account and subscription: `az login` follow the prompt to sign in or select your preferred account and subscription.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763326827698/cc8c3bb3-638e-46b6-a0f6-32ead4b3a768.png align="center")

3\. Grant Execute Permissions (if needed): If the script does not already have execute permissions, you'll need to grant them using the `chmod` command or you run it on GitBash directly: `chmod +x ./scripts/deploy.sh`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763327607115/8b7a549f-9a8c-4c69-9cc4-35464c9ae4ec.png align="center")

4\. Run the script:

Execute the script directly from the command line:

`./scripts/deploy.sh`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763328159743/8ff69eec-9173-488a-88f5-e689c110d473.png align="center")

If the script accepts arguments (e.g., for different environments like dev, staging, prod), you would include them:

`./scripts/deploy.sh [environment-name]`

5\. Monitor the Output: The script will output its progress to the console. It will likely:

Initialize Terraform ( `terraform init`).

Generate an execution plan ( `terraform plan`).

Prompt for confirmation or automatically apply the changes ( `terraform apply`) to provision the infrastructure.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763328715181/e130eb4c-c685-453b-88d7-582c64e7b7c0.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763329860345/e64f9747-04ef-4250-af50-7ff5c2cd81c7.png align="center")

6\. Follow Prompts:

If the script is interactive, follow any on-screen instructions, such as typing yes to confirm the Terraform apply operation.

### \# Troubleshooting

"Permission denied" error: Run chmod +x ./scripts/deploy.sh (Step 3).

Missing commands (e.g., terraform: command not found): Ensure all required dependencies are installed and their paths are correctly added to your system's PATH environment variable.

Authentication failures: Re-authenticate with your cloud provider using the Azure CLI or check that your credentials/environment variables are correctly configured. Also make sure that you have docker desktop and it is running. You can also run the error code on an AI search engine like ChatGPT or google AI.

This local execution runs the script from your machine. For automated, repeatable deployments within a team setting, you should integrate this process into a GitHub Actions workflow, as described in the previous answer, so it runs automatically when code is pushed to your repository.

---

## \### 2. Configure GitHub Secrets

After running the script, the next step is to configure these GitHub repository secrets:

We do that by going to the GitHub portal on the web and add these secrets to your GitHub repository:

To navigate to the it, when log-in to the repo, you go to settings, to secrets and variables, New repository secret

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763356556462/0dab7064-85a1-48ab-bb04-f67b0bf3d98e.png align="center")

| Secret Name | Description | Source |

|------------|-------------|--------|

| \`AZURE\_CREDENTIALS\` | Service Principal credentials | Combine client\_id, client\_secret, tenant\_id, subscription\_id |

| \`ACR\_LOGIN\_SERVER\` | Container registry URL | \`acr\_login\_server\` output |

| \`ACR\_USERNAME\` | Registry admin username | \`acr\_admin\_username\` output |

| \`ACR\_PASSWORD\` | Registry admin password | \`acr\_admin\_password\` output | | \`RESOURCE\_GROUP\` | Resource group name | \`resource\_group\_name\` output |

| \`CLUSTER\_NAME\` | AKS cluster name | \`aks\_cluster\_name\` output |

Use the below format to add the AZURE\_CREDENTIALS format in `json`:

{

"clientId": "&lt;client\_id\_from\_output&gt;", "clientSecret": "&lt;client\_secret\_from\_output&gt;", "subscriptionId": "&lt;subscription\_id\_from\_output&gt;", "tenantId": "&lt;tenant\_id\_from\_output&gt;"

}

```markdown
You can can run "terraform output -json" to get the required values from Terraform outputs
```

## \### 3. Deploy Application

Push your code to the \`main\` branch to trigger the CI/CD pipeline:

`git add .`

`git commit -m "Initial deployment"`

`git push origin main` or you can just use `git push`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763357804485/c0dc5c21-de40-4cbb-9f48-3364123362a0.png align="center")

After pushing your code to GitHub, it would trigger the GitHub Action to run the workflow to trigger the pipeline:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763358368593/6a39489e-33a6-4077-88e8-713bfb9ed9bd.png align="center")

## \### 4. Access Your Application

**Get the application URL:**

Run: `kubectl get svc -n webapp-demo`

You should eventually get: EXTERNAL-IP: 20.55.x.x

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763358982452/2c24b6da-1a74-4ac1-bddd-01dd54d66fc9.png align="center")

Then access your app at: http://&lt;EXTERNAL-IP&gt; on a web browser

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763359190098/4e314ea3-7929-4601-b44e-08339aa8b45d.png align="center")

When prompted in the above image, click on Continue to site.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763359512453/1e544120-b680-4490-9947-4ed6135aaa54.png align="center")

## \### 🚨 Troubleshooting/Common Issues

**1\. GitHub Actions Authentication Failed**

Verify service principal credentials `az ad sp show --id <client_id>`

Check role assignments `az role assignment list --assignee <client_id>`

**2\. Container Image Pull Errors**

Check ACR permissions `az acr check-health --name <acr_name>`

Verify AKS can pull from ACR `kubectl describe pod <pod_name> -n webapp-demo`

**3\. Application Not Accessible**

Check service and ingress status `kubectl get svc,ingress -n webapp-demo`

Check pod logs `kubectl logs -f deployment/webapp-demo -n webapp-demo`

## \### Debugging Commands

Check cluster status `kubectl cluster-info`

Get all resources in namespace `kubectl get all -n webapp-demo`

Describe failing resources `kubectl describe deployment webapp-demo -n webapp-demo`

View recent events `kubectl get events -n webapp-demo --sort-by='.lastTimestamp'`

## \### 🧹 Cleanup, to remove all resources:

Delete Kubernetes resources `kubectl delete namespace webapp-demo`

Destroy Terraform infrastructure `terraform destroy`

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763360911673/d7d49c20-f7f4-468a-8e91-9a6424fde845.png align="center")