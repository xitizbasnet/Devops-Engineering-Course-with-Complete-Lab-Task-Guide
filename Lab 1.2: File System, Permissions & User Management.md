# Lab 1.2: File System, Permissions & User Management

## Overview

This lab introduces Linux file permissions, user and group management, pipes, redirects, and environment variables. These are essential administration skills for managing Linux systems in DevOps environments.

## Learning Objectives

By the end of this lab, you will be able to:

* Understand Linux file permissions.
* Modify permissions using `chmod`.
* Create and manage users and groups.
* Assign administrative privileges.
* Use pipes and output redirection.
* Configure and persist environment variables.
* Create SSH configuration aliases for easier server access.

---

# Task 1: File Permissions (`chmod`)

## Objective

Learn how to view and modify Linux file permissions using both numeric and symbolic modes.

### Create a Test File

```bash
touch script.sh && ls -l script.sh
```

Permissions shown as:

```text
-rw-rw-r--
```

### Change Permissions Using Numeric Mode

```bash
chmod 755 script.sh    # owner: rwx | group: r-x | others: r-x

chmod 600 secret.txt   # owner: rw | no one else

chmod 777 public.sh    # everyone: rwx (avoid in production!)
```

### Change Permissions Using Symbolic Mode

```bash
chmod +x script.sh     # add execute for all

chmod u-w file.txt     # remove write for owner

ls -l script.sh        # verify
```

> **ℹ️ Note**
>
> Numeric permissions provide precise control, while symbolic permissions are convenient for making incremental permission changes.

---

# Task 2: User & Group Management

## Objective

Create users and groups, assign permissions, grant administrative privileges, and manage user accounts.

### Create a User

```bash
sudo adduser devopsuser
```

Set password when prompted.

### Create a Group

```bash
sudo groupadd devteam
```

### Add User to Group

```bash
sudo usermod -aG devteam devopsuser

id devopsuser      # verify

groups devopsuser
```

### Switch User

```bash
su - devopsuser

whoami && exit
```

### Grant Sudo Privileges

```bash
sudo visudo
```

Add the following line at the end of the file:

```text
devopsuser ALL=(ALL:ALL) NOPASSWD:ALL
```

### Delete the User

```bash
sudo userdel -r devopsuser
```

> **⚠️ Important**
>
> The `-r` option removes both the user account and the user's home directory.

---

# Task 3: Pipes, Redirects & Environment Variables

## Objective

Learn to redirect command output, chain commands using pipes, and configure environment variables.

### Redirect Output

```bash
echo 'Server=prod' > config.txt

echo 'Port=8080' >> config.txt

cat config.txt
```

---

### Pipe Chaining

```bash
cat /etc/passwd | grep '/bin/bash' | wc -l
```

```bash
ps aux | sort -k3 -rn | head -5    # top 5 CPU-consuming processes
```

---

### Environment Variables

```bash
export APP_ENV=production

export DB_HOST=10.0.1.5

echo $APP_ENV && printenv | grep APP
```

---

### Persist Environment Variables

```bash
echo 'export APP_ENV=production' >> ~/.bashrc

source ~/.bashrc
```

---

### SSH Configuration Shortcut

Create the SSH configuration directory:

```bash
mkdir -p ~/.ssh
```

Create the SSH configuration file:

```bash
cat > ~/.ssh/config << 'EOF'
Host devops-ec2
HostName <PUBLIC_IP>
User ubuntu
IdentityFile ~/.ssh/devops-key.pem
EOF
```

Connect using the alias:

```bash
ssh devops-ec2    # now just use alias!
```

---

# Best Practices

> **✅ Best Practice**
>
> Never `chmod 777` any file in production. Use `755` for executables and `644` for regular files. For SSH keys, always use `400`.

> **✅ Best Practice**
>
> Use `~/.bashrc` or `~/.bash_profile` to persist environment variables. For system-wide variables, use `/etc/environment`.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Managed Linux file permissions using `chmod`.
* ✅ Created and managed users and groups.
* ✅ Assigned administrative privileges.
* ✅ Used pipes and output redirection.
* ✅ Configured and persisted environment variables.
* ✅ Simplified SSH access using client configuration aliases.
