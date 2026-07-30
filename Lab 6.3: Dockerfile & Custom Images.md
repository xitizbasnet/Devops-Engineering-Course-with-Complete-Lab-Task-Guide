# Lab 6.3: Dockerfile & Custom Images

## Overview

This lab introduces Docker image creation using a **Dockerfile**. You will build a containerized Flask application, define application dependencies, apply Docker security practices, create a custom image, and run the application inside a Docker container.

## Learning Objectives

By the end of this lab, you will be able to:

* Create a Dockerized Flask application.
* Define application dependencies using `requirements.txt`.
* Configure Docker image build instructions using a Dockerfile.
* Use Docker layer caching for efficient builds.
* Apply container security best practices.
* Build and run custom Docker images.

---

# Task 1: Create a Dockerized Flask Application

## Objective

Create a Flask application and package it into a Docker image using a Dockerfile.

---

## Step 1: Create Application Directory

```bash id="p6m4vs"
mkdir flask-docker && cd flask-docker
```

---

# Step 2: Create Flask Application

Create the application file:

```bash id="h8q2nc"
cat > app.py << 'EOF'
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return '<h1>🐳 Dockerized Flask App</h1><p>By Vinod Muleva</p>'

@app.route('/health')
def health():
    return {'status':'healthy','version':'1.0'}

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
EOF
```

---

# Step 3: Create requirements.txt

Define the Python dependencies:

```bash id="w7k3px"
echo 'flask==3.0.0\ngunicorn==21.2.0' > requirements.txt
```

---

# Step 4: Create .dockerignore

Create a `.dockerignore` file to exclude unnecessary files from the Docker build context:

```bash id="m2c8zf"
echo '__pycache__\n*.pyc\n.env\nvenv/' > .dockerignore
```

---

# Step 5: Create Dockerfile

Create the Dockerfile:

```bash id="f4n7qm"
cat > Dockerfile << 'EOF'
# Base image — use slim for smaller size
FROM python:3.11-slim

# Set working directory
WORKDIR /app

# Copy requirements first (Docker layer caching)
COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Create non-root user (security best practice)
RUN useradd -m appuser && chown -R appuser /app

USER appuser

# Expose port
EXPOSE 5000

# Health check
HEALTHCHECK --interval=30s --timeout=3s CMD curl -f http://localhost:5000/health || exit 1

# Run with gunicorn (production server)
CMD ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]
EOF
```

---

# Task 2: Build Custom Docker Image

## Objective

Build a Docker image from the Dockerfile.

Build the image:

```bash id="n3k8rw"
docker build -t flask-app:1.0 .
```

Verify the created image:

```bash id="q9m4dx"
docker images
```

Expected output:

```text id="o7k3va"
flask-app
```

---

# Task 3: Run and Test Container

## Objective

Run the Flask application inside a Docker container and verify the application endpoints.

Run the container:

```bash id="c6t9wp"
docker run -d -p 5000:5000 --name flask-container flask-app:1.0
```

---

## Test Application Endpoint

Access the home page:

```bash id="v5m8qr"
curl http://localhost:5000
```

Expected response:

```text id="u8n2lc"
Dockerized Flask App
By Vinod Muleva
```

---

## Test Health Endpoint

```bash id="x3q7mk"
curl http://localhost:5000/health
```

Expected response:

```json
{
  "status": "healthy",
  "version": "1.0"
}
```

---

# Best Practices

> **✅ Best Practice**
>
> Use smaller base images such as `python:3.11-slim` to reduce Docker image size and improve security.

> **✅ Best Practice**
>
> Copy dependency files before application code in a Dockerfile. This improves Docker layer caching and reduces build time.

> **✅ Best Practice**
>
> Run containers using non-root users whenever possible to improve container security.

> **✅ Best Practice**
>
> Use production application servers such as Gunicorn instead of Flask's built-in development server for production workloads.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Created a Flask application.
* ✅ Defined Python dependencies.
* ✅ Created a Dockerfile.
* ✅ Built a custom Docker image.
* ✅ Applied Docker security practices.
* ✅ Ran and tested a containerized Flask application.
