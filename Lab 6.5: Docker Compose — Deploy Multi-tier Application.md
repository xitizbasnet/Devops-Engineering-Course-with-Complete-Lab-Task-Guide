# Lab 6.5: Docker Compose — Deploy Multi-tier Application

## Overview

This lab introduces Docker Compose for deploying multi-tier applications. You will define multiple services using a `docker-compose.yml` file, configure service dependencies, create application networks, manage persistent database storage, and control the complete application lifecycle.

## Learning Objectives

By the end of this lab, you will be able to:

* Create a multi-container application using Docker Compose.
* Configure services, networks, volumes, and health checks.
* Deploy frontend, application, and database tiers.
* Manage Docker Compose application lifecycle operations.
* Apply Docker image and container security best practices.

---

# Task 1: Create Multi-tier Application Structure

## Objective

Create a project directory for a multi-tier Docker Compose deployment.

Create the application directory:

```bash id="m8x4qv"
mkdir multi-tier-app && cd multi-tier-app
```

---

# Task 2: Create Docker Compose Configuration

## Objective

Define application services, networking, storage, and health checks using `docker-compose.yml`.

Create the Docker Compose file:

```bash id="y7p2kc"
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:

  web:
    build: ./web
    ports:
      - '80:80'
    depends_on:
      - app
    networks:
      - frontend

  app:
    build: ./app
    ports:
      - '5000:5000'
    environment:
      - DB_HOST=database
      - DB_NAME=devopsdb
      - DB_USER=appuser
      - DB_PASS=App@2024!
    depends_on:
      database:
        condition: service_healthy
    networks:
      - frontend
      - backend

  database:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: Root@2024!
      MYSQL_DATABASE: devopsdb
      MYSQL_USER: appuser
      MYSQL_PASSWORD: App@2024!
    volumes:
      - db-data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - backend

volumes:
  db-data:

networks:
  frontend:
  backend:
EOF
```

---

# Docker Compose Architecture

The application consists of three service layers:

| Service      | Purpose                     | Network            |
| ------------ | --------------------------- | ------------------ |
| **web**      | Frontend web service        | frontend           |
| **app**      | Application backend service | frontend + backend |
| **database** | MySQL database service      | backend            |

## Persistent Storage

The database uses a named Docker volume:

```text id="g8m4kp"
db-data:/var/lib/mysql
```

This ensures database data remains available even when containers are recreated.

---

# Task 3: Deploy Application Using Docker Compose

## Objective

Start and verify the multi-container application.

Start all services in detached mode:

```bash id="q5z9nr"
docker compose up -d
```

---

## Check Running Services

```bash id="r7m3vx"
docker compose ps
```

---

## View Application Logs

View application service logs:

```bash id="c4k8ws"
docker compose logs app
```

---

## Test Application

Access the application:

```bash id="h6p2qd"
curl http://localhost
```

---

# Task 4: Docker Compose Lifecycle Management

## Objective

Manage application containers using Docker Compose commands.

---

## Stop Services

```bash id="w9k3ft"
docker compose stop
```

---

## Start Services

```bash id="n2v7xm"
docker compose start
```

---

## Remove Containers

Stop and remove containers:

```bash id="p6r4kc"
docker compose down
```

---

## Remove Containers and Volumes

Remove containers along with persistent volumes:

```bash id="z8m5qw"
docker compose down -v
```

> **⚠️ Warning**
>
> The `docker compose down -v` command removes volumes and permanently deletes stored application data.

---

## Rebuild Images

```bash id="f3q7mv"
docker compose build
```

---

# Best Practices

> **✅ Best Practice**
>
> Always copy `requirements.txt` before copying the rest of the application code in a Dockerfile. This leverages Docker's build cache. If dependencies do not change, Docker can reuse the existing layer and significantly speed up builds.

> **✅ Best Practice**
>
> Never run containers as the root user. Add a `USER` instruction in your Dockerfile to improve container security.

> **✅ Best Practice**
>
> Never store secrets directly in Dockerfiles. Use environment variables, Docker secrets, or external secret management solutions.

---

# Alternative Tools

> **💡 Tip**
>
> Alternatives to DockerHub include:
>
> * **Amazon Elastic Container Registry (ECR)** — Private container registry with AWS IAM integration.
> * **GitHub Container Registry (ghcr.io)** — Container registry integrated with GitHub workflows.
> * **Harbor** — Self-hosted open-source container registry.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Created a Docker Compose multi-tier application.
* ✅ Configured web, application, and database services.
* ✅ Implemented Docker networks for service communication.
* ✅ Added persistent database storage using volumes.
* ✅ Used health checks for service dependency management.
* ✅ Managed application lifecycle using Docker Compose commands.
