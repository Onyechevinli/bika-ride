---
title: "Deploying a webapp to Azure using the deploy.sh scripting to automate the Terraform, GitHub Action, and other designated processes."
datePublished: Sun Nov 16 2025 15:41:26 GMT+0000 (Coordinated Universal Time)
cuid: cmi1vva8p000002jlbx0p1dd0
slug: deploying-a-webapp-to-azure-using-the-deploysh-scripting-to-automate-the-terraform-github-action-and-other-designated-processes
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

\## 📦 What's Included

\### Infrastructure (Terraform)

\- \*\*Azure Resource Group\*\*: Container for all resources

\- \*\*Azure Kubernetes Service (AKS)\*\*: Managed Kubernetes cluster

\- \*\*Azure Container Registry (ACR)\*\*: Private container image registry

\- \*\*Service Principal\*\*: Authentication for GitHub Actions

\- \*\*RBAC Permissions\*\*: Proper role assignments for ACR and AKS integration

\### Application

\- \*\*Node.js Web Application\*\*: Production-ready Express.js app with health checks

\- \*\*Docker Container\*\*: Optimized multi-stage build with security best practices

\- \*\*Kubernetes Manifests\*\*: Deployment, Service, and Ingress configurations

\### CI/CD Pipeline

\- \*\*GitHub Actions Workflow\*\*: Automated build, test, and deployment

\- \*\*Container Image Management\*\*: Automatic tagging and registry push

\- \*\*Blue-Green Deployments\*\*: Zero-downtime deployment strategy

\## 🚀 Quick Start

\### Prerequisites

1\. \*\*Azure CLI\*\* installed and authenticated

2\. \*\*Terraform\*\* (v1.5+) installed

3\. \*\*kubectl\*\* installed

4\. \*\*GitHub repository\*\* with Actions enabled

5\. \*\*Azure subscription\*\* with appropriate permissions

\### 1. Deploy Infrastructure

\`\`\`bash

\# create a git repo, then open vs code and run the following commands:

\# Clone the repository

`git clone <your-repo-url>`

`cd <your-repo-name>`

\-----------------------------------------------------------------------------

\# Run the following commands to deploy the infrastructure using Terraform:

To use the automated script `./scripts/deploy.sh` to deploy infrastructure, you generally need to prepare your local environment, grant execute permissions to the script, configure authentication, and then run it from your terminal. the preferred account to authenticate.

Authentication: Your local environment needs to be authenticated with Azure (e.g., via Azure CLI using `az login`) and potentially other tools the script uses (like Terraform, kubectl).

Dependencies: The required tools, such as terraform, az CLI, kubectl, etc., must be installed on your local machine and accessible in your system's PATH.

Configuration: The script likely uses environment variables or configuration files to determine which infrastructure to deploy. Ensure these are set correctly.

\# Step-by-Step Execution

1\. Open your terminal or command prompt(preferrably git-bash) or run your terminal on your VS code.

2\. Navigate to the root directory of your repository (or wherever the scripts folder is located).

3\. Grant Execute Permissions (if needed): If the script does not already have execute permissions, you'll need to grant them using the chmod command or you run it on gitbash directly:

bash

`chmod +x ./scripts/deploy.sh`

Use code with caution.

4\. Run the script:

Execute the script directly from the command line:

bash

`./scripts/deploy.sh`

Use code with caution.

If the script accepts arguments (e.g., for different environments like dev, staging, prod), you would include them:

bash

`./scripts/deploy.sh [environment-name]`

Use code with caution.

5\. Monitor the Output: The script will output its progress to the console. It will likely:

Initialize Terraform ( `terraform init`).

Generate an execution plan ( `terraform plan`).

Prompt for confirmation or automatically apply the changes ( `terraform apply`) to provision the infrastructure.

6\. Follow Prompts:

If the script is interactive, follow any on-screen instructions, such as typing yes to confirm the Terraform apply operation.

\# Troubleshooting

"Permission denied" error: Run chmod +x ./scripts/deploy.sh (Step 3).

Missing commands (e.g., terraform: command not found): Ensure all required dependencies are installed and their paths are correctly added to your system's PATH environment variable.

Authentication failures: Re-authenticate with your cloud provider using the Azure CLI or check that your credentials/environment variables are correctly configured.

This local execution runs the script from your machine. For automated, repeatable deployments within a team setting, you should integrate this process into a GitHub Actions workflow, as described in the previous answer, so it runs automatically when code is pushed to your repository.

\-----------------------------------------------------------------------------

or manually run the following commands:

\# Initialize Terraform `terraform init`

\# Review the deployment plan `terraform plan`

\# Deploy the infrastructure `terraform apply`

\`\`\`

\### 2. Configure GitHub Secrets

After Terraform deployment, configure these GitHub repository secrets:

\`\`\`bash

\# Get the required values from Terraform outputs `terraform output -json`

\`\`\`

Add these secrets to your GitHub repository:

| Secret Name | Description | Source |

|------------|-------------|--------|

| \`AZURE\_CREDENTIALS\` | Service Principal credentials | Combine client\_id, client\_secret, tenant\_id, subscription\_id |

| \`ACR\_LOGIN\_SERVER\` | Container registry URL | \`acr\_login\_server\` output |

| \`ACR\_USERNAME\` | Registry admin username | \`acr\_admin\_username\` output |

| \`ACR\_PASSWORD\` | Registry admin password | \`acr\_admin\_password\` output | | \`RESOURCE\_GROUP\` | Resource group name | \`resource\_group\_name\` output |

| \`CLUSTER\_NAME\` | AKS cluster name | \`aks\_cluster\_name\` output |

\*\*AZURE\_CREDENTIALS format:\*\* \`\`\`json

{

"clientId": "&lt;client\_id\_from\_output&gt;", "clientSecret": "&lt;client\_secret\_from\_output&gt;", "subscriptionId": "&lt;subscription\_id\_from\_output&gt;", "tenantId": "&lt;tenant\_id\_from\_output&gt;"

}

\`\`\`

\### 3. Deploy Application

Push your code to the \`main\` branch to trigger the CI/CD pipeline:

\`\`\`bash

git add .

git commit -m "Initial deployment"

git push origin main

\`\`\`

\### 4. Access Your Application

\`\`\`bash

\# Get the application URL

kubectl get ingress webapp-demo-ingress -n webapp-demo

\# Or use port forwarding for immediate access

kubectl port-forward svc/webapp-demo-service 8080:80 -n webapp-demo

\# if you can not access via ingress, you need to use:

A LoadBalancer service, right now, you have no way to access the app externally.

✅ Option 1 — Convert your Service to LoadBalancer (fastest way)

Create/update your service YAML to:

---

Save and push the changes to github to trigger the pipeline, or apply it directly: `kubectl apply -f k8s/deployment.yaml`

Then wait for few minutes to get the external IP, after that run: `kubectl get svc -n webapp-demo`

You should eventually get: EXTERNAL-IP: 20.55.x.x

Then access your app at: http://&lt;EXTERNAL-IP&gt;

\## 🚨 Troubleshooting/Common Issues

\*\*1. GitHub Actions Authentication Failed\*\*

\`\`\`bash

\# Verify service principal credentials `az ad sp show --id <client_id>`

\# Check role assignments `az role assignment list --assignee <client_id>`

\`\`\`

\*\*2. Container Image Pull Errors\*\* \`\`\`bash # Check ACR permissions az acr check-health --name &lt;acr\_name&gt; # Verify AKS can pull from ACR kubectl describe pod &lt;pod\_name&gt; -n webapp-demo \`\`\` \*\*3. Application Not Accessible\*\* \`\`\`bash # Check service and ingress status kubectl get svc,ingress -n webapp-demo # Check pod logs kubectl logs -f deployment/webapp-demo -n webapp-demo \`\`\` ### Debugging Commands \`\`\`bash # Check cluster status kubectl cluster-info # Get all resources in namespace kubectl get all -n webapp-demo # Describe failing resources kubectl describe deployment webapp-demo -n webapp-demo # View recent events kubectl get events -n webapp-demo --sort-by='.lastTimestamp' \`\`\` ## 🧹 Cleanup To remove all resources: \`\`\`bash # Delete Kubernetes resources kubectl delete namespace webapp-demo # Destroy Terraform infrastructure terraform destroy \`\`\`