---
layout: post
title: Introduction to Spring JPA with CRUD Operations
date: 2022-10-15
---

# Introduction to Spring JPA with CRUD Operations

Spring Data JPA is a key part of the Spring Framework that allows developers to interact with relational databases easily. By abstracting common data access tasks, Spring Data JPA minimizes boilerplate code and provides a powerful query language. This guide will introduce Spring Data JPA and demonstrate basic CRUD operations using a `Book` entity as an example.

---

## Table of Contents
1. [What is Spring Data JPA?](#what-is-spring-data-jpa)
2. [Setup](#setup)
   - Maven Dependencies
   - Database Configuration
3. [Defining the Entity Class](#defining-the-entity-class)
4. [Creating the Repository](#creating-the-repository)
5. [CRUD Operations](#crud-operations)
6. [Conclusion](#conclusion)

---

## What is Spring Data JPA?

Spring Data JPA simplifies database access by providing a set of repositories that interact with the database, powered by JPA (Java Persistence API). It integrates seamlessly with Spring Boot and Hibernate, enabling robust, maintainable, and efficient database access.

---

## Setup

### Maven Dependencies
Add the following dependencies to your `pom.xml` to use Spring Data JPA and an H2 in-memory database:

```xml
<dependencies>
    <!-- Spring Boot Starter Data JPA -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- H2 Database -->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

### Database Configuration
In `src/main/resources/application.properties`, configure the in-memory H2 database:

```properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.h2.console.enabled=true
```

---

## Defining the Entity Class

Create a `Book` entity class to represent the table structure in the database.

```java
package com.example.demo.model;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Book {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String author;
    private Double price;

    // Constructors
    public Book() {}

    public Book(String title, String author, Double price) {
        this.title = title;
        this.author = author;
        this.price = price;
    }

    // Getters and Setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public Double getPrice() {
        return price;
    }

    public void setPrice(Double price) {
        this.price = price;
    }
}
```

---

## Creating the Repository

Spring Data JPA provides an interface, `JpaRepository`, that includes standard CRUD methods. Define a repository interface for the `Book` entity.

```java
package com.example.demo.repository;

import com.example.demo.model.Book;
import org.springframework.data.jpa.repository.JpaRepository;

public interface BookRepository extends JpaRepository<Book, Long> {}
```

---

## CRUD Operations

### Save a Book
Create a new `Book` instance and save it to the database.

```java
package com.example.demo;

import com.example.demo.model.Book;
import com.example.demo.repository.BookRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class DatabaseSeeder implements CommandLineRunner {

    @Autowired
    private BookRepository bookRepository;

    @Override
    public void run(String... args) throws Exception {
        Book book = new Book("Spring in Action", "Craig Walls", 45.99);
        bookRepository.save(book);

        System.out.println("Book saved: " + book);
    }
}
```

### Retrieve All Books
Use the `findAll` method to fetch all records.

```java
List<Book> books = bookRepository.findAll();
books.forEach(System.out::println);
```

### Retrieve a Book by ID
Use the `findById` method to retrieve a specific record.

```java
Optional<Book> book = bookRepository.findById(1L);
book.ifPresent(System.out::println);
```

### Update a Book
Retrieve a `Book`, modify its attributes, and save it back.

```java
Optional<Book> optionalBook = bookRepository.findById(1L);
if (optionalBook.isPresent()) {
    Book book = optionalBook.get();
    book.setPrice(49.99);
    bookRepository.save(book);

    System.out.println("Updated Book: " + book);
}
```

### Delete a Book
Delete a record by its ID using the `deleteById` method.

```java
bookRepository.deleteById(1L);
System.out.println("Book deleted.");
```

---

## Conclusion

This guide has introduced the basics of Spring Data JPA and shown how to perform CRUD operations using a `Book` entity. Spring Data JPA abstracts away much of the complexity involved in database interactions, enabling developers to focus on the business logic of their applications.

You can further explore advanced features like custom queries, pagination, and sorting to unlock the full potential of Spring Data JPA.
