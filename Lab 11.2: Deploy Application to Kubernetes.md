# Lab 11.2: Deploy Application to Kubernetes

## Overview

This lab demonstrates how to deploy a containerized application on Kubernetes. You will learn essential `kubectl` commands, create Kubernetes namespaces, deploy applications using Deployments, expose applications using Services, and manage application configuration using ConfigMaps and Secrets.

## Learning Objectives

By the end of this lab, you will be able to:

* Use essential Kubernetes `kubectl` commands.
* Create and manage Kubernetes Deployments.
* Expose applications using Kubernetes Services.
* Configure applications using ConfigMaps.
* Store sensitive application data using Secrets.
* Verify application deployment and health status.

---

# Task 1: Core kubectl Commands

## Objective

Learn commonly used Kubernetes commands for managing clusters, workloads, and resources.

---

## Cluster and Resource Information

### List Pods

```bash
kubectl get pods
```

Lists running Pods in the current namespace.

---

### List Pods Across All Namespaces

```bash
kubectl get pods -A
```

Displays Pods from all Kubernetes namespaces.

---

### View Deployments

```bash
kubectl get deployments
```

Shows all application Deployments.

---

### View Services

```bash
kubectl get services
```

Lists Kubernetes Services.

---

### View Cluster Nodes

```bash
kubectl get nodes
```

Displays available Kubernetes nodes.

---

### View All Resources

```bash
kubectl get all
```

Shows common Kubernetes resources.

---

### View Namespaces

```bash
kubectl get namespaces
```

Lists all namespaces in the cluster.

---

# Troubleshooting Commands

## Describe Pod Details

```bash
kubectl describe pod <name>
```

Displays detailed information about a Pod, including:

* Events
* Container status
* Networking information
* Resource configuration

---

## View Pod Logs

```bash
kubectl logs <pod-name>
```

Displays application logs.

---

## Follow Live Logs

```bash
kubectl logs -f <pod-name>
```

Streams live container logs.

---

## Access Container Shell

```bash
kubectl exec -it <pod> -- bash
```

Opens an interactive shell inside a running container.

---

# Resource Management Commands

## Create or Update Resources

```bash
kubectl apply -f file.yaml
```

Creates or updates Kubernetes resources from YAML definitions.

---

## Delete Resources

```bash
kubectl delete -f file.yaml
```

Removes Kubernetes resources defined in a YAML file.

---

## Delete Specific Pod

```bash
kubectl delete pod <name>
```

Deletes an individual Pod.

---

## Scale Deployment

```bash
kubectl scale deployment <name> --replicas=5
```

Changes the number of application replicas.

---

## Check Deployment Status

```bash
kubectl rollout status deployment/<name>
```

Displays deployment rollout progress.

---

## Rollback Deployment

```bash
kubectl rollout undo deployment/<name>
```

Reverts a Deployment to the previous version.

---

# Task 2: Create Namespace, Deployment & Service

## Objective

Deploy a Flask application into Kubernetes using:

* Namespace
* Deployment
* Service

---

# Step 1: Create Namespace

Create a dedicated namespace:

```bash
kubectl create namespace devops-app
```

Verify:

```bash
kubectl get namespaces
```

---

# Step 2: Create Kubernetes Deployment

Create deployment configuration:

```bash
cat > flask-deployment.yaml << 'EOF'
```

Deployment file:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: flask-app
  namespace: devops-app
  labels:
    app: flask

spec:
  replicas: 3

  selector:
    matchLabels:
      app: flask

  template:
    metadata:
      labels:
        app: flask

    spec:
      containers:
      - name: flask
        image: <dockerhub-user>/flask-app:latest

        ports:
        - containerPort: 5000

        resources:

          requests:
            memory: "64Mi"
            cpu: "100m"

          limits:
            memory: "128Mi"
            cpu: "200m"

        readinessProbe:
          httpGet:
            path: /health
            port: 5000

          initialDelaySeconds: 10
          periodSeconds: 5


        livenessProbe:
          httpGet:
            path: /health
            port: 5000

          initialDelaySeconds: 15
          periodSeconds: 10
EOF
```

---

# Step 3: Create Kubernetes Service

Create service configuration:

```bash
cat > flask-service.yaml << 'EOF'
```

Service definition:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: flask-service
  namespace: devops-app

spec:

  selector:
    app: flask

  ports:

  - port: 80
    targetPort: 5000

  type: NodePort
EOF
```

---

# Step 4: Deploy Application

Apply Deployment:

```bash
kubectl apply -f flask-deployment.yaml
```

Apply Service:

```bash
kubectl apply -f flask-service.yaml
```

---

# Step 5: Verify Deployment

## View Application Pods

```bash
kubectl get pods -n devops-app
```

Expected:

```text
NAME                         READY   STATUS
flask-app-xxxxx              1/1     Running
```

---

## View Application Service

```bash
kubectl get services -n devops-app
```

---

## Access Application

For Minikube:

```bash
minikube service flask-service -n devops-app --url
```

This returns the application URL.

---

# Task 3: ConfigMap & Secrets

## Objective

Manage application configuration and sensitive information using Kubernetes built-in resources.

---

# ConfigMap

## Purpose

ConfigMaps store non-sensitive application configuration values.

Examples:

* Environment names
* Logging levels
* Application settings

---

## Create ConfigMap

```bash
kubectl create configmap app-config \
--from-literal=APP_ENV=production \
--from-literal=LOG_LEVEL=info \
-n devops-app
```

---

## Verify ConfigMap

```bash
kubectl get configmap app-config -n devops-app -o yaml
```

---

# Secrets

## Purpose

Secrets store sensitive information such as:

* Passwords
* API keys
* Credentials

Kubernetes stores Secret values as Base64 encoded data.

---

## Create Database Secret

```bash
kubectl create secret generic db-secret \
--from-literal=DB_PASSWORD=App@2024! \
--from-literal=DB_USER=appuser \
-n devops-app
```

---

## Use Secret in Deployment

Add the following environment configuration under the container specification:

```yaml
env:

- name: DB_PASSWORD

  valueFrom:

    secretKeyRef:

      name: db-secret

      key: DB_PASSWORD
```

---

# Kubernetes Deployment Flow

```text
Docker Image
      |
      |
 Kubernetes Deployment
      |
      |
 ReplicaSet
      |
      |
    Pods
      |
      |
 Kubernetes Service
      |
      |
 Application Access
```

---

# Best Practices

> **✅ Best Practice**
>
> Always use namespaces to separate applications, environments, and teams inside Kubernetes clusters.

> **✅ Best Practice**
>
> Define resource requests and limits to prevent one application from consuming all cluster resources.

> **✅ Best Practice**
>
> Use Kubernetes Secrets for sensitive values instead of storing passwords directly inside YAML files.

> **✅ Best Practice**
>
> Always configure readiness and liveness probes for production workloads to enable Kubernetes self-healing.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Practiced essential Kubernetes `kubectl` commands.
* ✅ Created a dedicated Kubernetes namespace.
* ✅ Deployed a Flask application using Kubernetes Deployment.
* ✅ Exposed the application using a Service.
* ✅ Configured application settings using ConfigMaps.
* ✅ Stored sensitive credentials using Kubernetes Secrets.
* ✅ Verified application availability using Minikube.
