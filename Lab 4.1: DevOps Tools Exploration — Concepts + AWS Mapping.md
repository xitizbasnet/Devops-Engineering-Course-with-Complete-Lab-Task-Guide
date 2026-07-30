# Lab 4.1: DevOps Tools Exploration — Concepts + AWS Mapping

## Overview

This lab introduces the DevOps lifecycle and maps commonly used open-source tools to their AWS-managed service equivalents. Understanding where each tool fits within the Software Development Life Cycle (SDLC) helps build a strong foundation before working with the technologies in subsequent labs.

## Learning Objectives

By the end of this lab, you will be able to:

* Map DevOps lifecycle phases to real-world tools.
* Understand the Software Development Life Cycle (SDLC) using AWS service equivalents.
* Identify which tools are used throughout the DevOps learning path.

---

# DevOps Lifecycle Overview

## Objective

Understand how each phase of the DevOps lifecycle is supported by open-source tools and corresponding AWS services.

| DevOps Phase | Open Source Tools     | AWS Equivalent                    |
| ------------ | --------------------- | --------------------------------- |
| **Plan**     | Jira, Trello          | AWS CodeStar, Backlog             |
| **Code**     | Git, GitHub, GitLab   | AWS CodeCommit                    |
| **Build**    | Maven, Gradle, npm    | AWS CodeBuild                     |
| **Test**     | Selenium, JUnit       | AWS Device Farm                   |
| **Release**  | Jenkins, GitLab CI    | AWS CodePipeline                  |
| **Deploy**   | Ansible, Chef, Puppet | AWS CodeDeploy, Elastic Beanstalk |
| **Operate**  | Kubernetes, Docker    | Amazon ECS, Amazon EKS            |
| **Monitor**  | ELK Stack, Prometheus | Amazon CloudWatch, AWS X-Ray      |

> **ℹ️ Note**
>
> The tools listed above represent common examples used across the DevOps ecosystem. Organizations may use different tools depending on their infrastructure, cloud provider, and operational requirements.

---

# Tool Usage Across This Learning Path

The following technologies are introduced and used throughout the training modules:

| Category                                                 | Tools           |
| -------------------------------------------------------- | --------------- |
| **Version Control**                                      | Git, GitHub     |
| **Programming & Automation**                             | Python          |
| **Cloud Platform**                                       | AWS             |
| **Containerization**                                     | Docker          |
| **Configuration Management**                             | Puppet, Ansible |
| **Continuous Testing**                                   | Selenium        |
| **Continuous Integration / Continuous Delivery (CI/CD)** | Jenkins         |
| **Container Orchestration**                              | Kubernetes      |
| **Monitoring & Logging**                                 | ELK Stack       |
| **Infrastructure as Code (IaC)**                         | Terraform       |

---

# Best Practices

> **✅ Best Practice**
>
> DevOps is a culture first and tools second. Focus on automation, collaboration, continuous improvement, and eliminating bottlenecks throughout the Software Development Life Cycle (SDLC), rather than simply learning individual tools.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Explored the DevOps lifecycle.
* ✅ Mapped each DevOps phase to commonly used open-source tools.
* ✅ Identified AWS service equivalents for each lifecycle phase.
* ✅ Reviewed the technologies that will be used throughout the training program.
