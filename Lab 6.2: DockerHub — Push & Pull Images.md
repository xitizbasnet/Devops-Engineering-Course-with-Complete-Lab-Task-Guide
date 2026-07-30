# Lab 6.2: DockerHub — Push & Pull Images

## Overview

This lab introduces Docker image creation and distribution using DockerHub. You will create a custom Docker image from a modified container, tag the image, push it to DockerHub, and pull the image from another machine.

## Learning Objectives

By the end of this lab, you will be able to:

* Create a custom Docker image from an existing container.
* Commit container changes into a reusable Docker image.
* Authenticate with DockerHub.
* Tag Docker images for a remote registry.
* Push and pull Docker images.

---

# Task 1: Commit Container Changes to New Image

## Objective

Create a custom Docker image by modifying an Ubuntu container and saving the changes as a new image.

---

## Step 1: Run Ubuntu Container and Install Nginx

Start an Ubuntu container:

```bash id="x4p9kd"
docker run -it ubuntu:22.04 bash
```

Inside the container, update packages and install Nginx:

```bash id="m7v2qc"
apt update && apt install nginx -y
```

Exit the container:

```bash id="q8n3lf"
exit
```

---

## Step 2: Identify the Container ID

List all containers:

```bash id="p6w4zs"
docker ps -a
```

---

## Step 3: Commit Container as a New Image

Create a new Docker image from the container:

```bash id="c9k5xm"
docker commit <CONTAINER_ID> custom-nginx:v1
```

Verify the newly created image:

```bash id="r5t8qn"
docker images
```

Expected image:

```text
custom-nginx:v1
```

> **ℹ️ Note**
>
> The `docker commit` command creates an image from a container's current state. For production environments, creating images using a `Dockerfile` is the recommended approach.

---

# Task 2: Push Image to DockerHub

## Objective

Upload the custom Docker image to DockerHub so it can be accessed from other systems.

---

## Step 1: Login to DockerHub

```bash id="h2m8vq"
docker login
```

Enter your DockerHub credentials:

```text
Username: <dockerhub-user>
Password: <password-or-PAT>
```

> **Important**
>
> Use a Personal Access Token (PAT) instead of your account password when required by DockerHub security policies.

---

## Step 2: Tag Image for DockerHub

Tag the local image with your DockerHub repository name:

```bash id="z7n4kc"
docker tag custom-nginx:v1 <dockerhub-user>/custom-nginx:v1
```

Create a `latest` tag:

```bash id="w5x9pj"
docker tag custom-nginx:v1 <dockerhub-user>/custom-nginx:latest
```

---

## Step 3: Push Image to DockerHub

Push the versioned image:

```bash id="d3m8yr"
docker push <dockerhub-user>/custom-nginx:v1
```

Push the latest version:

```bash id="f6q2ws"
docker push <dockerhub-user>/custom-nginx:latest
```

---

## Step 4: Verify Image on DockerHub

Verify the uploaded image:

```text
https://hub.docker.com
```

---

## Step 5: Pull Image on Any Machine

Download the image from DockerHub:

```bash id="k8r5mv"
docker pull <dockerhub-user>/custom-nginx:v1
```

---

# Best Practices

> **✅ Best Practice**
>
> Use `Dockerfile` instead of `docker commit` for production images. Dockerfiles provide version control, repeatability, and automation.

> **✅ Best Practice**
>
> Tag Docker images with meaningful version numbers instead of relying only on the `latest` tag.

> **✅ Best Practice**
>
> Never store DockerHub passwords in scripts or configuration files. Use secure authentication methods such as Personal Access Tokens (PATs).

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Created a custom Docker image from a container.
* ✅ Installed Nginx inside an Ubuntu container.
* ✅ Committed container changes into a Docker image.
* ✅ Logged into DockerHub.
* ✅ Tagged images for remote storage.
* ✅ Pushed and pulled Docker images from DockerHub.
