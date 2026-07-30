# Lab 12.2: Log Shipping with Filebeat

## Overview

This lab demonstrates how to configure **Filebeat** as a lightweight log shipper on an application server. Filebeat collects application and system logs, forwards them to Logstash, and enables centralized monitoring through the ELK Stack.

In this lab, you will configure Filebeat to:

* Collect Nginx access and error logs.
* Collect system logs.
* Add metadata fields to logs.
* Send logs securely to Logstash.
* Validate Filebeat configuration and connectivity.

---

# Learning Objectives

By the end of this lab, you will be able to:

* Install Filebeat on an application server.
* Configure Filebeat log inputs.
* Forward logs to Logstash.
* Validate Filebeat configuration.
* Verify communication between application servers and the ELK stack.

---

# Filebeat Architecture

```text
Application Server

     Nginx Logs
          |
          |
      Filebeat
          |
          | Port 5044
          |
      Logstash
          |
          |
 Elasticsearch
          |
          |
       Kibana
```

---

# Task 1: Install Filebeat on Application Server

## Objective

Install and configure Filebeat on the application server to collect logs and forward them to the ELK monitoring server.

> 📌 **Note**
>
> Run these steps on your **APP SERVER**, not on the ELK server.

---

# Step 1: Install Filebeat

Install Filebeat:

```bash
sudo apt install filebeat -y
```

---

# Step 2: Configure Filebeat

Open the Filebeat configuration file:

```bash
sudo nano /etc/filebeat/filebeat.yml
```

---

# Step 3: Configure Filebeat Inputs

Add the following configuration:

```yaml
filebeat.inputs:

- type: log

  enabled: true

  paths:
    - /var/log/nginx/access.log
    - /var/log/nginx/error.log

  fields:
    service: nginx
    environment: production


- type: log

  enabled: true

  paths:
    - /var/log/syslog

  fields:
    service: system
```

---

# Configuration Explanation

| Configuration   | Purpose                           |
| --------------- | --------------------------------- |
| `type: log`     | Defines log input type            |
| `enabled: true` | Enables log collection            |
| `paths`         | Specifies files to monitor        |
| `fields`        | Adds metadata to collected logs   |
| `service`       | Identifies application source     |
| `environment`   | Identifies deployment environment |

---

# Step 4: Configure Logstash Output

Configure Filebeat to send logs to Logstash:

```yaml
output.logstash:

  hosts:
    - '<ELK_PRIVATE_IP>:5044'
```

Replace:

```text
<ELK_PRIVATE_IP>
```

with the private IP address of your ELK server.

---

# Disable Direct Elasticsearch Output

Ensure Elasticsearch output is disabled:

```yaml
#output.elasticsearch:
```

Filebeat flow should be:

```text
Filebeat
    |
    |
 Logstash
    |
    |
Elasticsearch
```

---

# Step 5: Enable and Start Filebeat Service

Enable Filebeat:

```bash
sudo systemctl enable filebeat
```

Start Filebeat:

```bash
sudo systemctl start filebeat
```

Check service status:

```bash
sudo systemctl status filebeat
```

Expected:

```text
Active: active (running)
```

---

# Step 6: Validate Filebeat Configuration

## Test Configuration Syntax

Run:

```bash
sudo filebeat test config
```

Expected:

```text
Config OK
```

---

## Test Output Connection

Run:

```bash
sudo filebeat test output
```

Expected:

```text
logstash: <ELK_PRIVATE_IP>:5044
connection...
OK
```

---

# Log Shipping Workflow

```text
        Application Server

        /var/log/nginx/
              |
              |
          Filebeat
              |
              |
        Logstash :5044
              |
              |
      Elasticsearch :9200
              |
              |
          Kibana :5601
```

---

# Best Practices

> **✅ Best Practice**
>
> Install Filebeat on every application server that generates logs. Avoid manually copying logs to the monitoring server.

---

> **✅ Best Practice**
>
> Use metadata fields such as `service`, `environment`, and `application` to make searching and filtering easier in Kibana.

---

> **✅ Best Practice**
>
> Always validate Filebeat configuration before restarting the service:
>
> ```bash
> filebeat test config
> filebeat test output
> ```

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Installed Filebeat on an application server.
* ✅ Configured Nginx and system log collection.
* ✅ Added log metadata fields.
* ✅ Connected Filebeat to Logstash.
* ✅ Verified Filebeat configuration and output connectivity.
* ✅ Prepared centralized log shipping for ELK monitoring.
