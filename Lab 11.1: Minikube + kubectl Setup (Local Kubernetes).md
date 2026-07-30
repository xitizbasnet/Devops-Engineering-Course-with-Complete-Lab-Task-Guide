# Lab 11.1: Minikube + kubectl Setup (Local Kubernetes)

## Overview

This lab introduces Kubernetes fundamentals by setting up a local Kubernetes cluster using **Minikube** and managing it using **kubectl**. You will install Kubernetes command-line tools, create a local cluster, verify cluster health, and understand the differences between Docker Swarm and Kubernetes.

## Learning Objectives

By the end of this lab, you will be able to:

* Install `kubectl` on an Ubuntu EC2 instance.
* Install and configure Minikube.
* Start a local Kubernetes cluster using Docker as the driver.
* Verify Kubernetes cluster status.
* Use basic `kubectl` commands.
* Understand key differences between Docker Swarm and Kubernetes.

---

# Task 1: Install kubectl & Minikube on EC2

## Objective

Install Kubernetes client tools and create a local Kubernetes environment using Minikube.

---

# Step 1: Install kubectl

## Download kubectl Binary

Run:

```bash id="m8q3vx"
curl -LO https://dl.k8s.io/release/$(curl -Ls https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
```

---

## Install kubectl

Move the binary to the system path:

```bash id="x7p2mz"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

---

## Verify kubectl Installation

```bash id="q5w9kc"
kubectl version --client
```

Expected output:

```text id="f8m3vq"
Client Version: v1.x.x
```

---

# Step 2: Install Minikube

## Download Minikube Binary

```bash id="r4n8px"
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

---

## Install Minikube

```bash id="k6m3vz"
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

---

# Step 3: Start Minikube Cluster

## Objective

Create a single-node Kubernetes cluster using Docker as the runtime driver.

Start the cluster:

```bash id="w9q4mx"
minikube start --driver=docker --cpus=2 --memory=2048
```

---

# Step 4: Verify Cluster Status

Check Minikube status:

```bash id="n7p3kc"
minikube status
```

Expected result:

```text id="a6x8mv"
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

---

## Check Kubernetes Cluster Information

```bash id="z5m8qw"
kubectl cluster-info
```

---

## View Cluster Nodes

```bash id="p4x7nv"
kubectl get nodes
```

Expected output:

```text id="s8m2kc"
NAME       STATUS   ROLES           AGE
minikube   Ready    control-plane   xx
```

---

# Kubernetes Architecture Overview

```text id="c5m8qx"
             kubectl Client
                  |
                  |
          Kubernetes API Server
                  |
        -----------------------
        |                     |
    Scheduler             Controller
        |
        |
    Minikube Node
        |
     Containers
```

---

# Task 2: Docker Swarm vs Kubernetes — Key Differences

## Comparison Overview

| Feature        | Docker Swarm  | Kubernetes                                |
| -------------- | ------------- | ----------------------------------------- |
| Complexity     | Simple        | Complex but powerful                      |
| Scaling        | Manual        | Auto-scaling built-in                     |
| Load Balancing | Built-in      | Multiple options (Ingress, Load Balancer) |
| Self-healing   | Basic         | Advanced                                  |
| Ecosystem      | Docker-only   | Cloud-native (CNCF)                       |
| Production Use | Small teams   | Enterprise standard                       |
| AWS Managed    | Not available | EKS (Elastic Kubernetes Service)          |

---

# Key Takeaways

## Docker Swarm

* Easier to learn and configure.
* Suitable for smaller environments.
* Tightly integrated with Docker.

## Kubernetes

* Designed for large-scale container orchestration.
* Provides advanced scheduling, scaling, and self-healing.
* Supported by major cloud providers.
* Common production standard for cloud-native applications.

---

# Best Practices

> **✅ Best Practice**
>
> Keep Kubernetes cluster resources aligned with workload requirements. Allocate sufficient CPU and memory when running Minikube environments.

> **✅ Best Practice**
>
> Use `kubectl get nodes` and `kubectl cluster-info` regularly to verify cluster health before deploying applications.

> **✅ Best Practice**
>
> Learn Kubernetes concepts such as Pods, Deployments, Services, and Ingress before managing production clusters.

---

# Alternative Tool

> **💡 Tip**
>
> For AWS production Kubernetes environments, use Amazon Elastic Kubernetes Service (EKS), which provides a managed Kubernetes control plane.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Installed kubectl on an Ubuntu EC2 instance.
* ✅ Installed and configured Minikube.
* ✅ Created a local Kubernetes cluster.
* ✅ Verified Kubernetes cluster connectivity.
* ✅ Compared Docker Swarm and Kubernetes architectures.
* ✅ Prepared the environment for Kubernetes application deployment.
