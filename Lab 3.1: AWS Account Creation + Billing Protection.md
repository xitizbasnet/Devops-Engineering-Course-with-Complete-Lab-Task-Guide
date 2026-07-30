# Lab 3.1: AWS Account Creation + Billing Protection

## Overview

This lab guides you through creating an AWS Free Tier account and configuring billing protection to help prevent unexpected charges. You will also create a billing alarm using Amazon CloudWatch.

## Learning Objectives

By the end of this lab, you will be able to:

* Create an AWS Free Tier account.
* Configure the preferred AWS Region.
* Create a billing alarm using Amazon CloudWatch.
* Receive email notifications when estimated charges exceed a defined threshold.

---

# Task 1: Create Free Tier Account & Set Billing Alarm

## Objective

Create an AWS account and configure billing alerts to monitor estimated AWS charges.

### Step 1: Create an AWS Account

```text
Go to https://aws.amazon.com → Create an AWS Account
```

* Use a dedicated email address.
* Choose the **Free Tier** plan.

---

### Step 2: Select the AWS Region

Set the AWS Region to:

```text
ap-south-1 (Mumbai)
```

This option is available in the top-right corner of the AWS Management Console.

---

### Step 3: Create a Billing Alarm

Create a billing alarm to help prevent unexpected AWS charges.

```text
CloudWatch (switch to us-east-1 for billing metrics)
```

Navigate through the following path:

```text
CloudWatch → Alarms → Billing → Create Alarm
```

Configure the alarm using the following settings:

| Setting                | Value                     |
| ---------------------- | ------------------------- |
| **Metric**             | EstimatedCharges > $5 USD |
| **SNS Topic**          | billing-alerts            |
| **Notification Email** | Your email                |
| **Subscription**       | Confirm subscription      |
| **Alarm Name**         | AWS-Spend-Alert           |

> **⚠️ Important**
>
> Billing metrics are available only in the **us-east-1 (N. Virginia)** Region. Switch to this region before creating the billing alarm.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Created an AWS Free Tier account.
* ✅ Configured the preferred AWS Region.
* ✅ Created an Amazon CloudWatch billing alarm.
* ✅ Configured email notifications for billing alerts.

---

# Lab 3.2: EC2 Setup & Full Walkthrough

## Overview

This lab introduces the Amazon EC2 service by launching an Ubuntu instance, exploring the AWS Management Console, installing the AWS CLI, and configuring programmatic access using IAM credentials.

## Learning Objectives

By the end of this lab, you will be able to:

* Launch an Amazon EC2 instance.
* Explore the EC2 Management Console.
* Install the AWS Command Line Interface (AWS CLI).
* Configure AWS CLI credentials.
* Verify connectivity using AWS CLI commands.

---

# Task 1: Launch EC2 + Explore Console

## Objective

Launch an EC2 instance and configure the AWS CLI for command-line management.

### Launch an EC2 Instance

Navigate to:

```text
EC2 → Launch Instance
```

Configure the instance using the following settings:

| Setting            | Value                        |
| ------------------ | ---------------------------- |
| **Name**           | `devops-infra-lab`           |
| **AMI**            | Ubuntu 22.04 LTS (Free Tier) |
| **Instance Type**  | `t2.micro`                   |
| **Key Pair**       | `devops-key`                 |
| **Security Group** | SSH (22) + HTTP (80)         |

Launch the instance after completing the configuration.

---

### Install the AWS CLI (on EC2)

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

sudo apt install unzip -y

unzip awscliv2.zip

sudo ./aws/install

aws --version
```

---

### Configure AWS CLI

Configure the AWS CLI using IAM user credentials.

```bash
aws configure
```

Provide the following information when prompted:

| Prompt                | Value                                       |
| --------------------- | ------------------------------------------- |
| **Access Key**        | `<from IAM → Users → Security Credentials>` |
| **Secret Access Key** | `<from same>`                               |
| **Default Region**    | `ap-south-1`                                |
| **Output Format**     | `json`                                      |

---

### Verify the Configuration

```bash
aws ec2 describe-instances --region ap-south-1 --output table
```

---

# Best Practices

> **✅ Best Practice**
>
> Create a dedicated IAM user with least-privilege permissions instead of using the root account for daily AWS operations. Enable Multi-Factor Authentication (MFA) on the root account immediately.

---

# Alternative Tool

> **💡 Tip**
>
> Use **AWS CloudShell** (available from the top navigation bar in the AWS Management Console) as an instant browser-based terminal. It allows you to run AWS CLI commands without installing the CLI locally or connecting through SSH.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Launched an Ubuntu EC2 instance.
* ✅ Explored the AWS Management Console.
* ✅ Installed the AWS CLI.
* ✅ Configured AWS CLI credentials.
* ✅ Verified AWS CLI connectivity using Amazon EC2 commands.
