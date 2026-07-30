# Lab 6.4: Docker Volumes & Networking

## Overview

This lab introduces Docker data persistence and container networking concepts. You will learn how to create and manage Docker volumes, persist database data beyond container lifecycles, and configure custom Docker networks to allow communication between containers.

## Learning Objectives

By the end of this lab, you will be able to:

* Create and manage Docker volumes.
* Persist application data outside containers.
* Run database containers with persistent storage.
* Create custom Docker networks.
* Connect containers using container names.
* Understand different Docker network types.

---

# Task 1: Docker Volumes — Persist Data

## Objective

Use Docker volumes to store persistent data that remains available even after containers are removed.

---

## Step 1: Create a Named Volume

Create a Docker volume:

```bash id="r4p8xm"
docker volume create devops-data
```

List available volumes:

```bash id="m6q2vk"
docker volume ls
```

Inspect volume details:

```bash id="x9n3cw"
docker volume inspect devops-data
```

---

## Step 2: Run MySQL with a Persistent Volume

Start a MySQL container and attach the volume:

```bash id="k7t5qz"
docker run -d \
--name mysql-db \
-e MYSQL_ROOT_PASSWORD=Root@2024 \
-e MYSQL_DATABASE=devopsdb \
-v devops-data:/var/lib/mysql \
mysql:8
```

The volume mapping:

```text
devops-data:/var/lib/mysql
```

ensures that MySQL database files are stored outside the container.

---

## Step 3: Test Data Persistence

Create a table and insert data:

```bash id="n5v8qd"
docker exec -it mysql-db mysql -uroot -pRoot@2024 devopsdb -e \
'CREATE TABLE test(id INT); INSERT INTO test VALUES(1);'
```

Stop and remove the container:

```bash id="z8m4px"
docker stop mysql-db && docker rm mysql-db
```

---

## Step 4: Re-create Container Using the Same Volume

Create a new MySQL container with the existing volume:

```bash id="w3q7hs"
docker run -d --name mysql-db \
-e MYSQL_ROOT_PASSWORD=Root@2024 \
-e MYSQL_DATABASE=devopsdb \
-v devops-data:/var/lib/mysql \
mysql:8
```

Verify that the data still exists:

```bash id="c9k6rf"
docker exec -it mysql-db mysql -uroot -pRoot@2024 devopsdb -e 'SELECT * FROM test;'
```

Expected result:

```text id="e7m2zn"
Row still there!
```

> **ℹ️ Note**
>
> Docker volumes allow data to survive container deletion and recreation, making them essential for databases and stateful applications.

---

# Task 2: Docker Networking

## Objective

Create a custom Docker network and allow containers to communicate with each other using container names.

---

## Step 1: Create Custom Network

Create a Docker network:

```bash id="t8p5yd"
docker network create devops-net
```

List Docker networks:

```bash id="q4m9vk"
docker network ls
```

---

## Step 2: Run Containers on the Same Network

Run the backend container:

```bash id="b6x3nr"
docker run -d \
--name backend \
--network devops-net \
flask-app:1.0
```

Run the database container:

```bash id="p7w2kc"
docker run -d \
--name db \
--network devops-net \
-e MYSQL_ROOT_PASSWORD=Root@2024 \
mysql:8
```

---

## Step 3: Test Container Communication

From the backend container, connect to the database container using its hostname:

```bash id="h5n8mv"
docker exec -it backend ping db
```

The container name resolves automatically:

```text id="a3q7ws"
db → container hostname resolution
```

---

# Docker Network Types

| Network Type | Description                                                                |
| ------------ | -------------------------------------------------------------------------- |
| **bridge**   | Default network. Provides isolated containers on the same Docker host.     |
| **host**     | Shares the host network directly with no network isolation.                |
| **overlay**  | Supports multi-host networking for Docker Swarm / Kubernetes environments. |
| **none**     | Disables networking for the container.                                     |

---

# Best Practices

> **✅ Best Practice**
>
> Use Docker volumes for databases and other stateful applications instead of storing important data inside containers.

> **✅ Best Practice**
>
> Use custom Docker networks instead of relying only on the default bridge network when running multiple containers.

> **✅ Best Practice**
>
> Avoid exposing database ports publicly. Allow database communication only between required application containers.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Created and managed Docker volumes.
* ✅ Persisted MySQL data across container recreation.
* ✅ Created custom Docker networks.
* ✅ Connected multiple containers using Docker networking.
* ✅ Learned different Docker network types.
