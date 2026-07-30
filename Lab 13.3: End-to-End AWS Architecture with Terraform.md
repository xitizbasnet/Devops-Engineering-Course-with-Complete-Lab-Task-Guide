# Lab 13.3: End-to-End AWS Architecture with Terraform

## Overview

This lab demonstrates how to deploy a complete **three-tier AWS infrastructure architecture** using Terraform.

The architecture includes:

* 🌐 **Network Layer** — VPC with public, private, and database subnets.
* 🖥️ **Application Layer** — EC2 web server deployment.
* 🗄️ **Database Layer** — Managed MySQL RDS database.

Terraform modules are used to create scalable and reusable infrastructure components.

---

# Lab Information

| Duration      | Difficulty Level |
| ------------- | ---------------- |
| ⏱️ 90 minutes | 🔴 Expert        |

---

# Learning Objectives

By the end of this lab, you will be able to:

* Build a complete AWS three-tier architecture using Terraform.
* Use community Terraform modules.
* Provision VPC networking components.
* Deploy EC2 application servers.
* Create managed RDS databases.
* Execute Terraform deployment lifecycle commands.
* Safely clean up infrastructure after testing.

---

# Architecture Overview

```text
                    Internet
                       |
                       |
                 Public Subnets
                       |
                +--------------+
                | Web Server   |
                | EC2 + Nginx  |
                +--------------+
                       |
                       |
                Private Subnets
                       |
                Application Layer
                       |
                       |
              Database Subnets
                       |
                +--------------+
                | MySQL RDS    |
                +--------------+
```

---

# Create Terraform Project

Create project directory:

```bash
mkdir end-to-end-infra && cd end-to-end-infra
```

---

# Terraform Infrastructure Components

This deployment creates:

| Component        | Terraform Resource        |
| ---------------- | ------------------------- |
| VPC              | Terraform AWS VPC Module  |
| Public Subnets   | Internet-facing resources |
| Private Subnets  | Application resources     |
| Database Subnets | RDS database layer        |
| EC2 Instance     | Web server                |
| RDS MySQL        | Managed database          |

---

# main.tf — VPC + EC2 + RDS Configuration

## VPC Module Configuration

```hcl
module "vpc" {

source = "terraform-aws-modules/vpc/aws"

version = "5.0.0"


name = "devops-vpc"

cidr = "10.0.0.0/16"


azs = [
"ap-south-1a",
"ap-south-1b"
]


public_subnets = [
"10.0.1.0/24",
"10.0.2.0/24"
]


private_subnets = [
"10.0.3.0/24",
"10.0.4.0/24"
]


database_subnets = [
"10.0.5.0/24",
"10.0.6.0/24"
]


enable_nat_gateway = true

single_nat_gateway = true


enable_dns_hostnames = true


tags = {

Project = "DevOps"

ManagedBy = "Terraform"

}

}
```

---

# VPC Configuration Explanation

| Configuration        | Purpose                               |
| -------------------- | ------------------------------------- |
| `cidr`               | Defines VPC IP range                  |
| `public_subnets`     | Hosts internet-facing resources       |
| `private_subnets`    | Hosts application services            |
| `database_subnets`   | Isolated database network             |
| `enable_nat_gateway` | Allows private subnet outbound access |
| `single_nat_gateway` | Reduces lab cost                      |

---

# EC2 Web Server Module

Deploy the application server:

```hcl
module "web_server" {

source = "./modules/ec2-instance"


name = "web-server"


key_name = "devops-key"


user_data = <<-EOF

#!/bin/bash

apt update

apt install nginx -y

systemctl start nginx

EOF

}
```

---

# EC2 Deployment Explanation

The EC2 instance automatically:

1. Launches using the Terraform EC2 module.
2. Installs Nginx.
3. Starts the web service.
4. Becomes ready to serve HTTP traffic.

---

# RDS MySQL Database Module

```hcl
module "db" {

source = "terraform-aws-modules/rds/aws"

version = "6.0.0"


identifier = "devops-rds"


engine = "mysql"

engine_version = "8.0"


instance_class = "db.t3.micro"


allocated_storage = 20


db_name = "devopsdb"


username = "admin"


password = var.db_password


subnet_ids = module.vpc.database_subnets


vpc_security_group_ids = [
aws_security_group.rds.id
]


skip_final_snapshot = true

}
```

---

# RDS Configuration Explanation

| Setting               | Description                                           |
| --------------------- | ----------------------------------------------------- |
| `engine`              | Database engine type                                  |
| `engine_version`      | MySQL version                                         |
| `instance_class`      | Database instance size                                |
| `allocated_storage`   | Storage capacity                                      |
| `subnet_ids`          | Database subnet placement                             |
| `skip_final_snapshot` | Removes database snapshot requirement during deletion |

---

# Terraform Deployment Workflow

## Step 1: Initialize Terraform

Downloads providers and modules:

```bash
terraform init
```

---

## Step 2: Review Infrastructure Plan

Generate execution plan:

```bash
terraform plan -var='db_password=Admin@2024!'
```

Review:

* Resources to create.
* Network changes.
* Security changes.
* Database configuration.

---

## Step 3: Deploy Infrastructure

Create AWS resources:

```bash
terraform apply -var='db_password=Admin@2024!' -auto-approve
```

---

## Step 4: View Outputs

Display Terraform outputs:

```bash
terraform output
```

---

# Verify Deployment

After successful deployment:

## Verify EC2

Check:

* Instance status.
* Public IP address.
* Nginx availability.

Example:

```bash
curl http://<EC2_PUBLIC_IP>
```

Expected:

```text
Nginx welcome page
```

---

## Verify RDS

Check:

* Database endpoint.
* MySQL availability.
* Security group access.

---

# Infrastructure Cleanup

⚠️ Always remove lab resources after completion to avoid AWS charges.

Destroy infrastructure:

```bash
terraform destroy -auto-approve
```

---

# Terraform Lifecycle

```text
Write Configuration

        ↓

terraform init

        ↓

terraform plan

        ↓

terraform apply

        ↓

Infrastructure Running

        ↓

terraform destroy
```

---

# Best Practices

> **✅ Best Practice**
>
> Always use a remote Terraform state backend for team projects.
>
> Recommended setup:
>
> * Amazon S3 for state storage.
> * DynamoDB for state locking.
>
> Local state files should only be used for personal labs.

---

> **✅ Best Practice**
>
> Always review `terraform plan` output before applying changes in production environments.

---

> **✅ Best Practice**
>
> Use targeted deployments carefully:
>
> ```bash
> terraform apply -target=<resource>
> ```
>
> This allows applying changes to specific resources only when required.

---

# Alternative Infrastructure as Code Tools

| Tool      | Description                                                                         |
| --------- | ----------------------------------------------------------------------------------- |
| Pulumi    | Write Infrastructure as Code using Python, JavaScript, or Go                        |
| AWS CDK   | Define AWS infrastructure using programming languages such as Python and TypeScript |
| Terraform | Uses HCL and supports multi-cloud infrastructure                                    |

---

# Lab Summary

In this lab, you completed:

* ✅ Created a complete AWS three-tier architecture.
* ✅ Provisioned a custom VPC using Terraform modules.
* ✅ Created public, private, and database subnet layers.
* ✅ Deployed an EC2 web server with Nginx.
* ✅ Created a managed MySQL RDS database.
* ✅ Applied Terraform deployment workflow.
* ✅ Cleaned up infrastructure safely.

---

# DevOps Course Completion

🎉 **Congratulations!**

You have completed the complete DevOps Infrastructure Automation journey covering:

* Linux Administration
* Python Automation
* AWS Infrastructure
* Git & GitFlow
* Docker
* Puppet
* Ansible
* Selenium Testing
* Jenkins CI/CD
* Kubernetes
* ELK Monitoring
* Terraform Infrastructure as Code

You are now familiar with the complete modern DevOps lifecycle from development to production operations.
