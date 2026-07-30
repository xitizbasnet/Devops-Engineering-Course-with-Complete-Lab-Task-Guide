# Lab 9.2: Headless Selenium Test Cases

## Overview

This lab introduces headless browser automation testing using Selenium WebDriver. You will create automated test cases using Java, JUnit, and Selenium that can execute on a server environment without requiring a graphical user interface.

## Learning Objectives

By the end of this lab, you will be able to:

* Create Selenium WebDriver test cases using Java.
* Configure Chromium for headless execution.
* Automate browser actions without a GUI.
* Execute Selenium tests using Maven.
* Apply Selenium automation best practices.

---

# Task 1: Write Headless Selenium Test

## Objective

Create automated Selenium tests that run in headless mode on an EC2 server.

Headless mode allows browser automation without opening a visible browser window, making it suitable for:

* CI/CD pipelines.
* Cloud servers.
* Automated testing environments.

---

## Step 1: Create Selenium Test Class

Create the test file:

```bash id="w5k8mq"
src/test/java/com/devops/testing/WebsiteTest.java
```

Add the following content:

```java id="p7x3vn"
package com.devops.testing;

import org.junit.jupiter.api.*;
import org.openqa.selenium.*;
import org.openqa.selenium.chrome.*;

public class WebsiteTest {

    private WebDriver driver;

    @BeforeEach
    public void setUp() {

        ChromeOptions options = new ChromeOptions();

        options.addArguments("--headless"); 
        // no GUI needed

        options.addArguments("--no-sandbox"); 
        // required for EC2

        options.addArguments("--disable-dev-shm-usage");

        options.addArguments("--disable-gpu");

        options.setBinary("/usr/bin/chromium-browser");

        driver = new ChromeDriver(options);
    }


    @Test
    public void testGoogleTitle() {

        driver.get("https://www.google.com");

        String title = driver.getTitle();

        System.out.println("Page title: " + title);

        Assertions.assertTrue(
            title.contains("Google"),
            "Title should contain Google"
        );
    }


    @Test
    public void testSearchFunctionality() {

        driver.get("https://www.google.com");

        WebElement searchBox =
            driver.findElement(By.name("q"));

        searchBox.sendKeys("DevOps Jenkins");

        searchBox.submit();

        Assertions.assertTrue(
            driver.getCurrentUrl()
            .contains("DevOps+Jenkins")
        );
    }


    @AfterEach
    public void tearDown() {

        if (driver != null) {
            driver.quit();
        }
    }
}
```

---

# Test Case Explanation

| Test Case                   | Description                                     |
| --------------------------- | ----------------------------------------------- |
| `testGoogleTitle()`         | Opens Google and verifies the page title.       |
| `testSearchFunctionality()` | Performs a search and validates the result URL. |

---

# Selenium Configuration Explanation

| Configuration             | Purpose                                                           |
| ------------------------- | ----------------------------------------------------------------- |
| `--headless`              | Runs Chrome without a graphical interface.                        |
| `--no-sandbox`            | Required for Chrome execution on many Linux servers.              |
| `--disable-dev-shm-usage` | Prevents shared memory issues in containers and EC2 environments. |
| `--disable-gpu`           | Improves compatibility in headless environments.                  |

---

# Step 2: Execute Selenium Tests

Run Maven tests:

```bash id="r8m4qz"
mvn test
```

Expected result:

```text id="n3x7vp"
Tests run in headless mode — no display required!
```

---

# Best Practices

> **✅ Best Practice**
>
> Always run Selenium tests in headless mode on servers and CI/CD environments using the `--headless` browser option.

> **✅ Best Practice**
>
> Use `WebDriverWait` with explicit conditions instead of `Thread.sleep()` to create reliable Selenium tests.

Example:

```java
WebDriverWait wait =
    new WebDriverWait(driver, Duration.ofSeconds(10));
```

> **✅ Best Practice**
>
> Always close browser sessions after test execution using `driver.quit()` to prevent resource leaks.

---

# Alternative Tools

> **💡 Tip**
>
> Modern alternatives to Selenium include:
>
> * **Playwright** — Developed by Microsoft; supports Python, JavaScript, and Java with fast and stable browser automation.
> * **Cypress** — Popular JavaScript-based frontend testing framework with excellent developer experience.

---

# Lab Summary

In this lab, you completed the following tasks:

* ✅ Created Selenium WebDriver test cases.
* ✅ Configured Chromium for headless execution.
* ✅ Automated browser navigation and search testing.
* ✅ Executed Selenium tests using Maven.
* ✅ Learned best practices for reliable server-side browser automation.
