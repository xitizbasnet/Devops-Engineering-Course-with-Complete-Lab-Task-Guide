# Lab 10.2: End-to-End CI, CD Pipeline in Jenkins

## Overview

This lab demonstrates how to build a complete CI/CD pipeline using Jenkins. You will create a Jenkins Pipeline as Code using a `Jenkinsfile`, automate application testing, build and publish Docker images, deploy the application, perform health checks, and configure GitHub webhook-based automation.

## Learning Objectives

By the end of this lab, you will be able to:

* Create Jenkins declarative pipelines using a `Jenkinsfile`.
* Integrate Jenkins with GitHub repositories.
* Automate application testing.
* Build and publish Docker images.
* Deploy applications automatically using Jenkins.
* Configure GitHub webhooks for continuous integration.
* Apply Jenkins CI/CD security best practices.

---

# Task 1: Create Jenkinsfile for Full Pipeline

## Objective

Create a complete CI/CD pipeline that performs:

* Source code checkout.
* Automated testing.
* Docker image creation.
* DockerHub image publishing.
* Application deployment.
* Deployment health verification.

---

## Step 1: Create Jenkinsfile

In your GitHub repository, create a file named:

```text
Jenkinsfile
```

Create the file:

```bash
cat > Jenkinsfile << 'EOF'
```

Add the following pipeline configuration:

```groovy
pipeline {

    agent { 
        label 'linux' 
    }

    environment {

        DOCKER_HUB = credentials('dockerhub-credentials')

        IMAGE_NAME = "${DOCKER_HUB_USR}/flask-devops"

        APP_PORT = '5000'
    }


    stages {


        stage('Checkout') {

            steps {

                echo '=== Cloning repository ==='

                git url: 'https://github.com/<user>/devops-project.git',
                branch: 'main'

            }

        }


        stage('Run Tests') {

            steps {

                echo '=== Running unit tests ==='

                sh 'pip3 install pytest flask -q'

                sh 'python3 -m pytest tests/ -v || true'

            }

        }


        stage('Build Docker Image') {

            steps {

                echo "=== Building ${IMAGE_NAME}:${BUILD_NUMBER} ==="

                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."

                sh "docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest"

            }

        }


        stage('Push to DockerHub') {

            steps {

                sh "echo ${DOCKER_HUB_PSW} | docker login -u ${DOCKER_HUB_USR} --password-stdin"

                sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"

                sh "docker push ${IMAGE_NAME}:latest"

            }

        }


        stage('Deploy') {

            steps {

                echo '=== Deploying application ==='

                sh "docker stop flask-app || true"

                sh "docker rm flask-app || true"

                sh "docker run -d -p ${APP_PORT}:5000 --name flask-app ${IMAGE_NAME}:latest"

            }

        }


        stage('Health Check') {

            steps {

                sleep(10)

                sh "curl -f http://localhost:${APP_PORT}/health || exit 1"

                echo 'Deployment healthy!'

            }

        }

    }


    post {


        success {

            echo "Pipeline SUCCESS — Build #${BUILD_NUMBER}"

        }


        failure {

            echo "Pipeline FAILED — Check logs!"

        }


        always {

            sh 'docker image prune -f'

        }

    }

}
EOF
```

---

# Pipeline Stage Explanation

| Stage              | Purpose                                             |
| ------------------ | --------------------------------------------------- |
| Checkout           | Downloads source code from GitHub.                  |
| Run Tests          | Installs dependencies and executes automated tests. |
| Build Docker Image | Creates application container image.                |
| Push to DockerHub  | Publishes Docker image to registry.                 |
| Deploy             | Runs the latest application container.              |
| Health Check       | Validates application availability.                 |

---

# Task 2: Create Pipeline Job in Jenkins

## Objective

Configure Jenkins to automatically execute the pipeline from the GitHub repository.

---

## Step 1: Create Jenkins Pipeline Job

Navigate:

```text
Jenkins Dashboard
        ↓
New Item
```

Configure:

```text
Name:
devops-cicd-pipeline

Type:
Pipeline
```

Select:

```text
OK
```

---

## Step 2: Configure Pipeline from SCM

Under the **Pipeline** section:

```text
Definition:
Pipeline script from SCM

SCM:
Git

Repository URL:
https://github.com/<user>/devops-project.git

Branch:
*/main

Script Path:
Jenkinsfile
```

Add GitHub credentials:

```text
Credentials:
Add GitHub credentials
```

Save configuration.

---

## Step 3: Add DockerHub Credentials

Navigate:

```text
Manage Jenkins
        ↓
Credentials
        ↓
System
        ↓
Global
        ↓
Add Credentials
```

Configure:

```text
Kind:
Username with password

Username:
<dockerhub-user>

Password:
<dockerhub-password-or-PAT>

ID:
dockerhub-credentials
```

Save credentials.

---

## Step 4: Run Pipeline

From Jenkins Dashboard:

```text
devops-cicd-pipeline
        ↓
Build Now
```

Jenkins will execute:

```text
Checkout
    ↓
Testing
    ↓
Docker Build
    ↓
Docker Push
    ↓
Deployment
    ↓
Health Check
```

---

# Task 3: Configure GitHub Webhook

## Objective

Automatically trigger Jenkins builds whenever code is pushed to GitHub.

---

## Step 1: Add Webhook in GitHub

Navigate:

```text
GitHub Repository
        ↓
Settings
        ↓
Webhooks
        ↓
Add webhook
```

Configure:

```text
Payload URL:

http://<JENKINS_IP>:8080/github-webhook/

Content Type:

application/json

Events:

Just the push event
```

Select:

```text
Add webhook
```

---

## Step 2: Configure Jenkins Build Trigger

Open Jenkins Pipeline configuration:

```text
Pipeline Job
        ↓
Configure
```

Enable:

```text
Build Triggers:

✓ GitHub hook trigger for GITScm polling
```

Save changes.

---

## Step 3: Test Automatic Trigger

Make a Git change:

```bash
git push
```

Expected behavior:

```text
GitHub Push Event
        ↓
Webhook Trigger
        ↓
Jenkins Pipeline Starts Automatically
```

---

# CI/CD Pipeline Architecture

```text
Developer
    |
    | git push
    ↓
GitHub Repository
    |
    | Webhook
    ↓
Jenkins Pipeline
    |
    ├── Checkout Code
    |
    ├── Run Tests
    |
    ├── Build Docker Image
    |
    ├── Push Image
    |
    ├── Deploy Container
    |
    └── Health Check
```

---

# Best Practices

> **✅ Best Practice**
>
> Use the Blue Ocean plugin for a visual Jenkins pipeline interface. Use Pipeline Stage View to quickly identify failed pipeline stages.

> **✅ Best Practice**
>
> Never store credentials directly inside a Jenkinsfile.
>
> Use Jenkins Credentials Store and reference secrets using:
>
> * `credentials()`
> * `withCredentials()`

> **✅ Best Practice**
>
> Keep pipeline logic version-controlled by storing Jenkinsfiles inside the application repository.

---

# Alternative Tools

> **💡 Tip**
>
> Alternative CI/CD platforms include:
>
> * **GitLab CI/CD** — Provides built-in CI/CD functionality without requiring a separate Jenkins installation.
> * **GitHub Actions** — Ideal for repositories hosted on GitHub and provides free CI/CD minutes for public repositories.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Created an end-to-end Jenkins CI/CD pipeline.
* ✅ Automated source checkout and application testing.
* ✅ Built and published Docker images.
* ✅ Automated application deployment.
* ✅ Added deployment health checks.
* ✅ Configured GitHub webhook automation.
* ✅ Applied Jenkins security and pipeline best practices.
