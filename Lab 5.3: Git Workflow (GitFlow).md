# Lab 5.3: Git Workflow (GitFlow)

## Overview

This lab introduces the **GitFlow branching strategy**, a structured workflow used by development teams to manage feature development, releases, and production fixes. You will learn how different branch types are used throughout the software development lifecycle.

## Learning Objectives

By the end of this lab, you will be able to:

* Understand the GitFlow branching model.
* Create and manage different branch types.
* Follow a structured development and release workflow.
* Apply best practices for Git commit messages.

---

# GitFlow Branch Strategy

## Objective

Understand the purpose of each branch type in the GitFlow workflow.

| Branch        | Purpose                    |
| ------------- | -------------------------- |
| **main**      | Production-ready code      |
| **develop**   | Integration branch         |
| **feature/*** | New features               |
| **release/*** | Release preparation        |
| **hotfix/***  | Emergency production fixes |

---

# Typical GitFlow Workflow

## Step 1: Create Develop Branch

Switch to the main branch:

```bash id="j3f7qk"
git checkout main
```

Create and switch to the develop branch:

```bash id="4x8kq2"
git checkout -b develop
```

---

## Step 2: Create Feature Branch

Create a feature branch for new development work:

```bash id="8s1n5v"
git checkout -b feature/payment-api
```

Develop the feature and commit changes.

---

## Step 3: Merge Feature into Develop

Switch back to the develop branch:

```bash id="m7p2cx"
git checkout develop
```

Merge the feature branch:

```bash id="v5q8ld"
git merge feature/payment-api --no-ff
```

Delete the completed feature branch:

```bash id="b6w2ra"
git branch -d feature/payment-api
```

---

# Step 4: Prepare a Release

Create a release branch from develop:

```bash id="n9k4pf"
git checkout -b release/1.0 develop
```

Perform release preparation activities:

```text
... fix release bugs ...
```

---

# Step 5: Merge Release into Main

Switch to the main branch:

```bash id="h8r3ws"
git checkout main
```

Merge the release branch:

```bash id="c2m7yx"
git merge release/1.0 --no-ff
```

Create a version tag:

```bash id="f9v1kd"
git tag v1.0
```

Push the main branch and tags:

```bash id="q3z6mt"
git push origin main --tags
```

---

# Best Practices

> **✅ Best Practice**
>
> Write meaningful commit messages using the format:
>
> ```text
> type: description
> ```
>
> Examples:
>
> ```text
> feat: add payment API
> fix: resolve login bug
> docs: update README
> ```
>
> This format helps maintain clear project history and enables automated changelog generation.

---

# Alternative Tools

> **💡 Tip**
>
> Instead of GitHub, you can use:
>
> * **GitLab** — Provides built-in CI/CD capabilities.
> * **AWS CodeCommit** — Provides private repositories with IAM authentication.
> * **Bitbucket** — Commonly used in enterprise environments with Jira integration.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Learned the GitFlow branching strategy.
* ✅ Created develop and feature branches.
* ✅ Merged feature changes using a structured workflow.
* ✅ Prepared release branches.
* ✅ Created version tags for releases.
* ✅ Reviewed Git best practices for professional development workflows.
