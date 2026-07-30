# Lab 2.2: Python Automation Scripts for AWS & DevOps

## Overview

This lab demonstrates how Python can be used to automate AWS infrastructure and perform server health monitoring. You will use the **Boto3** SDK to interact with Amazon EC2 and apply Object-Oriented Programming (OOP) concepts to build a reusable server monitoring tool.

## Learning Objectives

By the end of this lab, you will be able to:

* Install and configure the AWS SDK for Python (Boto3).
* Configure AWS credentials for programmatic access.
* Automate Amazon EC2 operations using Python.
* Build reusable automation scripts using Object-Oriented Programming (OOP).
* Monitor server health by checking disk and memory utilization.
* Generate simple health reports for Linux servers.

---

# Task 1: Automate EC2 Operations with Boto3

## Objective

Install Boto3, configure AWS credentials, and create a Python script to manage EC2 instances.

### Install Boto3

```bash
pip install boto3
```

### Configure AWS Credentials

Configure AWS credentials on the EC2 instance (or use an IAM role).

```bash
aws configure
```

### Create the Automation Script

Create a file named **`ec2_automation.py`**.

```python
import boto3

ec2 = boto3.client('ec2', region_name='ap-south-1')

def list_running_instances():
    response = ec2.describe_instances(
        Filters=[
            {
                'Name': 'instance-state-name',
                'Values': ['running']
            }
        ]
    )

    for r in response['Reservations']:
        for i in r['Instances']:
            name = next(
                (
                    t['Value']
                    for t in i.get('Tags', [])
                    if t['Key'] == 'Name'
                ),
                'Unnamed'
            )

            print(f'ID: {i["InstanceId"]} | Type: {i["InstanceType"]} | Name: {name}')

def stop_instances_by_tag(tag_key, tag_value):
    response = ec2.describe_instances(
        Filters=[
            {
                'Name': f'tag:{tag_key}',
                'Values': [tag_value]
            },
            {
                'Name': 'instance-state-name',
                'Values': ['running']
            }
        ]
    )

    ids = [
        i['InstanceId']
        for r in response['Reservations']
        for i in r['Instances']
    ]

    if ids:
        ec2.stop_instances(InstanceIds=ids)
        print(f'Stopped: {ids}')
    else:
        print('No matching running instances')

print('Running EC2 Instances:')
list_running_instances()

# Tag your EC2 with AutoStop=true first, then:
# stop_instances_by_tag('AutoStop', 'true')
```

> **ℹ️ Note**
>
> The script has been formatted with proper Python indentation while preserving its original logic.

---

# Task 2: OOP-based Server Health Monitor

## Objective

Create a reusable Python class that performs server health checks and generates a health report.

### Create the Monitoring Script

Create a file named **`server_monitor.py`**.

```python
import subprocess
import os
from datetime import datetime

class ServerMonitor:

    def __init__(self, name):
        self.name = name
        self.checks = []

    def check_disk(self, threshold=80):
        result = subprocess.run(
            ['df', '-h', '/'],
            capture_output=True,
            text=True
        )

        line = result.stdout.strip().split('\n')[1]
        usage = int(line.split()[4].replace('%', ''))

        status = 'OK' if usage < threshold else 'WARN'

        self.checks.append({
            'metric': 'disk',
            'value': usage,
            'status': status
        })

        return status

    def check_memory(self):
        with open('/proc/meminfo') as f:
            data = {
                l.split(':')[0]: int(l.split(':')[1].strip().split()[0])
                for l in f
            }

        used_pct = 100 - (data['MemAvailable'] / data['MemTotal'] * 100)

        status = 'OK' if used_pct < 85 else 'WARN'

        self.checks.append({
            'metric': 'memory',
            'value': round(used_pct, 1),
            'status': status
        })

        return status

    def report(self):
        print(
            f'\n=== Health Report: {self.name} @ '
            f'{datetime.now().strftime("%Y-%m-%d %H:%M")} ==='
        )

        for c in self.checks:
            icon = '✅' if c['status'] == 'OK' else '⚠️'
            print(
                f'{icon} {c["metric"].upper()}: '
                f'{c["value"]}% [{c["status"]}]'
            )

monitor = ServerMonitor('prod-web-01')

monitor.check_disk()
monitor.check_memory()
monitor.report()
```

### Run the Script

```bash
python3 server_monitor.py
```

> **ℹ️ Note**
>
> The code has been formatted with proper Python syntax and indentation for readability while preserving its original functionality.

---

# Best Practices

> **✅ Best Practice**
>
> Always use virtual environments (`venv`) for Python projects to isolate dependencies. Never install packages globally with `pip` on production systems.

---

# Alternative Tool

> **💡 Tip**
>
> **Visual Studio Code** with the **Remote - SSH** extension is an excellent environment for developing Python scripts directly on an EC2 instance. Install the **Remote - SSH** extension to edit, run, and debug code without transferring files manually.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Installed and configured the AWS SDK for Python (Boto3).
* ✅ Configured AWS credentials for automation.
* ✅ Created a Python script to manage Amazon EC2 instances.
* ✅ Built an Object-Oriented server monitoring application.
* ✅ Monitored disk and memory utilization.
* ✅ Generated a basic server health report using Python.
