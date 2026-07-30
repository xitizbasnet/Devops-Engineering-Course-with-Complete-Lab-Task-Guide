# Lab 8.1: Ansible Setup & Ad-hoc Commands

## Overview

This lab introduces Ansible configuration management using an agentless architecture. You will configure an Ansible Control Node, connect to managed nodes using SSH, create an inventory file, test connectivity, and execute ad-hoc commands for common system administration tasks.

## Learning Objectives

By the end of this lab, you will be able to:

* Install and configure Ansible on an Ubuntu control node.
* Understand Ansible's agentless architecture.
* Create and manage Ansible inventory files.
* Test SSH connectivity between control and managed nodes.
* Execute Ansible ad-hoc commands.
* Perform basic server administration tasks using Ansible modules.

---

# Task 1: Install Ansible on Control Node

## Objective

Install Ansible on an Ubuntu EC2 instance that will act as the Control Node.

---

## Step 1: Update System Packages

On the **Control EC2 instance**:

```bash id="v7m3px"
sudo apt update
```

---

## Step 2: Install Required Packages

```bash id="r5q8kc"
sudo apt install -y software-properties-common
```

---

## Step 3: Add Ansible Repository

```bash id="n4w9zs"
sudo add-apt-repository --yes --update ppa:ansible/ansible
```

---

## Step 4: Install Ansible

```bash id="p2x6mv"
sudo apt install -y ansible
```

Verify installation:

```bash id="h8k4qw"
ansible --version
```

---

# Ansible Architecture

Ansible uses an **agentless architecture**.

```text
Control Node
     |
     | SSH
     |
Managed Nodes
(No agent installation required)
```

> **ℹ️ Key Advantage**
>
> Unlike Puppet, Ansible does not require agents to be installed on managed nodes. It uses SSH communication to execute tasks remotely.

---

# Task 2: Create Inventory File

## Objective

Create an Ansible inventory file that defines managed servers and connection details.

---

## Step 1: Copy SSH Key to Control Node

Copy the SSH key:

```bash id="w6p3rm"
scp -i devops-key.pem devops-key.pem ubuntu@<CONTROL_IP>:~/.ssh/
```

Set secure permissions:

```bash id="c8m5qx"
chmod 400 ~/.ssh/devops-key.pem
```

---

## Step 2: Create Ansible Lab Directory

```bash id="k9v4zn"
mkdir ansible-labs && cd ansible-labs
```

Create inventory file:

```bash id="s3q7wx"
nano inventory.ini
```

---

## Step 3: Configure Inventory

Add the following content:

```ini id="m5z8qr"
[webservers]
web1 ansible_host=<NODE1_PRIVATE_IP>
web2 ansible_host=<NODE2_PRIVATE_IP>

[dbservers]
db1 ansible_host=<NODE3_PRIVATE_IP>

[all:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/devops-key.pem
ansible_python_interpreter=/usr/bin/python3
```

---

## Inventory Structure

| Group        | Servers                     |
| ------------ | --------------------------- |
| `webservers` | Web application servers     |
| `dbservers`  | Database servers            |
| `all:vars`   | Common connection variables |

---

## Step 4: Test Connectivity

Run the Ansible ping module:

```bash id="x2n7cp"
ansible -i inventory.ini all -m ping
```

Expected result:

```text id="y5q8mv"
web1 → SUCCESS → pong

web2 → SUCCESS → pong

db1 → SUCCESS → pong
```

---

# Task 3: Execute Ansible Ad-hoc Commands

## Objective

Use Ansible modules to perform common administration tasks without writing playbooks.

---

# Check Server Uptime

```bash id="q7m4xs"
ansible -i inventory.ini all -m command -a 'uptime'
```

---

# Check Disk Space

```bash id="f9w2pk"
ansible -i inventory.ini all -m shell -a 'df -h | grep /dev/root'
```

---

# Install Nginx on Web Servers

```bash id="z6r3mv"
ansible -i inventory.ini webservers -m apt \
-a 'name=nginx state=present update_cache=yes' \
--become
```

---

# Start Nginx Service

```bash id="n8x5qd"
ansible -i inventory.ini webservers -m service \
-a 'name=nginx state=started enabled=yes' \
--become
```

---

# Copy a File to Managed Nodes

```bash id="w4m9kc"
ansible -i inventory.ini all -m copy \
-a 'src=/etc/hosts dest=/tmp/hosts'
```

---

# Gather System Facts

Collect system information:

```bash id="p7q2vz"
ansible -i inventory.ini web1 -m setup | grep -E \
'ansible_distribution|ansible_memtotal'
```

---

# Create Directory on Managed Nodes

Create a directory:

```bash id="d3x8mr"
ansible -i inventory.ini all -m file \
-a 'path=/opt/devops state=directory mode=0755' \
--become
```

---

# Best Practices

> **✅ Best Practice**
>
> Use SSH keys instead of passwords for Ansible authentication. Secure private keys with appropriate permissions such as `chmod 400`.

> **✅ Best Practice**
>
> Organize servers into inventory groups such as web servers, database servers, and application servers for easier management.

> **✅ Best Practice**
>
> Use Ansible modules instead of raw shell commands whenever possible. Modules provide better idempotency and error handling.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Installed Ansible on a Control Node.
* ✅ Configured SSH-based communication with managed nodes.
* ✅ Created an Ansible inventory file.
* ✅ Tested managed node connectivity.
* ✅ Executed Ansible ad-hoc commands.
* ✅ Managed packages, services, files, and system information remotely.
