# Lab 7.1: Puppet Master-Agent Setup on AWS EC2

## Overview

This lab introduces Puppet configuration management using a master-agent architecture on AWS EC2. You will create two EC2 instances, configure one as a Puppet Master and another as a Puppet Agent, establish secure certificate-based trust, and prepare the environment for centralized configuration management.

## Learning Objectives

By the end of this lab, you will be able to:

* Deploy Puppet Master and Puppet Agent nodes on AWS EC2.
* Configure network communication between Puppet nodes.
* Install Puppet Server and Puppet Agent.
* Configure Puppet agent communication with the master.
* Generate and sign Puppet certificates.
* Establish trusted communication between Puppet nodes.

---

# Task 1: Launch 2 EC2 Instances

## Objective

Create two Ubuntu EC2 instances to build a Puppet master-agent environment.

Launch two Ubuntu 22.04 EC2 instances:

```text
EC2 → Launch 2 Ubuntu 22.04 t2.micro instances
```

## Instance Configuration

| Instance        | Role          | Recommended Type                               |
| --------------- | ------------- | ---------------------------------------------- |
| `puppet-master` | Puppet Master | `t2.medium` recommended — Puppet needs 2GB RAM |
| `puppet-agent`  | Puppet Agent  | `t2.micro`                                     |

Configure both instances:

* Same VPC.
* Same Security Group.
* Security Group Rules:

  * SSH (22)
  * Puppet communication port (8140) from each other.

---

## Configure Hostnames

### On Puppet Master

```bash
sudo hostnamectl set-hostname puppet-master.devops.local
```

---

### On Puppet Agent

```bash
sudo hostnamectl set-hostname puppet-agent.devops.local
```

---

# Task 2: Edit `/etc/hosts` on Both Servers

## Objective

Configure hostname resolution between Puppet Master and Puppet Agent nodes.

On **BOTH servers**, edit the hosts file:

```bash
sudo nano /etc/hosts
```

Add the following entries:

```text
<MASTER_PRIVATE_IP> puppet-master.devops.local puppet

<AGENT_PRIVATE_IP> puppet-agent.devops.local
```

---

## Verify Connectivity

From both servers:

```bash
ping puppet-master.devops.local -c 2
```

---

# Task 3: Install Puppet Server on Master

## Objective

Install and configure Puppet Server on the master node.

Perform the following steps on the **MASTER** server.

---

## Add Puppet Repository

```bash
wget https://apt.puppet.com/puppet8-release-jammy.deb

sudo dpkg -i puppet8-release-jammy.deb
```

Update package information:

```bash
sudo apt update
```

Install Puppet Server:

```bash
sudo apt install -y puppetserver
```

---

## Configure Puppet Server Memory

Reduce memory allocation for `t2.medium` instances:

```bash
sudo nano /etc/default/puppetserver
```

Find:

```text
JAVA_ARGS="-Xms2g -Xmx2g"
```

Change to:

```text
JAVA_ARGS="-Xms512m -Xmx512m"
```

---

## Enable and Start Puppet Server

```bash
sudo systemctl enable puppetserver

sudo systemctl start puppetserver

sudo systemctl status puppetserver
```

---

## Verify Puppet Server Installation

```bash
/opt/puppetlabs/bin/puppetserver --version
```

---

# Task 4: Install Puppet Agent on Agent Node

## Objective

Install and configure Puppet Agent to communicate with the Puppet Master.

Perform the following steps on the **AGENT** server.

---

## Add Puppet Repository

```bash
wget https://apt.puppet.com/puppet8-release-jammy.deb

sudo dpkg -i puppet8-release-jammy.deb
```

Update packages and install Puppet Agent:

```bash
sudo apt update && sudo apt install -y puppet-agent
```

---

## Configure Puppet Master Connection

Edit the Puppet configuration file:

```bash
sudo nano /etc/puppetlabs/puppet/puppet.conf
```

Add the following under the `[main]` section:

```ini
[main]
server = puppet-master.devops.local
```

---

## Start Puppet Agent and Send Certificate Request

```bash
sudo /opt/puppetlabs/bin/puppet agent --test --waitforcert 60
```

The agent sends a Certificate Signing Request (CSR) to the Puppet Master.

---

# Task 5: Sign Certificate on Master

## Objective

Approve the Puppet Agent certificate request and establish trusted communication.

Perform the following steps on the **MASTER** server.

---

## List Pending Certificate Requests

```bash
sudo /opt/puppetlabs/bin/puppetserver ca list
```

---

## Sign Agent Certificate

```bash
sudo /opt/puppetlabs/bin/puppetserver ca sign \
--certname puppet-agent.devops.local
```

---

## List Signed Certificates

```bash
sudo /opt/puppetlabs/bin/puppetserver ca list --all
```

---

# Best Practices

> **✅ Best Practice**
>
> Use private IP addresses for Puppet Master-Agent communication when both servers are located inside the same AWS VPC.

> **✅ Best Practice**
>
> Restrict Puppet port `8140` access to only trusted Puppet nodes instead of allowing access from the internet.

> **✅ Best Practice**
>
> Allocate sufficient memory for Puppet Server. Production environments should use appropriately sized instances based on the number of managed nodes.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Created Puppet Master and Puppet Agent EC2 instances.
* ✅ Configured hostname resolution between nodes.
* ✅ Installed Puppet Server on the master node.
* ✅ Installed Puppet Agent on the agent node.
* ✅ Configured Puppet agent communication.
* ✅ Created and signed Puppet certificates.
* ✅ Established trusted communication between Puppet nodes.
