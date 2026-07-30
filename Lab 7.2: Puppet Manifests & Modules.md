# Lab 7.2: Puppet Manifests & Modules

## Overview

This lab introduces Puppet manifests and modules for automating server configuration. You will create Puppet manifests to define the desired state of a server, apply configurations from the Puppet Master, and build reusable Puppet modules for managing infrastructure.

## Learning Objectives

By the end of this lab, you will be able to:

* Create Puppet manifests using Puppet's declarative syntax.
* Automate package installation and service management.
* Manage files using Puppet resources.
* Create reusable Puppet modules.
* Install and use Puppet Forge modules.

---

# Task 1: Write Puppet Manifest to Install Nginx

## Objective

Create a Puppet manifest that installs Nginx, ensures the service is running, and manages the default web page on the Puppet Agent node.

---

## Step 1: Create Puppet Manifest on Master

On the **MASTER** server, create the manifest:

```bash id="p8x3wm"
sudo nano /etc/puppetlabs/code/environments/production/manifests/site.pp
```

---

## Step 2: Add Puppet Manifest Content

Add the following configuration:

```puppet id="h7q2mv"
node 'puppet-agent.devops.local' {

  package { 'nginx':
    ensure => installed,
  }

  service { 'nginx':
    ensure  => running,
    enable  => true,
    require => Package['nginx'],
  }

  file { '/var/www/html/index.html':
    ensure  => file,
    content => '<h1>Managed by Puppet!</h1>',
    owner   => 'www-data',
    group   => 'www-data',
    mode    => '0644',
    require => Package['nginx'],
  }

}
```

---

## Manifest Resource Explanation

| Resource  | Purpose                                       |
| --------- | --------------------------------------------- |
| `package` | Ensures Nginx is installed on the agent.      |
| `service` | Ensures Nginx is running and enabled at boot. |
| `file`    | Creates and manages the website index file.   |

---

## Step 3: Apply Configuration from Agent

Run Puppet agent:

```bash id="q5n8zr"
sudo /opt/puppetlabs/bin/puppet agent --test
```

Expected result:

```text id="f7k2pc"
Puppet installs nginx, starts service, creates HTML file!
```

---

# Task 2: Create a Puppet Module

## Objective

Create a reusable Puppet module to organize and manage infrastructure configurations.

---

## Step 1: Create Puppet Module Structure

On the **MASTER** server:

Navigate to the modules directory:

```bash id="x8m4vd"
cd /etc/puppetlabs/code/environments/production/modules
```

Generate a Puppet module:

```bash id="r3k9qw"
sudo puppet module generate devops-webserver --skip-interview
```

---

## Module Directory Structure

The generated module contains:

```text id="d9x3mq"
webserver/
├── manifests/
│   └── init.pp
├── templates/
├── files/
└── tests/
```

---

## Step 2: Edit Module Manifest

Open the module manifest:

```bash id="k6w2pv"
sudo nano webserver/manifests/init.pp
```

Add the following Puppet class:

```puppet id="v4n8cx"
class webserver {

  package { 'nginx':
    ensure => installed
  }

  service { 'nginx':
    ensure => running,
    enable => true
  }

}
```

---

## Step 3: Use Module in site.pp

Update the Puppet site manifest:

```puppet id="m7q2zs"
node 'puppet-agent.devops.local' {

  include webserver

}
```

The Puppet agent will now use the reusable `webserver` module.

---

# Task 3: Install Puppet Forge Module

## Objective

Install a community-supported Puppet module from Puppet Forge.

Install Apache module:

```bash id="w5c9yx"
sudo puppet module install puppetlabs-apache
```

List installed Puppet modules:

```bash id="z4m8qp"
sudo puppet module list
```

---

# Best Practices

> **✅ Best Practice**
>
> Puppet uses a declarative approach. Define the desired state, and Puppet determines how to achieve that state. The same manifest can work across different operating system versions.

> **✅ Best Practice**
>
> Use Puppet modules to organize reusable configurations instead of maintaining large single manifest files.

> **✅ Best Practice**
>
> Test Puppet manifests in a non-production environment before applying changes to production systems.

---

# Alternative Tool

> **💡 Tip**
>
> Ansible is a simpler, agentless alternative to Puppet.
>
> * **Ansible** is often preferred by small teams because it requires no agent installation.
> * **Puppet/Chef** are commonly used in large enterprise environments managing thousands of servers.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Created a Puppet manifest for Nginx management.
* ✅ Automated package installation and service configuration.
* ✅ Managed website files using Puppet resources.
* ✅ Created a reusable Puppet module.
* ✅ Installed a Puppet Forge module.
* ✅ Learned Puppet configuration management best practices.
