# Lab 10.1: Jenkins Master-Slave Architecture Setup

## Overview

This lab introduces Jenkins distributed build architecture using a Jenkins Master and Jenkins Agent (Slave) setup. You will install Jenkins on a master EC2 instance, configure Jenkins through the web interface, install required plugins, and connect an agent node for distributed build execution.

## Learning Objectives

By the end of this lab, you will be able to:

* Install Jenkins on an AWS EC2 Ubuntu instance.
* Configure Jenkins using the initial setup wizard.
* Install essential Jenkins plugins.
* Understand Jenkins Master-Agent architecture.
* Connect an agent node using SSH.
* Execute workloads on distributed Jenkins nodes.

---

# Task 1: Install Jenkins on Master EC2

## Objective

Install Jenkins on the Jenkins Master EC2 instance.

---

## Step 1: Launch Jenkins Master EC2 Instance

Launch an EC2 instance:

```text
Instance Name: jenkins-master
Operating System: Ubuntu 22.04
Instance Type: t2.medium
Security Group:
- SSH (22)
- Jenkins Web UI (8080)
```

Connect to the instance:

```bash id="v8m3qp"
ssh -i devops-key.pem ubuntu@<JENKINS_MASTER_IP>
```

---

## Step 2: Install Java

Update packages:

```bash id="x4n7mc"
sudo apt update
```

Install OpenJDK 17:

```bash id="p6r2vz"
sudo apt install openjdk-17-jdk -y
```

Verify Java:

```bash id="q8m5kw"
java -version
```

---

## Step 3: Add Jenkins Repository

Download Jenkins signing key:

```bash id="w3x7mq"
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```

Add Jenkins repository:

```bash id="k9m4qp"
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/" \
| sudo tee /etc/apt/sources.list.d/jenkins.list
```

---

## Step 4: Install Jenkins

Update package list:

```bash id="n7q3mx"
sudo apt update
```

Install Jenkins:

```bash id="r5v8kc"
sudo apt install jenkins -y
```

Enable and start Jenkins:

```bash id="m2x9qp"
sudo systemctl enable jenkins

sudo systemctl start jenkins
```

Check Jenkins status:

```bash id="h6w4mz"
sudo systemctl status jenkins
```

---

## Step 5: Retrieve Initial Jenkins Password

Get the initial administrator password:

```bash id="z8p3mv"
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Save this password for the first login.

---

# Task 2: Jenkins Initial Setup via Browser

## Objective

Complete the Jenkins setup wizard and install required plugins.

---

## Step 1: Open Jenkins Dashboard

Access Jenkins:

```text
http://<JENKINS_MASTER_IP>:8080
```

---

## Step 2: Unlock Jenkins

Paste the initial administrator password obtained earlier.

---

## Step 3: Install Recommended Plugins

Select:

```text
Install suggested plugins
```

Wait approximately:

```text
3-5 minutes
```

---

## Step 4: Create Administrator User

Create the admin account:

```text
Username: admin

Password: Admin@Jenkins2024

Full Name: Vinod Muleva

Jenkins URL:
http://<JENKINS_IP>:8080/
```

Select:

```text
Start using Jenkins
```

---

# Step 5: Install Additional Jenkins Plugins

Navigate:

```text
Manage Jenkins
    ↓
Plugins
    ↓
Available Plugins
```

Install:

| Plugin                    | Purpose                           |
| ------------------------- | --------------------------------- |
| Docker Pipeline           | Docker-based CI/CD pipelines      |
| GitHub Integration Plugin | GitHub repository integration     |
| Maven Integration         | Maven build support               |
| Pipeline Stage View       | Visual pipeline stages            |
| Blue Ocean                | Modern Jenkins pipeline interface |

Select:

```text
Install without restart
```

---

# Task 3: Add Jenkins Slave / Agent Node

## Objective

Connect an additional EC2 instance as a Jenkins Agent for distributed builds.

---

## Step 1: Launch Jenkins Agent EC2 Instance

Launch:

```text
Instance Name: jenkins-agent

OS: Ubuntu 22.04

Instance Type: t2.micro
```

---

## Step 2: Install Required Tools on Agent

Connect to agent:

```bash id="c5m8xr"
ssh -i devops-key.pem ubuntu@<AGENT_IP>
```

Update packages:

```bash id="d7q2mw"
sudo apt update
```

Install Java, Maven, and Docker:

```bash id="s4x8vp"
sudo apt install openjdk-17-jdk maven docker.io -y
```

Add Ubuntu user to Docker group:

```bash id="q3m7kc"
sudo usermod -aG docker ubuntu
```

---

# Step 3: Configure Agent Node in Jenkins UI

On Jenkins Master:

Navigate:

```text
Manage Jenkins
    ↓
Nodes
    ↓
New Node
```

---

## Node Configuration

Configure:

```text
Node name:
agent-1

Type:
Permanent Agent

Number of executors:
2

Remote root directory:
/home/ubuntu/jenkins

Labels:
linux docker maven

Launch method:
Launch agent via SSH

Host:
<AGENT_PRIVATE_IP>
```

---

## Step 4: Add SSH Credentials

Select:

```text
Credentials
    ↓
Add
    ↓
SSH Username with private key
```

Configure:

```text
Username:
ubuntu

Private Key:
Paste contents of devops-key.pem
```

Host Key Verification:

```text
Non verifying
```

---

## Step 5: Connect Agent

Save configuration:

```text
Save
```

Launch agent:

```text
Launch agent
```

Expected status:

```text
Status: Connected
```

---

# Jenkins Master-Agent Architecture

```text
                 Jenkins Master
                       |
                       |
              SSH Communication
                       |
                       |
              Jenkins Agent Node
                       |
        -------------------------------
        |             |               |
      Maven         Docker          Builds
```

---

# Best Practices

> **✅ Best Practice**
>
> Use Jenkins agents for build execution instead of running all workloads on the Jenkins master server. This improves scalability and security.

> **✅ Best Practice**
>
> Keep Jenkins master focused on orchestration tasks and delegate resource-intensive builds to agent nodes.

> **✅ Best Practice**
>
> Secure Jenkins using:
>
> * Strong administrator passwords.
> * Role-based access control.
> * Regular plugin updates.
> * Restricted network access to port 8080.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Installed Jenkins on an AWS EC2 Master node.
* ✅ Configured Jenkins through the browser setup wizard.
* ✅ Installed required Jenkins plugins.
* ✅ Created a Jenkins Agent node.
* ✅ Connected the agent using SSH authentication.
* ✅ Built a distributed Jenkins Master-Agent architecture.
