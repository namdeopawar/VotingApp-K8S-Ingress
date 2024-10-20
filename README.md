# Voting Application Deployment on AWS with Kubernetes

This project demonstrates the deployment of a microservices-based voting application on AWS using Kubernetes. It utilizes Kops for cluster provisioning, NGINX Ingress for secure routing, SSL for encryption, and AWS Route 53 for DNS management. The application is containerized using Docker and deployed with Kubernetes for scalability and high availability.

## Architecture Overview

The application architecture includes:
- **Kubernetes Cluster**: Provisioned using Kops with one control plane node and two worker nodes for high availability and scalability.
- **NGINX Ingress Controller**: Handles incoming traffic securely, with SSL termination for encrypted data transmission.
- **Dockerized Microservices**: The application is composed of several services (Voting app, Results app, Redis, PostgreSQL) packaged as Docker containers.
- **AWS Route 53**: Used for DNS management to resolve domain names to the Kubernetes Ingress endpoint.
- **Kubernetes Secrets**: Protects sensitive information such as Docker credentials and SSL certificates.
- **Horizontal Pod Autoscaling (HPA)**: Automatically scales the application based on traffic and resource usage.

## Prerequisites

Before deploying the application, ensure the following:
- AWS account with EC2 and Route 53 configured.
- Domain managed via AWS Route 53.
- Kops installed for managing Kubernetes clusters on AWS.
- Docker repository for hosting private Docker images.

## Step-by-Step Deployment Instructions

### 1. Kubernetes Cluster Setup using Kops

1. **Configure Environment Variables**:
   Set up environment variables to define your cluster settings:

   ```bash
   export NAME=example.com
   export KOPS_STATE_STORE=s3://example.com
   export AWS_REGION=us-east-1
   export CLUSTER_NAME=example.com
2. **Create the Cluster Configuration**:
   ```bash
   kops create cluster --name=$CLUSTER_NAME --state=$KOPS_STATE_STORE --zones=us-east-1a,us-east-1b --node-count=2 --control-plane-count=1 --node-size=t3.medium --control-plane-size=t3.medium --dns-zone=$CLUSTER_NAME --dry-run --output yaml > cluster.yml
3. **Apply the Cluster Configuration**:
   Apply the generated configuration to create your Kubernetes cluster:
   ```bash
   kops create -f cluster.yml
   kops update cluster --name=$CLUSTER_NAME --yes --admin
4. **Validate the Cluster Configuration**:
   Confirm that your Kubernetes cluster is up and running:
   ```bash
   kops validate cluster --wait 10m


