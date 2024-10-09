# Infrastructure Setup for EKS with Jenkins Integration

This document outlines the steps to set up an Amazon EKS (Elastic Kubernetes Service) cluster, configure necessary CLI tools, create a service account for Jenkins, and establish role bindings.

## Table of Contents

1. [IAM Policies](#iam-policies)
2. [CLI Tools Installation](#cli-tools-installation)
   - [AWS CLI](#aws-cli)
   - [Kubectl CLI](#kubectl-cli)
   - [EKSCTL CLI](#eksctl-cli)
3. [Create an EKS Cluster](#create-an-eks-cluster)
   - [EKS Master Node](#eks-master-node)
   - [EKS Worker Nodes](#eks-worker-nodes)
4. [Jenkins Integration](#jenkins-integration)
   - [Create a Service Account](#create-a-service-account)
   - [Create a Role](#create-a-role)
   - [Bind the Role](#bind-the-role)
   - [Generate Authentication Token](#generate-authentication-token)

---

## IAM Policies

- Assign specific permissions to roles as needed.
- Be cautious with granting administrative access.

## CLI Tools Installation

### AWS CLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install
aws configure

```

### Kubectl CLI

```bash

curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client

```

### EKSCTL CLI

```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version

```

### Create a EKS Cluster

1. **EKS master**

- Create the EKS cluster without any node groups:

```bash
eksctl create cluster --name=EKS \
                      --region=ap-south-1 \
                      --zones=ap-south-1a,ap-south-1b \
                      --without-nodegroup

```

2. **EKS Worker Nodes**

- Add a node group to the EKS cluster:

```bash
eksctl create nodegroup --cluster=EKS \
                       --region=ap-south-1 \
                       --name=node2 \
                       --node-type=t3.medium \
                       --nodes=3 \
                       --nodes-min=2 \
                       --nodes-max=4 \
                       --node-volume-size=20 \
                       --ssh-access \
                       --ssh-public-key=microservices \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access
```

### Create a service Account, Create a role and bind that role for Jenkins to integrate with EKS and Generate a token to authenticate

## 1. Create a Service Account for Jenkins

```bash
apiVersion: v1
kind: ServiceAccount
metadata:
    name: jenkins
    namespace: microservices
```

## 2. Create a Role

- Define the role with specific permissions:

```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-role
  namespace: webapps
rules:
  - apiGroups:
        - ""
        - apps
        - autoscaling
        - batch
        - extensions
        - policy
        - rbac.authorization.k8s.io
    resources:
      - pods
      - componentstatuses
      - configmaps
      - daemonsets
      - deployments
      - events
      - endpoints
      - horizontalpodautoscalers
      - ingress
      - jobs
      - limitranges
      - namespaces
      - nodes
      - pods
      - persistentvolumes
      - persistentvolumeclaims
      - resourcequotas
      - replicasets
      - replicationcontrollers
      - serviceaccounts
      - services

    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]

```

## 3. Bind the Role

- Bind the role to the service account:

```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: BindRole
metadata:
    name: Roll Binding
    namespace: microservices
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: microservices-role
subjects:
  - namespace: microservices
    kind: ServiceAccount
    name: jenkins
```

## 4. Generate Authentication Token

- Create a secret for the Jenkins service account:

```bash
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: jenkins-secret
  namespace: microservices
  annotations:
    kubernetes.io/service-account.name: jenkins
```

- To view the secret:

```bash
  kubectl describe secret jenkins-secret -n microservices
```
