# Lab 12.3: Kibana Dashboards & Visualizations

## Overview

This lab demonstrates how to use **Kibana** to analyze, visualize, and monitor centralized logs collected through the ELK Stack.

You will create:

* Elasticsearch index patterns.
* Real-time log searches.
* Log-based visualizations.
* Monitoring dashboards.
* Alerting rules for critical events.

---

# Learning Objectives

By the end of this lab, you will be able to:

* Access and navigate the Kibana interface.
* Create Elasticsearch index patterns.
* Search and filter application logs.
* Build monitoring visualizations.
* Create operational dashboards.
* Configure alerts based on Elasticsearch data.

---

# Task 1: Access Kibana Dashboard

## Open Kibana

Navigate to:

```text id="4kv9na"
http://<ELK_IP>:5601
```

Replace:

```text id="r5r8hk"
<ELK_IP>
```

with the public IP address of your ELK server.

---

# Task 2: Create Elasticsearch Index Pattern

## Objective

Create an index pattern so Kibana can access logs stored in Elasticsearch.

---

## Steps

Navigate:

```text id="k5v2pm"
Stack Management
        ↓
Index Patterns
        ↓
Create index pattern
```

Configure:

```text id="x6m1se"
Index Pattern:

devops-logs-*
```

Select time field:

```text id="m0fr7h"
Time field:

@timestamp
```

Click:

```text id="o4r2qq"
Create index pattern
```

---

# Index Pattern Explanation

| Field           | Purpose                                   |
| --------------- | ----------------------------------------- |
| `devops-logs-*` | Matches all daily log indices             |
| `@timestamp`    | Enables time-based filtering              |
| Index Pattern   | Allows Kibana to query Elasticsearch data |

---

# Task 3: Explore Logs Using Discover

## Objective

View and filter ingested logs in real time.

---

## Open Discover

Navigate:

```text id="z8g6hd"
Discover
        ↓
Select:
devops-logs-*
```

---

## View Logs

You should see:

* Nginx access logs.
* Nginx error logs.
* System logs.
* Metadata fields.

---

## Filter Logs

Example filter:

```text id="s7q2yk"
service: nginx
```

This displays only Nginx-related logs.

---

## Save Search

Save the search as:

```text id="0qv3b8"
Nginx Access Logs
```

---

# Task 4: Create Kibana Visualizations

## Objective

Create visual representations of application and infrastructure logs.

---

Navigate:

```text id="c5n8pt"
Visualize Library
        ↓
Create Visualization
```

---

# Visualization 1: Top IP Addresses

## Type

```text
Bar Chart
```

Configuration:

```text id="4w9c9f"
Aggregation:

Terms

Field:

clientip

Size:

10
```

Purpose:

Displays the top 10 client IP addresses generating requests.

---

# Visualization 2: Requests Over Time

## Type

```text
Line Chart
```

Configuration:

```text id="1g5tkw"
X-Axis:

Date Histogram

Field:

@timestamp
```

Purpose:

Shows traffic trends over time.

---

# Visualization 3: HTTP Status Code Distribution

## Type

```text
Pie Chart
```

Configuration:

```text id="7q4j2m"
Aggregation:

Terms

Field:

response status code
```

Purpose:

Displays distribution of:

* 200 responses.
* 404 errors.
* 500 errors.

---

# Visualization 4: Slowest Requests

## Type

```text
Data Table
```

Configuration:

Display:

* Request URL.
* Response time.
* Client IP.
* HTTP status.

Purpose:

Identify slow application requests.

---

# Task 5: Build Monitoring Dashboard

## Objective

Combine multiple visualizations into a single operational dashboard.

---

## Create Dashboard

Navigate:

```text id="5z7r7x"
Dashboard
        ↓
Create New Dashboard
```

---

## Add Visualizations

Add:

1. Top 10 IP Addresses.
2. Requests Over Time.
3. HTTP Status Code Distribution.
4. Slowest Requests.

---

## Dashboard Title

Set:

```text id="0k4t3n"
DevOps Nginx Monitoring Dashboard
```

Save dashboard.

---

# Dashboard Layout Example

```text id="s5x3p0"
+--------------------------------+
| Top Client IP Addresses        |
+--------------------------------+

+--------------------------------+
| Requests Over Time             |
+--------------------------------+

+--------------------------------+
| HTTP Status Distribution       |
+--------------------------------+

+--------------------------------+
| Slowest Requests               |
+--------------------------------+
```

---

# Task 6: Configure Kibana Alerts

## Objective

Create automated notifications for critical application issues.

---

Navigate:

```text id="4f6k1a"
Stack Management
        ↓
Rules
        ↓
Create Rule
```

---

## Create Elasticsearch Query Rule

Condition:

```text id="6x5xwx"
Count of HTTP 5xx errors > 10
within 5 minutes
```

---

## Configure Action

Notification methods:

```text id="p5g7wy"
Email

or

Webhook
```

---

# Monitoring Alert Flow

```text id="4r7t4v"
Application Error

      |
      |

Elasticsearch Index

      |
      |

Kibana Rule

      |
      |

Email / Webhook Alert
```

---

# Best Practices

> **✅ Best Practice**
>
> Use **Index Lifecycle Management (ILM)** to automatically remove old Elasticsearch indices.
>
> Without ILM, logs continue growing and may eventually consume all available disk space.

---

> **✅ Best Practice**
>
> Ship structured JSON logs from applications.
>
> Structured logs make searching and analysis easier. Include fields such as:
>
> * `service`
> * `environment`
> * `version`
> * `timestamp`

---

> **✅ Best Practice**
>
> Create dashboards based on operational requirements:
>
> * Application health.
> * Error rates.
> * Traffic patterns.
> * Performance metrics.

---

# Alternative Monitoring Tools

| Tool                | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| Grafana + Loki      | Lightweight log monitoring, commonly used with Kubernetes    |
| Splunk              | Enterprise-grade monitoring and analytics platform           |
| AWS CloudWatch Logs | Native AWS logging solution                                  |
| Datadog             | SaaS-based monitoring platform                               |
| EFK Stack           | Elasticsearch + Fluentd + Kibana for Kubernetes environments |

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Created Kibana index patterns.
* ✅ Viewed centralized logs using Discover.
* ✅ Filtered application-specific logs.
* ✅ Created log visualizations.
* ✅ Built a DevOps monitoring dashboard.
* ✅ Configured alerting for application errors.
* ✅ Reviewed ELK alternatives and monitoring best practices.
