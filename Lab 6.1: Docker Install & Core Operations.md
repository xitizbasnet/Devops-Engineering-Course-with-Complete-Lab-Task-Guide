# Lab 6.1: Docker Install & Core Operations

## Overview

This lab introduces Docker installation, container fundamentals, and essential Docker commands. You will install Docker on an Ubuntu EC2 instance, manage Docker images and containers, and learn commands used in daily container operations.

## Learning Objectives

By the end of this lab, you will be able to:

* Install Docker on Ubuntu EC2.
* Configure Docker for non-root user access.
* Run and manage Docker containers.
* Download and manage Docker images.
* View container logs and resource usage.
* Inspect running containers.

---

# Task 1: Install Docker on EC2 Ubuntu

## Objective

Install Docker Engine and Docker Compose plugin on an Ubuntu EC2 instance.

---

## Step 1: Update System Packages

```bash id="n5z7qx"
sudo apt update
```

---

## Step 2: Install Required Dependencies

```bash id="d4f8pw"
sudo apt install -y ca-certificates curl gnupg
```

---

## Step 3: Configure Docker Repository Key

Create the keyrings directory:

```bash id="r6t2kp"
sudo install -m 0755 -d /etc/apt/keyrings
```

Download and add the Docker GPG key:

```bash id="s9c4mv"
curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
| sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

---

## Step 4: Add Docker Repository

```bash id="w8h3zn"
echo "deb [arch=$(dpkg --print-architecture)
signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo
$VERSION_CODENAME) stable" \
| sudo tee /etc/apt/sources.list.d/docker.list
```

---

## Step 5: Install Docker Engine

```bash id="p3v7xm"
sudo apt update

sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

Verify Docker installation:

```bash id="k9d2lf"
docker --version
```

---

## Step 6: Add Ubuntu User to Docker Group

Add the `ubuntu` user to the Docker group:

```bash id="h4q8cs"
sudo usermod -aG docker ubuntu && newgrp docker
```

Test Docker installation:

```bash id="x6m2rv"
docker run hello-world
```

> **ℹ️ Note**
>
> Adding the user to the Docker group allows Docker commands to run without using `sudo`.

---

# Task 2: Docker Lifecycle Commands

## Objective

Learn the fundamental Docker commands used to manage images, containers, and container resources.

---

# Docker Images

## List Local Images

```bash id="m2x7qw"
docker images
```

---

## Download Images from Docker Hub

```bash id="z8k4nv"
docker pull nginx:latest

docker pull ubuntu:22.04
```

---

## Remove Images

Remove a specific image:

```bash id="v3p9ls"
docker rmi nginx
```

Remove unused dangling images:

```bash id="b7c5qk"
docker image prune
```

---

# Docker Containers

## Run Containers

Run an Nginx container in detached mode:

```bash id="q5n8hx"
docker run -d -p 80:80 --name web nginx
```

Run Ubuntu container with an interactive terminal:

```bash id="f2m6cd"
docker run -it ubuntu:22.04 bash
```

---

## List Containers

Show running containers:

```bash id="w6t9ps"
docker ps
```

Show all containers:

```bash id="c8r4yj"
docker ps -a
```

---

## Manage Container Lifecycle

Stop and start a container:

```bash id="k3v8qd"
docker stop web && docker start web
```

Remove a stopped container:

```bash id="s7m2fx"
docker rm web
```

---

## View Container Logs

Display container logs:

```bash id="n8q4pv"
docker logs web
```

Follow live container logs:

```bash id="m5z9lw"
docker logs -f web
```

---

## Access Container Shell

Enter a running container:

```bash id="q9v3mk"
docker exec -it web bash
```

---

# Inspect Docker Resources

## View Container Details

```bash id="r2k8xf"
docker inspect web | grep IPAddress
```

---

## Monitor Resource Usage

```bash id="t6m4qp"
docker stats
```

---

# Best Practices

> **✅ Best Practice**
>
> Avoid running containers as the root user whenever possible. Use appropriate user permissions and security configurations for production environments.

> **✅ Best Practice**
>
> Regularly remove unused images, containers, and volumes to maintain a clean Docker environment.

> **✅ Best Practice**
>
> Use specific image versions instead of relying on `latest` tags in production deployments to ensure predictable builds.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Installed Docker Engine on Ubuntu EC2.
* ✅ Configured Docker for non-root user access.
* ✅ Downloaded and managed Docker images.
* ✅ Created and managed Docker containers.
* ✅ Viewed container logs and runtime information.
* ✅ Inspected container details and resource usage.
