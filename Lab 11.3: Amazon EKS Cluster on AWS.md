# Lab 11.3: Amazon EKS Cluster on AWS

## Overview

This lab demonstrates how to create and manage a production-style Kubernetes cluster using **Amazon Elastic Kubernetes Service (EKS)**. You will use `eksctl` to provision an AWS-managed Kubernetes cluster, configure `kubectl` access, deploy an application, expose it externally, and clean up AWS resources after completion.

## Learning Objectives

By the end of this lab, you will be able to:

* Install and configure `eksctl`.
* Create a managed Kubernetes cluster on AWS EKS.
* Configure `kubectl` access to an EKS cluster.
* Deploy applications to AWS Kubernetes infrastructure.
* Expose applications using Kubernetes Services.
* Clean up EKS resources to avoid unnecessary AWS charges.
* Understand Kubernetes production best practices.

---

# Task 1: Install eksctl

## Objective

Install `eksctl`, the official command-line utility for creating and managing Amazon EKS clusters.

---

## Download and Install eksctl

Run:

```bash
curl -sL 'https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_Linux_amd64.tar.gz' | tar xz
```

Move the binary:

```bash
sudo mv eksctl /usr/local/bin/
```

---

## Verify Installation

```bash
eksctl version
```

Expected output:

```text
eksctl version: x.x.x
```

---

# Task 2: Create AWS EKS Cluster

## Objective

Create a managed Kubernetes cluster with AWS-managed control plane and EC2 worker nodes.

---

## Create Cluster

Run:

```bash
eksctl create cluster \
--name devops-eks \
--region ap-south-1 \
--nodegroup-name standard-nodes \
--node-type t3.medium \
--nodes 2 \
--nodes-min 1 \
--nodes-max 3 \
--managed
```

---

## Cluster Configuration

| Configuration | Value              |
| ------------- | ------------------ |
| Cluster Name  | `devops-eks`       |
| AWS Region    | `ap-south-1`       |
| Node Group    | `standard-nodes`   |
| Instance Type | `t3.medium`        |
| Initial Nodes | 2                  |
| Minimum Nodes | 1                  |
| Maximum Nodes | 3                  |
| Node Type     | Managed Node Group |

---

## Cluster Creation Time

> ⏱️ Cluster creation typically takes **15–20 minutes**.

The process creates:

* EKS Kubernetes control plane.
* Worker node group.
* Networking components.
* IAM roles and permissions.
* Kubernetes configuration.

---

# Task 3: Configure kubectl Access

## Objective

Connect `kubectl` with the newly created EKS cluster.

---

## Update kubeconfig

Run:

```bash
aws eks update-kubeconfig \
--name devops-eks \
--region ap-south-1
```

---

## Verify Kubernetes Nodes

```bash
kubectl get nodes
```

Expected output:

```text
NAME                                            STATUS   ROLE
ip-xxx.ap-south-1.compute.internal              Ready    <none>
ip-xxx.ap-south-1.compute.internal              Ready    <none>
```

---

# Task 4: Deploy Application to EKS

## Deploy Kubernetes Resources

Apply the application Deployment:

```bash
kubectl apply -f flask-deployment.yaml
```

Apply the Service:

```bash
kubectl apply -f flask-service.yaml
```

---

## Verify Application Deployment

Check Services:

```bash
kubectl get services
```

Example output:

```text
NAME             TYPE           EXTERNAL-IP
flask-service    LoadBalancer   <EXTERNAL-IP>
```

---

## Access Application

Use the Load Balancer external address:

```bash
curl http://<EXTERNAL_IP>
```

Expected result:

```text
Application response from Kubernetes cluster
```

---

# Task 5: Cleanup AWS Resources

## Objective

Delete the EKS cluster after completing the lab to prevent unexpected AWS charges.

---

## Delete Cluster

Run:

```bash
eksctl delete cluster \
--name devops-eks \
--region ap-south-1
```

---

# EKS Architecture Overview

```text
                 AWS EKS Cluster

              Kubernetes Control Plane
                       |
                       |
              Managed Kubernetes API
                       |
        --------------------------------
        |                              |
   Worker Node 1                 Worker Node 2
        |                              |
      Pods                           Pods
        |                              |
        --------------------------------
                       |
              Kubernetes Services
                       |
              AWS Load Balancer
                       |
                   Users
```

---

# Best Practices

> **✅ Best Practice**
>
> Always define Kubernetes resource requests and limits.
>
> Without resource limits, a single Pod can consume all available node resources and affect other workloads.

---

> **✅ Best Practice**
>
> Add `readinessProbe` and `livenessProbe` to every production Deployment.
>
> Kubernetes uses these probes to determine:
>
> * When a Pod is ready to receive traffic.
> * When a Pod needs to be restarted due to failure.

---

> **⚠️ Important Note**
>
> EKS clusters generate AWS charges.
>
> Example:
>
> * `t3.medium` worker nodes cost approximately **$0.05/hour**.
> * Always delete unused clusters after completing labs.

---

# Alternative Tools

> **💡 Tip**
>
> Kubernetes alternatives include:
>
> | Tool            | Description                                                 |
> | --------------- | ----------------------------------------------------------- |
> | Docker Swarm    | Simpler container orchestration solution                    |
> | Amazon ECS      | AWS-native container service without Kubernetes overhead    |
> | HashiCorp Nomad | Lightweight multi-workload orchestrator                     |
> | k3s             | Lightweight Kubernetes distribution for local environments  |
> | kind            | Kubernetes clusters using Docker containers for development |

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Installed `eksctl`.
* ✅ Created an AWS-managed EKS Kubernetes cluster.
* ✅ Connected `kubectl` to EKS.
* ✅ Deployed an application on AWS Kubernetes infrastructure.
* ✅ Exposed the application using a LoadBalancer Service.
* ✅ Cleaned up AWS resources after completion.
* ✅ Reviewed production Kubernetes operational practices.
