---
layout: post
title: How to Use JUnit 5 for Simple Method Testing
date: 2023-11-15
tags: Junit Junit5
---

## Introduction

JUnit 5, the latest version of the popular Java testing framework, provides a modern and flexible platform for writing unit tests. It introduces several improvements over JUnit 4, such as better support for Java 8 features, more powerful assertions, and dynamic testing capabilities. In this article, we’ll demonstrate how to use JUnit 5 to test a simple Java method.

## Main Section

#### The Method to Test

Let's consider a simple method in a utility class that checks if a given number is even:

```java
public class MathUtils {
    public static boolean isEven(int number) {
        return number % 2 == 0;
    }
}
```

#### Setting Up JUnit 5

To use JUnit 5, ensure your project includes the JUnit 5 dependencies. If you're using Maven, add the following to your pom.xml:

```xml
<dependencies>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.10.0</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

#### Writing the Test

Here’s a basic test for the ```isEven``` method:

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertTrue;
import static org.junit.jupiter.api.Assertions.assertFalse;

public class MathUtilsTest {

    @Test
    void testIsEven() {
        // Test case: Even number
        assertTrue(MathUtils.isEven(4), "4 should be even");
        
        // Test case: Odd number
        assertFalse(MathUtils.isEven(3), "3 should not be even");
    }
}
```

#### Explanation of the Test

**Test Annotation** The ``@Test`` annotation marks a method as a test method.

**Assertions:**
- ```assertTrue(condition, message)```: Ensures the condition is true, otherwise fails with the provided message.
- ```assertFalse(condition, message)```: Ensures the condition is false.

**Testing Multiple Scenarios:** We test both even and odd numbers to verify that the method works correctly for different inputs.

#### Running the Test

Most modern IDEs like IntelliJ IDEA, Eclipse, or VS Code support running JUnit 5 tests directly. To execute the test:

1. Right-click on the test file or method and select **Run**.
2. Alternatively, use the terminal to run tests with Maven or Gradle:

**Maven:** ```mvn test```

## Summary

JUnit 5 simplifies unit testing with its powerful assertions, cleaner API, and support for modern Java features. In this article, we covered:

- Writing a simple method to check if a number is even.
- Setting up JUnit 5 in your project.
- Creating and running tests using JUnit 5.

Unit testing is essential for ensuring the reliability of your code, and JUnit 5 makes it easier than ever to write and maintain tests. Start incorporating it into your development workflow today!
