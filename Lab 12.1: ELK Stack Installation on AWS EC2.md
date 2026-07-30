# Lab 12.1: ELK Stack Installation on AWS EC2

## Overview

This lab introduces centralized log monitoring using the **ELK Stack**:

* **Elasticsearch** — Stores and searches log data.
* **Logstash** — Processes, filters, transforms, and forwards logs.
* **Kibana** — Provides visualization dashboards for Elasticsearch data.
* **Filebeat** — Lightweight log shipper installed on servers to forward logs.

You will deploy an ELK monitoring server on AWS EC2, configure Elasticsearch, Kibana, and Logstash, and prepare the environment for centralized application monitoring.

---

# ELK Stack Architecture

```text
Application Servers
        |
        |
    Filebeat
        |
        | Port: 5044
        |
    Logstash
        |
        | Process / Filter / Transform
        |
 Elasticsearch
        |
        |
     Kibana
        |
        |
 Monitoring Dashboard
```

---

# ELK Components Reference

| Component     | Role                                                | Port           |
| ------------- | --------------------------------------------------- | -------------- |
| Filebeat      | Lightweight log shipper (runs on each server)       | N/A (outbound) |
| Logstash      | Log processor, enricher, parser, and transformer    | 5044           |
| Elasticsearch | Search and analytics database for storing logs      | 9200, 9300     |
| Kibana        | Visualization dashboard interface for Elasticsearch | 5601           |

---

# Lab Information

| Parameter        | Value         |
| ---------------- | ------------- |
| Duration         | ⏱️ 90 minutes |
| Difficulty       | 🔴 Advanced   |
| Platform         | AWS EC2       |
| Operating System | Ubuntu 22.04  |
| Monitoring Stack | ELK 8.x       |

---

# Task 1: Launch EC2 Instance for ELK

## Objective

Create an EC2 instance to host the ELK monitoring stack.

---

## EC2 Configuration

Launch an EC2 instance with:

```text
Instance Name:
elk-server

Operating System:
Ubuntu 22.04

Instance Type:
t2.medium minimum

Storage:
20GB
```

---

## Security Group Configuration

Allow the following ports:

| Service       | Port | Purpose               |
| ------------- | ---- | --------------------- |
| SSH           | 22   | Server administration |
| Elasticsearch | 9200 | HTTP API access       |
| Elasticsearch | 9300 | Cluster communication |
| Kibana        | 5601 | Dashboard access      |
| Logstash      | 5044 | Beats input           |
| Logstash      | 5000 | Syslog input          |

---

## Connect to EC2

```bash
ssh -i devops-key.pem ubuntu@<ELK_IP>
```

---

# Step 2: Install Java

## Objective

Install Java required by Elasticsearch and Logstash.

Update packages:

```bash
sudo apt update
```

Install Java:

```bash
sudo apt install openjdk-17-jdk -y
```

Verify:

```bash
java -version
```

Expected:

```text
openjdk version "17.x.x"
```

---

# Step 3: Add Elastic APT Repository

Import Elastic GPG key:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch \
| sudo gpg --dearmor -o /usr/share/keyrings/elastic-keyring.gpg
```

Add repository:

```bash
echo 'deb [signed-by=/usr/share/keyrings/elastic-keyring.gpg] \
https://artifacts.elastic.co/packages/8.x/apt stable main' \
| sudo tee /etc/apt/sources.list.d/elastic-8.x.list
```

Update package list:

```bash
sudo apt update
```

---

# Task 2: Install & Configure Elasticsearch

## Objective

Install Elasticsearch as the centralized log storage and search engine.

---

## Install Elasticsearch

```bash
sudo apt install elasticsearch -y
```

---

## Configure Elasticsearch

Edit configuration:

```bash
sudo nano /etc/elasticsearch/elasticsearch.yml
```

Update:

```yaml
network.host: 0.0.0.0

http.port: 9200

discovery.type: single-node

xpack.security.enabled: false
```

> ⚠️ Security is disabled only for this lab environment.

---

## Start Elasticsearch

Enable service:

```bash
sudo systemctl enable elasticsearch
```

Start service:

```bash
sudo systemctl start elasticsearch
```

Check status:

```bash
sudo systemctl status elasticsearch
```

---

## Test Elasticsearch

Wait approximately 30 seconds after startup.

Run:

```bash
curl http://localhost:9200
```

Expected response:

```json
{
  "cluster_name": "...",
  "version": {
    "number": "8.x.x"
  }
}
```

---

# Task 3: Install & Configure Kibana

## Objective

Install Kibana to visualize Elasticsearch data through dashboards.

---

## Install Kibana

```bash
sudo apt install kibana -y
```

---

## Configure Kibana

Edit:

```bash
sudo nano /etc/kibana/kibana.yml
```

Set:

```yaml
server.host: '0.0.0.0'

server.port: 5601

elasticsearch.hosts: ['http://localhost:9200']
```

---

## Start Kibana

Enable:

```bash
sudo systemctl enable kibana
```

Start:

```bash
sudo systemctl start kibana
```

---

## Access Kibana Dashboard

Open:

```text
http://<ELK_IP>:5601
```

---

## First-Time Access

For this lab:

* Wait approximately 2–3 minutes for Kibana startup.
* No credentials are required.
* Elasticsearch security is disabled.

---

# Task 4: Install & Configure Logstash

## Objective

Configure Logstash to receive, process, and forward logs to Elasticsearch.

---

## Install Logstash

```bash
sudo apt install logstash -y
```

---

# Create Logstash Pipeline Configuration

Create configuration file:

```bash
sudo nano /etc/logstash/conf.d/apache-logs.conf
```

Add:

```conf
input {

  beats {
    port => 5044
  }


  syslog {
    port => 5000
    type => 'syslog'
  }

}


filter {

  if [type] == 'apache' {

    grok {

      match => {
        'message' => '%{COMBINEDAPACHELOG}'
      }

    }


    date {

      match => [
        'timestamp',
        'dd/MMM/yyyy:HH:mm:ss Z'
      ]

    }


    geoip {

      source => 'clientip'

    }

  }


  mutate {

    add_field => {
      'environment' => 'production'
    }

  }

}


output {

  elasticsearch {

    hosts => [
      'http://localhost:9200'
    ]

    index => 'devops-logs-%{+YYYY.MM.dd}'

  }


  stdout {

    codec => rubydebug

  }

}
```

---

# Start Logstash Service

Enable:

```bash
sudo systemctl enable logstash
```

Start:

```bash
sudo systemctl start logstash
```

Verify:

```bash
sudo systemctl status logstash
```

---

# ELK Data Flow

```text
Server Logs
     |
     |
 Filebeat
     |
     |
 Logstash
     |
     |
 Elasticsearch
     |
     |
 Kibana Dashboard
```

---

# Best Practices

> **✅ Best Practice**
>
> Deploy ELK components on appropriately sized instances. Elasticsearch requires sufficient memory and CPU resources for production workloads.

---

> **✅ Best Practice**
>
> Enable Elasticsearch security features in production environments. This lab disables security only for learning purposes.

---

> **✅ Best Practice**
>
> Use Filebeat instead of directly sending logs from applications. Filebeat provides lightweight, reliable log forwarding.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Created an AWS EC2 instance for ELK monitoring.
* ✅ Installed Java dependencies.
* ✅ Added Elastic package repository.
* ✅ Installed and configured Elasticsearch.
* ✅ Installed and configured Kibana dashboard.
* ✅ Installed and configured Logstash pipelines.
* ✅ Prepared the ELK environment for centralized log collection.
