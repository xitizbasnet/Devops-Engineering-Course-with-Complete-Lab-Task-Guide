# Lab 13.2: Terraform Modules & Workspaces

## Overview

This lab introduces **Terraform Modules** and **Terraform Workspaces**, two important concepts used to build scalable and maintainable Infrastructure as Code (IaC) solutions.

You will learn how to:

* Create reusable Terraform modules.
* Use modules in root Terraform configurations.
* Manage multiple environments using Terraform Workspaces.
* Maintain separate infrastructure states for development, staging, and production environments.

---

# Lab Information

| Duration      | Difficulty Level |
| ------------- | ---------------- |
| ⏱️ 60 minutes | 🔴 Advanced      |

---

# Learning Objectives

By the end of this lab, you will be able to:

* Create reusable Terraform infrastructure components.
* Pass variables into Terraform modules.
* Export values using module outputs.
* Deploy multiple environments using workspaces.
* Maintain isolated Terraform state files.

---

# Task 1: Create a Reusable EC2 Module

## What is a Terraform Module?

A Terraform module is a collection of Terraform configuration files that can be reused across multiple projects.

Benefits:

* ♻️ Code reuse.
* 📦 Standardized infrastructure.
* 🔧 Easier maintenance.
* 🚀 Faster environment provisioning.

---

## Create Module Directory Structure

```bash
mkdir -p modules/ec2-instance
cd modules/ec2-instance
```

---

## Module Structure

Recommended module layout:

```text
modules/
└── ec2-instance/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

---

# Create Module Resource Configuration

## modules/ec2-instance/main.tf

```hcl
resource "aws_instance" "this" {
  ami = var.ami_id
  instance_type = var.instance_type
  key_name = var.key_name
  vpc_security_group_ids = [aws_security_group.this.id]
  user_data = var.user_data

  tags = merge(var.tags, { Name = var.name })
}


resource "aws_security_group" "this" {

  name_prefix = "${var.name}-sg"
  description = "SG for ${var.name}"

  dynamic "ingress" {

    for_each = var.ingress_rules

    content {

      from_port = ingress.value.from_port
      to_port = ingress.value.to_port
      protocol = ingress.value.protocol
      cidr_blocks = ingress.value.cidr_blocks

    }
  }


  egress {

    from_port = 0
    to_port = 0
    protocol = "-1"

    cidr_blocks = [
      "0.0.0.0/0"
    ]

  }
}
```

---

# Create Module Variables

## modules/ec2-instance/variables.tf

```hcl
variable "name" {}

variable "ami_id" {
  default = "ami-0f58b397bc5c1f2e8"
}

variable "instance_type" {
  default = "t2.micro"
}

variable "key_name" {}

variable "user_data" {
  default = ""
}

variable "tags" {
  default = {}
}

variable "ingress_rules" {
  default = []
}
```

---

# Create Module Outputs

## modules/ec2-instance/outputs.tf

```hcl
output "public_ip" {
  value = aws_instance.this.public_ip
}


output "instance_id" {
  value = aws_instance.this.id
}
```

---

# Module Output Explanation

| Output        | Description                   |
| ------------- | ----------------------------- |
| `public_ip`   | Returns EC2 public IP address |
| `instance_id` | Returns EC2 instance ID       |

---

# Task 2: Use Module in Root Configuration

## Root Terraform Configuration

In the root `main.tf` file:

```hcl
module "web_server" {

  source = "./modules/ec2-instance"

  name = "devops-web"

  instance_type = "t2.micro"

  key_name = "devops-key"


  ingress_rules = [

    {
      from_port = 22
      to_port = 22
      protocol = "tcp"
      cidr_blocks = [
        "0.0.0.0/0"
      ]
    },

    {
      from_port = 80
      to_port = 80
      protocol = "tcp"
      cidr_blocks = [
        "0.0.0.0/0"
      ]
    }

  ]

  tags = var.common_tags

}
```

---

# Export Module Output

Add output:

```hcl
output "web_server_ip" {

  value = module.web_server.public_ip

}
```

---

# Module Execution Workflow

```text
Root Configuration

        ↓

Terraform Module

        ↓

EC2 Instance

        ↓

Security Group

        ↓

Output Values
```

---

# Task 3: Terraform Workspaces (Multi-Environment)

## What are Terraform Workspaces?

Terraform Workspaces allow the same Terraform configuration to manage multiple environments while maintaining separate state files.

Common usage:

* Development environment.
* Staging environment.
* Production environment.

---

# Workspace Commands

## View Available Workspaces

```bash
terraform workspace list
```

Default workspace:

```text
default
```

---

## Create Workspaces

Create development workspace:

```bash
terraform workspace new dev
```

Create staging workspace:

```bash
terraform workspace new staging
```

Create production workspace:

```bash
terraform workspace new prod
```

---

## Switch Workspace

Select development:

```bash
terraform workspace select dev
```

Select production:

```bash
terraform workspace select prod
```

---

## Check Current Workspace

```bash
terraform workspace show
```

---

# Workspace State Management

Each workspace maintains an independent state file:

```text
Terraform Configuration

        |

        +---- dev state

        |

        +---- staging state

        |

        +---- prod state
```

Changes in one environment do not affect others.

---

# Use Workspace Values in Configuration

## Define Instance Type Mapping

```hcl
variable "instance_type_map" {

  default = {

    dev = "t2.micro"

    staging = "t2.small"

    prod = "t2.medium"

  }

}
```

---

## Dynamic Resource Configuration

```hcl
resource "aws_instance" "web" {

  instance_type =
    var.instance_type_map[terraform.workspace]

}
```

---

# Environment Mapping

| Workspace | Instance Type |
| --------- | ------------- |
| dev       | t2.micro      |
| staging   | t2.small      |
| prod      | t2.medium     |

Terraform automatically selects the correct instance size based on the active workspace.

---

# Deploy Development Environment

Select development:

```bash
terraform workspace select dev
```

Apply:

```bash
terraform apply
```

---

# Deploy Production Environment

Select production:

```bash
terraform workspace select prod
```

Apply:

```bash
terraform apply
```

---

# Best Practices

> **✅ Best Practice**
>
> Use Terraform modules for repeated infrastructure patterns such as:
>
> * EC2 instances.
> * VPCs.
> * Databases.
> * Kubernetes clusters.

---

> **✅ Best Practice**
>
> Keep environment states isolated. Development changes should never impact production infrastructure.

---

> **✅ Best Practice**
>
> Store reusable modules in a version-controlled repository and maintain proper module versioning.

---

# Lab Summary

In this lab, you completed:

* ✅ Created a reusable EC2 Terraform module.
* ✅ Defined module variables and outputs.
* ✅ Used modules from a root Terraform configuration.
* ✅ Created Terraform workspaces.
* ✅ Managed multiple environments using separate states.
* ✅ Automated environment-specific infrastructure sizing.

---

# Next Step

Continue to **Lab 13.3: End-to-End AWS Architecture with Terraform** to build a complete AWS infrastructure using Terraform modules, networking, compute, and application components.
