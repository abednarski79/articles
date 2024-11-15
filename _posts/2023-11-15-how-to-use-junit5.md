---
layout: post
title: How to Use JUnit 5 for Simple Method Testing
date: 2023-11-15
---

## Introduction

JUnit 5 is the latest version of the popular Java testing framework, offering a modern platform with enhanced features like better support for Java 8, more powerful assertions, and dynamic testing. This article demonstrates how to use JUnit 5 to test a simple Java method.

## Example Code

```java
public static boolean isPrime(int number) {
    if (number <= 1) return false;
    for (int i = 2; i <= Math.sqrt(number); i++) {
        if (number % i == 0) return false;
    }
    return true;
}
```
