# Lab 13.1: Terraform Setup & Basic Operations

## Overview

This lab introduces **Terraform Infrastructure as Code (IaC)** concepts and demonstrates how to install Terraform, understand its role compared to configuration management tools, and provision AWS resources using Terraform configurations.

---

# Lab Information

| Duration      | Difficulty Level |
| ------------- | ---------------- |
| ⏱️ 45 minutes | 🟡 Intermediate  |

---

# Learning Objectives

By the end of this lab, you will be able to:

* Install and verify Terraform on Linux.
* Understand the difference between Infrastructure as Code and Configuration Management.
* Create Terraform configuration files.
* Initialize Terraform projects.
* Validate and deploy AWS resources.
* Manage Terraform state and outputs.
* Destroy infrastructure safely after testing.

---

# Task 1: Install Terraform

## Install Required Packages

```bash
sudo apt update && sudo apt install -y gnupg software-properties-common
```

---

## Add HashiCorp GPG Key

```bash
wget -O- https://apt.releases.hashicorp.com/gpg \
| gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
```

---

## Add Terraform Repository

```bash
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" \
| sudo tee /etc/apt/sources.list.d/hashicorp.list
```

---

## Install Terraform

```bash
sudo apt update && sudo apt install terraform -y
```

---

## Verify Installation

```bash
terraform version
```

Enable Terraform command autocomplete:

```bash
terraform -install-autocomplete
```

---

# Task 2: IaC vs Configuration Management

## Key Difference Between Terraform and Configuration Management Tools

| Aspect           | IaC (Terraform)                                                  | Configuration Management (Ansible/Puppet)      |
| ---------------- | ---------------------------------------------------------------- | ---------------------------------------------- |
| What it manages  | Infrastructure resources such as EC2, VPC, RDS                   | Software and configuration on existing servers |
| When to use      | Provisioning new resources                                       | Configuring and deploying applications         |
| State management | Maintains Terraform state file                                   | Idempotent execution without a state file      |
| Language         | HCL (HashiCorp Configuration Language)                           | YAML / DSL                                     |
| Usage together   | Terraform provisions infrastructure → Ansible configures servers | Used after infrastructure creation             |

---

# Terraform + Configuration Management Workflow

```text
Terraform

    ↓

Creates AWS Infrastructure

    ↓

Ansible / Puppet

    ↓

Configures Applications and Services
```

---

# Task 3: Terraform Core Workflow

## Create Terraform Project Directory

```bash
mkdir terraform-aws && cd terraform-aws
```

---

# Create Terraform Configuration Files

Terraform projects commonly use:

| File           | Purpose                         |
| -------------- | ------------------------------- |
| `main.tf`      | Main infrastructure definitions |
| `variables.tf` | Input variable definitions      |
| `outputs.tf`   | Output values after deployment  |

---

# main.tf — Create S3 Bucket

Create the Terraform configuration:

```bash
cat > main.tf << 'EOF'
terraform {
required_providers {
aws = { 
source = "hashicorp/aws"
version = "~> 5.0"
}
backend "s3" {
bucket = "vinod-terraform-state"
key = "devops/terraform.tfstate"
region = "ap-south-1"
}
}

provider "aws" {
region = var.aws_region
}

resource "aws_s3_bucket" "lab_bucket" {
bucket = "${var.project_name}-lab-${random_id.suffix.hex}"
tags = var.common_tags
}

resource "random_id" "suffix" {
byte_length = 4
}
EOF
```

---

# variables.tf — Define Variables

Create variable definitions:

```bash
cat > variables.tf << 'EOF'
variable "aws_region" {
description = "AWS region"
type = string
default = "ap-south-1"
}

variable "project_name" {
description = "Project name prefix"
type = string
default = "devops"
}

variable "common_tags" {
description = "Common resource tags"
type = map(string)

default = {
Project = "DevOps"
ManagedBy = "Terraform"
Environment = "Lab"
}
}
EOF
```

---

# outputs.tf — Define Outputs

Create output values:

```bash
cat > outputs.tf << 'EOF'
output "bucket_name" {
value = aws_s3_bucket.lab_bucket.bucket
}

output "bucket_arn" {
value = aws_s3_bucket.lab_bucket.arn
}
EOF
```

---

# Terraform Core Workflow

Terraform follows the workflow:

```text
Write Configuration

        ↓

Initialize Provider

        ↓

Validate Configuration

        ↓

Preview Changes

        ↓

Apply Infrastructure

        ↓

Manage State

        ↓

Destroy Resources
```

---

# Step 1: Initialize Terraform

Downloads required providers:

```bash
terraform init
```

---

# Step 2: Format Configuration

Automatically formats Terraform files:

```bash
terraform fmt
```

---

# Step 3: Validate Configuration

Checks Terraform syntax:

```bash
terraform validate
```

Expected output:

```text
Success! The configuration is valid.
```

---

# Step 4: Preview Infrastructure Changes

Shows resources Terraform will create:

```bash
terraform plan
```

---

# Step 5: Create AWS Resources

Deploy infrastructure:

```bash
terraform apply
```

Confirm deployment:

```text
Type:
yes
```

---

# Step 6: View Terraform Outputs

Display generated outputs:

```bash
terraform output
```

---

# Step 7: View Current Terraform State

Show managed resources:

```bash
terraform show
```

---

# Step 8: Destroy Infrastructure

Remove all Terraform-managed resources:

```bash
terraform destroy
```

Confirm:

```text
Type:
yes
```

---

# Terraform Command Reference

| Command              | Description                               |
| -------------------- | ----------------------------------------- |
| `terraform init`     | Initialize project and download providers |
| `terraform fmt`      | Format Terraform configuration files      |
| `terraform validate` | Validate syntax and configuration         |
| `terraform plan`     | Preview infrastructure changes            |
| `terraform apply`    | Create or update infrastructure           |
| `terraform output`   | Display output values                     |
| `terraform show`     | View Terraform state                      |
| `terraform destroy`  | Delete managed infrastructure             |

---

# Best Practices

> **✅ Best Practice**
>
> Always run `terraform plan` before `terraform apply` to review infrastructure changes before deployment.

---

> **✅ Best Practice**
>
> Store Terraform state remotely using an S3 backend with state locking for team environments.

---

> **✅ Best Practice**
>
> Use variables and outputs instead of hardcoding values directly inside Terraform resources.

---

# Lab Summary

In this lab, you completed:

* ✅ Terraform installation.
* ✅ Understanding of IaC concepts.
* ✅ Comparison between Terraform and configuration management.
* ✅ AWS provider configuration.
* ✅ S3 bucket provisioning using Terraform.
* ✅ Terraform workflow execution.
* ✅ Infrastructure lifecycle management.

---

# Next Step

Continue to **Lab 13.2: Terraform Modules** to learn how to create reusable Terraform components and organize infrastructure code for enterprise environments.
