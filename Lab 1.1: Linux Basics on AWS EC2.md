# Lab 1.1: Linux Basics on AWS EC2

## Overview

This lab introduces the fundamentals of working with Linux on an AWS EC2 instance. You will learn how to launch an Ubuntu virtual machine, connect securely using SSH, execute essential Linux commands, and understand the Linux file system hierarchy.

## Learning Objectives

By the end of this lab, you will be able to:

* Launch an Ubuntu EC2 instance on AWS.
* Connect to the instance using SSH from a terminal.
* Practice essential Linux commands.
* Understand the Linux file system hierarchy.

---

# Task 1: Launch Ubuntu EC2 Instance on AWS

## Objective

Create and configure an Ubuntu EC2 instance using the AWS Management Console.

### Steps

1. Open the AWS Console.
2. Navigate to:

```text
AWS Console → EC2 → Launch Instance
```

3. Configure the instance with the following settings:

| Setting            | Value                                               |
| ------------------ | --------------------------------------------------- |
| **Name**           | `devops-linux-lab`                                  |
| **AMI**            | Ubuntu Server 22.04 LTS (Free tier eligible)        |
| **Instance Type**  | `t2.micro`                                          |
| **Key Pair**       | Create new → `devops-key` → RSA → `.pem` → Download |
| **Security Group** | Allow SSH (22) from My IP                           |
|                    | Allow HTTP (80) from Anywhere                       |

4. Launch the instance.

5. Wait until the instance reaches the **Running** state.

> **Important**
>
> Download and securely store the `.pem` key file. It cannot be downloaded again after creation.

---

# Task 2: Connect via SSH & Run Linux Commands

## Objective

Connect to the EC2 instance and practice common Linux administration commands.

### Set Key Permissions (Linux/macOS)

```bash
chmod 400 devops-key.pem
```

### Connect to the EC2 Instance

```bash
ssh -i devops-key.pem ubuntu@<PUBLIC_IP>
```

### Verify the Connection

```bash
whoami && uname -a && uptime
```

---

# Essential Linux Commands to Practice

## Navigation

```bash
pwd && ls -la && cd /var/log && cd ~ && cd ..
```

---

## File Operations

```bash
mkdir devops_practice && cd devops_practice

touch script.sh README.md

echo 'Hello DevOps' > notes.txt

cat notes.txt && head -5 /etc/passwd && tail -5 /etc/passwd
```

---

## Copy, Move, and Delete Files

```bash
cp notes.txt notes_backup.txt

mv notes.txt greetings.txt

rm notes_backup.txt && rm -rf old_folder
```

---

## System Information

```bash
df -h && free -m && top && ps aux | grep ssh

cat /etc/os-release && hostname -I
```

---

## Search and Filter

```bash
grep 'root' /etc/passwd

find /home -name '*.txt'

ls -la | grep '.sh'
```

---

## Networking

```bash
ping -c 3 google.com

curl http://checkip.amazonaws.com

netstat -tlnp 2>/dev/null || ss -tlnp
```

---

# Best Practice

> **✅ Best Practice**
>
> Always SSH with a specific user (`ubuntu`, `ec2-user`)—never log in as `root` directly on production servers. Use `sudo` for elevated commands.

---

# Alternative Tool

> **💡 Tip**
>
> Instead of PuTTY on Windows, use **Windows Terminal + OpenSSH** (built in since Windows 10) or **MobaXterm** for a richer experience.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Launched an Ubuntu EC2 instance on AWS.
* ✅ Connected securely using SSH.
* ✅ Practiced essential Linux commands.
* ✅ Explored the Linux file system.
* ✅ Retrieved system and network information.
* ✅ Applied Linux administration best practices.

 
