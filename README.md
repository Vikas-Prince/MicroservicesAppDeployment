# Microservices Deployment on AWS Integrating with Jenkins and EKS

This project demonstrates the deployment of a microservices application on AWS, utilizing Jenkins for Continuous Integration (CI) and Continuous Deployment (CD). The application is containerized using Docker and orchestrated with Kubernetes on an Amazon EKS cluster.

## Overview

This project includes:

- Dockerfiles for each microservice
- Jenkinsfiles for each microservice and a main Jenkinsfile for deployment
- Kubernetes deployment files for the services
- Configuration for a multi-branch pipeline in Jenkins

The application is built using Docker images that are pushed to Docker Hub and deployed onto an AWS EKS cluster.

## Prerequisites

Before you begin, ensure you have the following tools installed:

- [Docker](https://www.docker.com/)
- [Jenkins](https://www.jenkins.io/)
- [AWS CLI](https://aws.amazon.com/cli/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

You will also need:

- An AWS account
- Docker Hub account
- A configured AWS EKS cluster

## Architecture

The architecture consists of multiple microservices, each encapsulated in Docker containers, managed by Kubernetes on EKS. Jenkins orchestrates the CI/CD processes, enabling automated builds and deployments.

## Setup Instructions

1. **Clone the Repository:**

   ```bash
   git clone https://github.com/Vikas-Prince/MicroservicesAppDeployment
   cd MicroservicesAppDeployment
   ```

2. **Configure Jenkins:**

   - Install the necessary plugins for Docker, Kubernetes, and Git.
   - Set up a new multi-branch pipeline job pointing to your repository make sure that you need to add all your credentials before start the pipeline.

3. **Set Up AWS Credentials:**

   - Configure your AWS CLI with appropriate IAM permissions to manage EKS and other resources.

4. **Create EKS Cluster:**

   - Follow AWS documentation to create and configure your EKS cluster.

5. **Service Account for Jenkins:**
   - Create a Kubernetes service account, role, and bind the role to grant Jenkins the required permissions to deploy applications. Generate a token to authenticate Jenkins with the EKS cluster.

## CI/CD Pipeline

The project utilizes a multi-branch pipeline in Jenkins that automatically builds, tests, and deploys each microservice. Each branch corresponds to a microservice with its own Dockerfile.

## Docker Configuration

Each microservice has a `Dockerfile` defining how to build its image. Ensure that these are correctly configured to expose the necessary ports and include all dependencies.

### Workflow:

1. Code is pushed to a branch.
2. Jenkins detects the change and triggers a build.
3. Docker images are built and pushed to Docker Hub.
4. The main Jenkinsfile in the main branch triggers deployments to the EKS cluster.

## Jenkins Configuration

The Jenkins pipeline is defined in each microservice's `Jenkinsfile` as well as in the main branch for deployment. This includes stages for:

- Building the Docker image
- Running tests
- Pushing the image to Docker Hub
- Deploying to EKS

## EKS Configuration

1. Create an EKS cluster and configure your `kubectl` context.
2. Create a service account for Jenkins with permissions to deploy applications in your namespace.
3. Create a token for authentication

### Example command to create a service account:

```bash
kubectl create serviceaccount jenkins-sa
kubectl apply -f jenkins-sa-role.yaml
```

## Deployment

To deploy the application on the EKS cluster, navigate to the main branch in Jenkins and run the deployment pipeline. This uses the Jenkinsfile located in the main branch to handle deployment.

### Deployment Steps:

- Fetch the Docker image from Docker Hub.
- Use `kubectl` to apply the Kubernetes deployment files.

## Troubleshooting

1. Check the Jenkins logs for build failures.
2. Ensure that your AWS credentials are correctly configured.
3. Verify that the EKS cluster is running and accessible.
4. Use kubectl commands to check the status of deployed pods and services.
