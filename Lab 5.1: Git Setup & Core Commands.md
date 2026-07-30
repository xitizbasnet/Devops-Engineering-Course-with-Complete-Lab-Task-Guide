# Lab 5.1: Git Setup & Core Commands

## Overview

This lab introduces Git installation, configuration, repository initialization, and the core Git workflow. You will also learn common Git commands used for version control and collaboration with remote repositories such as GitHub.

## Learning Objectives

By the end of this lab, you will be able to:

* Install and configure Git.
* Configure your Git identity and global settings.
* Initialize a Git repository.
* Stage and commit changes.
* Connect a local repository to GitHub.
* Use commonly used Git commands for daily development.

---

# Task 1: Install & Configure Git

## Objective

Install Git on Ubuntu and configure the global Git settings for your development environment.

### Install Git

```bash
sudo apt update && sudo apt install git -y

git --version
```

### Configure Git Identity

```bash
git config --global user.name "Vinod Muleva"

git config --global user.email "vinod@email.com"

git config --global core.editor nano

git config --global init.defaultBranch main
```

### Verify Git Configuration

```bash
git config --list
```

> **ℹ️ Note**
>
> The `--global` option applies the configuration to the current user and all Git repositories on the system.

---

# Task 2: Git Lifecycle — Init to Push

## Objective

Initialize a Git repository, track project files, create commits, and push the repository to GitHub.

### Create a New Project

```bash
mkdir devops-project && cd devops-project

git init

ls -la
```

The `.git` directory is created, indicating that the folder has been initialized as a Git repository.

---

### Create Project Files

```bash
echo "# DevOps Project" > README.md

echo "APP_ENV=dev" > .env
```

---

### Stage and Commit Changes

Check the repository status:

```bash
git status
```

Stage a single file:

```bash
git add README.md
```

Stage all files:

```bash
git add .
```

Verify the staged files:

```bash
git status
```

Create the first commit:

```bash
git commit -m "feat: initial project setup"
```

---

### View Commit History

Display a concise commit history:

```bash
git log --oneline
```

Display the commit graph:

```bash
git log --oneline --graph --all
```

---

### Push the Repository to GitHub

Create a repository on GitHub, then configure the remote repository.

```bash
git remote add origin https://github.com/<user>/devops-project.git

git push -u origin main
```

#### Personal Access Token (PAT)

Instead of using a password, generate a Personal Access Token (PAT).

```text
GitHub → Settings → Developer Settings → Personal Access Token (PAT) → Generate
```

Use the generated token as the password when prompted during authentication.

> **⚠️ Important**
>
> GitHub no longer supports password authentication for Git operations over HTTPS. Use a Personal Access Token (PAT) or SSH authentication instead.

---

# Task 3: Git Common Commands Reference

## Objective

Review commonly used Git commands and their purposes.

| Command                   | Description                                             |
| ------------------------- | ------------------------------------------------------- |
| `git clone <url>`         | Copy a remote repository locally.                       |
| `git pull`                | Fetch and merge changes from a remote repository.       |
| `git fetch`               | Fetch changes from a remote repository without merging. |
| `git diff`                | Show unstaged changes.                                  |
| `git diff --staged`       | Show staged changes.                                    |
| `git reset HEAD file`     | Unstage a file.                                         |
| `git revert <hash>`       | Undo a commit safely by creating a new commit.          |
| `git reset --hard HEAD~1` | Undo the last commit (destructive).                     |
| `git tag v1.0`            | Tag a commit or release.                                |
| `git stash`               | Temporarily save uncommitted changes.                   |
| `git stash pop`           | Restore previously stashed changes.                     |

---

# Best Practices

> **✅ Best Practice**
>
> Configure your Git identity before creating commits to ensure all commits are properly attributed.

> **✅ Best Practice**
>
> Commit frequently with clear, meaningful commit messages that describe the purpose of each change.

> **✅ Best Practice**
>
> Use a Personal Access Token (PAT) or SSH keys instead of passwords when authenticating with GitHub.

> **⚠️ Warning**
>
> Use `git reset --hard` with caution. It permanently discards uncommitted changes and can remove commit history if used incorrectly.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Installed and configured Git.
* ✅ Initialized a local Git repository.
* ✅ Created, staged, and committed project files.
* ✅ Connected the repository to GitHub.
* ✅ Pushed commits to a remote repository.
* ✅ Reviewed commonly used Git commands for everyday development.
