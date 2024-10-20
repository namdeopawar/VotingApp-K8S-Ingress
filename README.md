# Voting Application Deployment on AWS with Kubernetes

This project demonstrates the deployment of a microservices-based voting application on AWS using Kubernetes. It utilizes Kops for cluster provisioning, NGINX Ingress for secure routing, SSL for encryption, and AWS Route 53 for DNS management. The application is containerized using Docker and deployed with Kubernetes for scalability and high availability.

## Architecture Overview

The application architecture includes:
- **Kubernetes Cluster**: Provisioned using Kops with one control plane node and two worker nodes for high availability and scalability.
- **NGINX Ingress Controller**: Handles incoming traffic securely, with SSL termination for encrypted data transmission.
- **Dockerized Microservices**: The application is composed of several services (Voting app, Results app, Redis, PostgreSQL) packaged as Docker containers.
- **AWS Route 53**: Used for DNS management to resolve domain names to the Kubernetes Ingress endpoint.
- **Kubernetes Secrets**: Protects sensitive information such as Docker credentials and SSL certificates.

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

### 2. Set Up NGINX Ingress with SSL
1. **Apply NGINX Ingress Configuration**:
   Set up the Ingress rules using your Ingress.yml file:
   ```bash
   kubectl apply -f Ingress.yml

2. **Generate SSL Certificates**:
    Generate SSL certificates (or use pre-existing ones):
    ```bash
    vim /tmp/example.key
    vim /tmp/example.crt

3. **Create Kubernetes Secret for SSL**:
   Store the SSL certificate and key securely using Kubernetes Secrets:
   ```bash
   kubectl create secret tls nginx-tls-default --key=/tmp/example.key --cert=/tmp/example.crt

### 3. Deploy the Voting Application

1. **Apply the Application Configuration**:
   Deploy the microservices (Voting, Results, Redis, PostgreSQL) using the voting.yml file:
   ```bash
   kubectl apply -f voting.yml

2. **Verify Deployments**:
   Check the status of your deployments to ensure everything is running properly:
   ```bash
   kubectl get pods
   kubectl get services

### 4. Configure DNS with AWS Route 53

1. **Retrieve the External IP**:
   Get the external IP address of the NGINX Ingress controller:
   ```bash
   kubectl get svc -n ingress-nginx

2. **Create DNS Records in Route 53**:
   In the AWS Route 53 console, create A records pointing to the external IP of the Ingress controller for the following domains:
   ```bash
   vote.example.com
   results.example.com

### 5. Monitor and Scale the Application
1. **Monitor Application Status**:
   Keep track of your running services and pods:
   ```bash
   kubectl get pods
   kubectl get services

2. **Check Logs for Issue**:
   If a service fails, check the logs to diagnose the issue:
   ```bash
   kubectl logs <pod-name>

### Conclusion
   This deployment showcases the integration of modern DevOps practices, such as containerization, orchestration with Kubernetes, secure communication using SSL, and cloud-native services on AWS. The use of Kubernetes Secrets ensures secure handling of sensitive data, while Horizontal Pod Autoscaling ensures the application scales seamlessly with demand.

### Repository
   Check out the repository for this project here.

