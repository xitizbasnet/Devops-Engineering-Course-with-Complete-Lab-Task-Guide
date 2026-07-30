# Lab 9.1: Maven + Selenium Setup

## Overview

This lab introduces Selenium-based automated testing using Java and Maven. You will install the required development tools, create a Maven project, configure Selenium dependencies, and prepare the environment for browser automation testing.

## Learning Objectives

By the end of this lab, you will be able to:

* Install Java, Maven, and Chromium browser dependencies.
* Create a Java Maven project.
* Configure Selenium WebDriver dependencies.
* Prepare a test automation environment using JUnit and Selenium.

---

# Task 1: Install Java, Maven & Chromium

## Objective

Install the required tools for Selenium test automation.

---

## Step 1: Update System Packages

```bash id="v5q8mx"
sudo apt update
```

---

## Step 2: Install Required Packages

Install:

* OpenJDK 17
* Apache Maven
* Chromium Browser
* Chromium WebDriver

```bash id="m7x2kp"
sudo apt install -y openjdk-17-jdk maven chromium-browser chromium-chromedriver
```

---

## Step 3: Verify Installation

Check Java version:

```bash id="k4n8qw"
java -version
```

Check Maven version:

```bash id="p8r5mc"
mvn -version
```

Check Chromium version:

```bash id="x3m7vz"
chromium-browser --version
```

---

# Step 4: Create Maven Project

## Objective

Generate a Java Maven project structure for Selenium tests.

Create the project:

```bash id="q9w4ks"
mvn archetype:generate \
-DgroupId=com.devops.testing \
-DartifactId=selenium-tests \
-DarchetypeArtifactId=maven-archetype-quickstart \
-DinteractiveMode=false
```

Navigate into the project directory:

```bash id="n6p3xr"
cd selenium-tests
```

---

# Maven Project Structure

The generated project contains:

```text id="c7m9qx"
selenium-tests/
├── pom.xml
└── src/
    ├── main/
    └── test/
```

---

# Task 2: Add Selenium Dependencies to `pom.xml`

## Objective

Configure Maven dependencies required for Selenium browser automation and automated testing.

Edit the `pom.xml` file:

```bash id="r2k8mv"
nano pom.xml
```

Add the following dependencies inside the `<dependencies>` section:

```xml id="q8m4vz"
<dependency>
    <groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-java</artifactId>
    <version>4.15.0</version>
</dependency>

<dependency>
    <groupId>io.github.bonigarcia</groupId>
    <artifactId>webdrivermanager</artifactId>
    <version>5.6.3</version>
</dependency>

<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.0</version>
    <scope>test</scope>
</dependency>
```

---

# Dependency Explanation

| Dependency         | Purpose                                                  |
| ------------------ | -------------------------------------------------------- |
| `selenium-java`    | Provides Selenium WebDriver APIs for browser automation. |
| `webdrivermanager` | Automatically manages browser driver binaries.           |
| `junit-jupiter`    | Provides JUnit 5 testing framework support.              |

---

# Task 3: Resolve Maven Dependencies

## Objective

Download and verify all configured project dependencies.

Run:

```bash id="z6p3qw"
mvn dependency:resolve
```

Maven downloads the required libraries and prepares the project for Selenium test development.

---

# Best Practices

> **✅ Best Practice**
>
> Use Maven dependency management instead of manually downloading JAR files. This ensures consistent builds across development and CI/CD environments.

> **✅ Best Practice**
>
> Keep browser versions and WebDriver versions compatible to avoid automation failures.

> **✅ Best Practice**
>
> Use JUnit test frameworks to structure Selenium test cases and integrate with CI/CD pipelines.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Installed Java, Maven, and Chromium dependencies.
* ✅ Created a Maven-based Selenium project.
* ✅ Added Selenium and testing framework dependencies.
* ✅ Resolved Maven project dependencies.
* ✅ Prepared the environment for automated browser testing.
