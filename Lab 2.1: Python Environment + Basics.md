# Lab 2.1: Python Environment + Basics

## Overview

This lab introduces Python as the primary scripting language for DevOps automation. You will install Python, create a virtual environment, install commonly used packages, and build a basic automation script that demonstrates core Python concepts.

## Learning Objectives

By the end of this lab, you will be able to:

* Install Python 3 and related tools on Ubuntu.
* Create and use a Python virtual environment.
* Install common Python packages for DevOps.
* Understand Python variables, data types, control flow, and functions.
* Implement exception handling.
* Read and write JSON files using Python.

---

# Task 1: Install Python 3 on EC2

## Objective

Install Python 3 and the required development tools on an Ubuntu EC2 instance.

### Install Python

```bash
sudo apt update

sudo apt install python3 python3-pip python3-venv -y

python3 --version && pip3 --version
```

### Create a Virtual Environment (Best Practice)

```bash
mkdir python-devops && cd python-devops

python3 -m venv venv

source venv/bin/activate
```

The virtual environment prompt appears:

```text
(venv)
```

### Install Common Packages

```bash
pip install boto3 requests paramiko flask
```

> **✅ Best Practice**
>
> Always use a Python virtual environment (`venv`) to isolate project dependencies and avoid conflicts with system-wide packages.

---

# Task 2: Python Basics for DevOps

## Objective

Create a Python script that demonstrates fundamental programming concepts commonly used in DevOps automation.

### Create the Python Script

Create a file named **`devops_basics.py`**.

```python
#!/usr/bin/env python3

import os, subprocess, json

# Variables & data types
server_name = 'prod-web-01'
port = 8080
services = ['nginx', 'mysql', 'redis']
config = {'env': 'production', 'region': 'ap-south-1'}

# Control flow
for service in services:
    print(f'Checking service: {service}')

# Functions
def check_disk_usage(threshold=80):
    result = subprocess.run(['df', '-h', '/'], capture_output=True, text=True)
    lines = result.stdout.strip().split('\n')

    for line in lines[1:]:
        parts = line.split()
        usage = int(parts[4].replace('%', ''))

        if usage > threshold:
            print(f'⚠️ Disk usage {usage}% exceeds threshold {threshold}%')
            return False

    print(f'✅ Disk usage OK')
    return True

# Exception handling
try:
    check_disk_usage(80)
except Exception as e:
    print(f'Error: {e}')

# File I/O
with open('servers.txt', 'w') as f:
    json.dump({'servers': services}, f, indent=2)

with open('servers.txt', 'r') as f:
    data = json.load(f)

print(data)
```

> **ℹ️ Note**
>
> The code above has been formatted with proper Python indentation for readability while preserving its original logic and functionality.

---

# Best Practices

> **✅ Best Practice**
>
> Use virtual environments for every Python project to ensure dependency isolation and reproducible environments.

> **✅ Best Practice**
>
> Store configuration values in variables or configuration files instead of hardcoding them throughout your scripts.

> **✅ Best Practice**
>
> Use exception handling (`try`/`except`) to prevent automation scripts from terminating unexpectedly during runtime.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Installed Python 3 and development tools.
* ✅ Created and activated a Python virtual environment.
* ✅ Installed commonly used DevOps Python packages.
* ✅ Created a Python automation script.
* ✅ Worked with variables, loops, functions, and exception handling.
* ✅ Read and wrote JSON data using Python.
