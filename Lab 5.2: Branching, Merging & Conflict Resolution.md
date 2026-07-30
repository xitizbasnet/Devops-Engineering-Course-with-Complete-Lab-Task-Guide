# Lab 5.2: Branching, Merging & Conflict Resolution

## Overview

This lab introduces Git branching strategies, feature development workflows, and merge conflict resolution. You will create and manage branches, merge changes between branches, and resolve conflicts that occur when multiple branches modify the same file.

## Learning Objectives

By the end of this lab, you will be able to:

* Create and manage Git branches.
* Work on feature branches.
* Push branches to remote repositories.
* Merge changes between branches.
* Identify and resolve Git merge conflicts.
* Review Git history using commit graphs.

---

# Task 1: Create & Work on Branches

## Objective

Create a feature branch, make changes independently, commit the changes, and push the branch to GitHub.

### Create and Switch to a Branch

```bash id="w2n7xg"
git checkout -b feature/user-auth
```

List available branches:

```bash id="4t5g1j"
git branch
```

The output displays available branches, with `*` indicating the current active branch.

---

### Make Changes on Feature Branch

Create a new authentication file:

```bash id="6p4rkh"
echo 'def login(): pass' > auth.py
```

Stage and commit the changes:

```bash id="6b1zkm"
git add .

git commit -m "feat: add login function"
```

---

### Push Feature Branch to GitHub

```bash id="9m8f0c"
git push -u origin feature/user-auth
```

> **ℹ️ Note**
>
> Feature branches allow developers to work on new functionality without directly affecting the main production branch.

---

# Task 2: Simulate & Resolve Merge Conflict

## Objective

Create a merge conflict by modifying the same file in different branches and resolve the conflict manually.

---

## Step 1: Modify File on Main Branch

Switch to the main branch:

```bash id="d9g7zl"
git checkout main
```

Create and commit a version file:

```bash id="c4h6jx"
echo 'version=1.0' > version.txt

git add .

git commit -m "add version on main"
```

---

## Step 2: Modify the Same File on Feature Branch

Switch back to the feature branch:

```bash id="9f0g4h"
git checkout feature/user-auth
```

Modify the same file with different content:

```bash id="g1f3qk"
echo 'version=2.0' > version.txt

git add .

git commit -m "add version on feature"
```

---

## Step 3: Attempt the Merge

Switch back to the main branch:

```bash id="w9p6df"
git checkout main
```

Merge the feature branch:

```bash id="8k2jcw"
git merge feature/user-auth
```

A merge conflict occurs:

```text
CONFLICT in version.txt!
```

---

## Step 4: Inspect Conflict Markers

View the conflicting file:

```bash id="3t7j6q"
cat version.txt
```

Git displays conflict markers similar to:

```text id="q5o1zz"
<<<<<<< HEAD
version=1.0
=======
version=2.0
>>>>>>> feature/user-auth
```

---

## Step 5: Resolve the Conflict

Choose the desired version and update the file:

```bash id="q4x1fu"
echo 'version=2.0' > version.txt
```

Stage the resolved file:

```bash id="h0d8rn"
git add version.txt
```

Create the merge resolution commit:

```bash id="8y3r5v"
git commit -m "fix: resolve version merge conflict"
```

---

## Review Git History

View the commit graph:

```bash id="x7q1sp"
git log --oneline --graph
```

---

# Best Practices

> **✅ Best Practice**
>
> Use feature branches for development work instead of making changes directly on the main branch.

> **✅ Best Practice**
>
> Resolve merge conflicts carefully by reviewing both versions before choosing which changes to keep.

> **✅ Best Practice**
>
> Commit frequently with meaningful messages to make troubleshooting and collaboration easier.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Created and managed Git feature branches.
* ✅ Developed changes independently from the main branch.
* ✅ Pushed feature branches to GitHub.
* ✅ Simulated a real-world merge conflict.
* ✅ Resolved conflicts manually.
* ✅ Reviewed Git history using commit graphs.
