# Lab 8.3: Ansible Roles & Galaxy

## Overview

This lab introduces Ansible Roles and Ansible Galaxy for creating reusable and organized automation components. You will create an Nginx role, structure Ansible automation using best practices, apply roles in playbooks, and install community-maintained roles from Ansible Galaxy.

## Learning Objectives

By the end of this lab, you will be able to:

* Create Ansible roles using `ansible-galaxy`.
* Understand Ansible role directory structure.
* Separate tasks, handlers, templates, and variables.
* Apply roles inside Ansible Playbooks.
* Install and manage community roles from Ansible Galaxy.
* Follow Ansible automation best practices.

---

# Task 1: Create Ansible Role Structure

## Objective

Create a reusable Ansible role for managing Nginx installation and configuration.

Create the role:

```bash id="n6q8vm"
ansible-galaxy init roles/nginx
```

View the generated role structure:

```bash id="p4x7kc"
tree roles/nginx/
```

Expected structure:

```text id="h7m3qx"
nginx/
├── defaults/
│   └── main.yml
├── handlers/
│   └── main.yml
├── tasks/
│   └── main.yml
├── templates/
├── vars/
│   └── main.yml
```

---

# Ansible Role Directory Structure

| Directory    | Purpose                                             |
| ------------ | --------------------------------------------------- |
| `tasks/`     | Contains automation tasks.                          |
| `handlers/`  | Contains triggered actions such as service reloads. |
| `templates/` | Stores Jinja2 configuration templates.              |
| `defaults/`  | Stores default variables.                           |
| `vars/`      | Stores role-specific variables.                     |

---

# Task 2: Configure Nginx Role Tasks

## Objective

Define tasks for installing Nginx, starting the service, and deploying configuration files.

Edit the role task file:

```bash id="r8v2mw"
roles/nginx/tasks/main.yml
```

Add:

```yaml id="y5n7cx"
---
- name: Install nginx
  apt:
    name: nginx
    state: present
    update_cache: yes

- name: Start nginx
  service:
    name: nginx
    state: started
    enabled: yes

- name: Deploy nginx config
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/sites-available/default
  notify: reload nginx
```

---

# Task 3: Configure Role Handler

## Objective

Create a handler to reload Nginx after configuration changes.

Edit the handler file:

```bash id="c3m8qp"
roles/nginx/handlers/main.yml
```

Add:

```yaml id="v7x2mr"
---
- name: reload nginx
  service:
    name: nginx
    state: reloaded
```

---

# Task 4: Use Role in Ansible Playbook

## Objective

Apply the Nginx role to web servers.

Create or edit:

```bash id="m9q4vx"
site.yml
```

Add:

```yaml id="f2k8wp"
---
- hosts: webservers
  become: yes

  roles:
    - nginx
```

---

## Execute Playbook

Run the playbook:

```bash id="z4n7kc"
ansible-playbook -i inventory.ini site.yml
```

Ansible will:

* Install Nginx.
* Start and enable the service.
* Deploy Nginx configuration.
* Reload Nginx when configuration changes occur.

---

# Task 5: Install Roles from Ansible Galaxy

## Objective

Use community-created Ansible roles to extend automation capabilities.

Install Docker role:

```bash id="w8m3qp"
ansible-galaxy install geerlingguy.docker
```

Install MySQL role:

```bash id="q6x9mv"
ansible-galaxy install geerlingguy.mysql
```

List installed Galaxy roles:

```bash id="n3p7kc"
ansible-galaxy list
```

---

# Best Practices

> **✅ Best Practice**
>
> Always use `--check` mode (dry run) before applying any playbook to production environments.

```bash
ansible-playbook playbook.yml --check
```

> **✅ Best Practice**
>
> Use `--diff` to view exactly what file changes will be applied before modifying systems.

```bash
ansible-playbook playbook.yml --diff
```

> **✅ Best Practice**
>
> Store sensitive information such as passwords and keys using Ansible Vault.

Create encrypted secrets:

```bash
ansible-vault create secrets.yml
```

> Never commit plaintext passwords or credentials to Git repositories.

---

# Alternative Tools

> **💡 Tip**
>
> Other configuration management options include:
>
> * **Chef** — Uses a Ruby-based DSL for infrastructure automation.
> * **AWS Systems Manager (SSM)** — Provides cloud-based configuration management and automation for EC2 instances without requiring traditional agents.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Created an Ansible role structure.
* ✅ Automated Nginx installation using roles.
* ✅ Configured handlers for service management.
* ✅ Applied roles through Ansible Playbooks.
* ✅ Installed reusable roles from Ansible Galaxy.
* ✅ Learned secure Ansible automation practices.
