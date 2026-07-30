# Lab 8.2: Ansible Playbooks — Automate Server Configuration

## Overview

This lab introduces Ansible Playbooks for automating server configuration tasks. You will create a reusable playbook to deploy a LAMP stack, validate the playbook syntax, perform a dry run, apply configuration changes, and verify the deployment.

## Learning Objectives

By the end of this lab, you will be able to:

* Create Ansible Playbooks for automated configuration management.
* Define variables and reusable configuration values.
* Install and configure software packages using Ansible.
* Manage services using Ansible modules.
* Use handlers for service restarts.
* Validate and execute Ansible Playbooks safely.

---

# Task 1: Deploy LAMP Stack with Playbook

## Objective

Create an Ansible Playbook that automatically installs and configures:

* **Linux** — Managed Ubuntu server.
* **Apache** — Web server.
* **MySQL** — Database server.
* **PHP** — Application runtime environment.

The playbook will:

* Update package repositories.
* Install required packages.
* Start and enable Apache.
* Deploy a PHP test page.
* Configure firewall access.
* Restart Apache when required.

---

# Step 1: Create LAMP Playbook

Create the playbook file:

```bash id="x7m4pq"
cat > lamp-setup.yml << 'EOF'
---
- name: Install and Configure LAMP Stack
  hosts: webservers
  become: yes

  vars:
    http_port: 80
    db_name: devopsdb
    db_user: appuser
    db_pass: App@2024!

  tasks:

    - name: Update apt cache
      apt:
        update_cache: yes
        cache_valid_time: 3600

    - name: Install required packages
      apt:
        name:
          - apache2
          - mysql-server
          - php
          - php-mysql
          - libapache2-mod-php
        state: present

    - name: Start and enable Apache
      service:
        name: apache2
        state: started
        enabled: yes

    - name: Deploy index.php
      copy:
        content: '<?php phpinfo(); ?>'
        dest: /var/www/html/index.php
        owner: www-data
        group: www-data
        mode: '0644'
      notify: restart apache

    - name: Configure firewall
      ufw:
        rule: allow
        port: "{{ http_port }}"
        proto: tcp

  handlers:

    - name: restart apache
      service:
        name: apache2
        state: restarted
EOF
```

---

# Playbook Structure Explanation

| Section    | Purpose                                  |
| ---------- | ---------------------------------------- |
| `hosts`    | Defines target managed servers.          |
| `become`   | Executes tasks with elevated privileges. |
| `vars`     | Stores reusable configuration values.    |
| `tasks`    | Defines configuration actions.           |
| `handlers` | Runs actions triggered by changes.       |

---

# Task 2: Validate Playbook

## Objective

Verify the playbook before applying changes.

---

## Syntax Check

Run:

```bash id="m4x8qn"
ansible-playbook -i inventory.ini lamp-setup.yml --syntax-check
```

Expected result:

```text
Syntax OK
```

---

# Task 3: Perform Dry Run

## Objective

Preview changes without modifying servers.

Run:

```bash id="q9v3pk"
ansible-playbook -i inventory.ini lamp-setup.yml --check
```

> **ℹ️ Note**
>
> The `--check` option performs a simulation run and helps identify potential issues before applying changes.

---

# Task 4: Apply Configuration

## Objective

Execute the playbook and configure managed servers.

Run:

```bash id="r6k2wm"
ansible-playbook -i inventory.ini lamp-setup.yml -v
```

The playbook will:

* Install Apache.
* Install MySQL.
* Install PHP packages.
* Deploy the PHP test page.
* Configure firewall access.
* Restart Apache if required.

---

# Task 5: Test Deployment

## Objective

Verify that the LAMP stack is running successfully.

Access the deployed PHP page:

```bash id="p3z7xc"
curl http://<NODE1_IP>
```

Expected output:

```text
PHP information page response
```

---

# Best Practices

> **✅ Best Practice**
>
> Always validate Ansible Playbooks using `--syntax-check` before execution.

> **✅ Best Practice**
>
> Use `--check` mode to preview changes before applying them to production environments.

> **✅ Best Practice**
>
> Use variables for configurable values such as ports, usernames, and application settings instead of hardcoding values throughout playbooks.

> **✅ Best Practice**
>
> Use handlers for actions that should only run when configuration changes occur, such as restarting services.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Created an Ansible Playbook for LAMP deployment.
* ✅ Automated package installation and service configuration.
* ✅ Used variables and handlers in a playbook.
* ✅ Validated playbook syntax.
* ✅ Performed a dry run before deployment.
* ✅ Applied automated server configuration.
* ✅ Verified the deployed web application.
