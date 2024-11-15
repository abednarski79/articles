---
layout: post
title: Using MongoTemplate in Spring for Basic CRUD Operations
date: 2024-05-15
tags: Mongo  MongoTemplate Spring
---

## Introduction

The **MongoTemplate** class in Spring Data MongoDB is a powerful tool for interacting with MongoDB databases. 
It provides low-level access to MongoDB, enabling advanced queries and fine-grained control over database operations. 
While **MongoRepository** is easier for standard use cases, **MongoTemplate** is ideal for scenarios where more flexibility is required.

This article explains how to perform basic CRUD (Create, Read, Update, Delete) operations using **MongoTemplate** in a Spring application.

## Setting Up the Project

#### 1. Add Dependencies

To use Spring Data MongoDB, include the following dependency in your **pom.xml**:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

#### 2. Configure MongoDB Connection

In the **application.properties** or **application.yml**, configure the MongoDB connection details:

```properties
spring.data.mongodb.uri=mongodb://localhost:27017/mydatabase
```

### Creating the Model Class

Let's create a simple model class for demonstration purposes.

```java
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;

@Document(collection = "users")
public class User {

    @Id
    private String id;
    private String name;
    private String email;
    private int age;

    // Constructors, Getters, and Setters
    public User() {}

    public User(String name, String email, int age) {
        this.name = name;
        this.email = email;
        this.age = age;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

### CRUD Operations Using MongoTemplate

#### 1. Create or Save Documents

Use **MongoTemplate**'s **save()** method to create or update a document.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    @Autowired
    private MongoTemplate mongoTemplate;

    public User createUser(User user) {
        return mongoTemplate.save(user);
    }
}
```

**Example Usage:**

```java
User newUser = new User("John Doe", "john.doe@example.com", 30);
userService.createUser(newUser);
```

#### 2. Read Documents

**a. Find All Documents**

Use **findAll()** to retrieve all documents from a collection.

```java
import java.util.List;

public List<User> getAllUsers() {
    return mongoTemplate.findAll(User.class);
}
```

**b. Find by ID**

Use **findById()** to retrieve a document by its ID.

```java
public User getUserById(String id) {
    return mongoTemplate.findById(id, User.class);
}
```

**c. Query with Criteria**

Use the **Query** and **Criteria** classes to perform custom queries.

```java
import org.springframework.data.mongodb.core.query.Criteria;
import org.springframework.data.mongodb.core.query.Query;

public User getUserByEmail(String email) {
    Query query = new Query();
    query.addCriteria(Criteria.where("email").is(email));
    return mongoTemplate.findOne(query, User.class);
}
```

#### 3. Update Documents

Use **updateFirst()** or **updateMulti()** for updates.

```java
import org.springframework.data.mongodb.core.query.Update;

public User updateUserAge(String email, int newAge) {
    Query query = new Query();
    query.addCriteria(Criteria.where("email").is(email));
    
    Update update = new Update();
    update.set("age", newAge);
    
    mongoTemplate.updateFirst(query, update, User.class);
    return getUserByEmail(email);
}
```

#### 4. Delete Documents

**a. Delete by ID**
Use **remove()** to delete a document by its ID.

```java
public void deleteUserById(String id) {
    Query query = new Query();
    query.addCriteria(Criteria.where("id").is(id));
    mongoTemplate.remove(query, User.class);
}
```
**b. Delete All Documents**
Use **remove()** with an empty Query to delete all documents.

```java
public void deleteAllUsers() {
    mongoTemplate.remove(new Query(), User.class);
}
```

### Full Example Service Class

Here’s the complete service class with all CRUD operations:

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.data.mongodb.core.query.Criteria;
import org.springframework.data.mongodb.core.query.Query;
import org.springframework.data.mongodb.core.query.Update;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class UserService {

    @Autowired
    private MongoTemplate mongoTemplate;

    public User createUser(User user) {
        return mongoTemplate.save(user);
    }

    public List<User> getAllUsers() {
        return mongoTemplate.findAll(User.class);
    }

    public User getUserById(String id) {
        return mongoTemplate.findById(id, User.class);
    }

    public User getUserByEmail(String email) {
        Query query = new Query();
        query.addCriteria(Criteria.where("email").is(email));
        return mongoTemplate.findOne(query, User.class);
    }

    public User updateUserAge(String email, int newAge) {
        Query query = new Query();
        query.addCriteria(Criteria.where("email").is(email));
        Update update = new Update();
        update.set("age", newAge);
        mongoTemplate.updateFirst(query, update, User.class);
        return getUserByEmail(email);
    }

    public void deleteUserById(String id) {
        Query query = new Query();
        query.addCriteria(Criteria.where("id").is(id));
        mongoTemplate.remove(query, User.class);
    }

    public void deleteAllUsers() {
        mongoTemplate.remove(new Query(), User.class);
    }
}
```

## Testing the Application

You can use Spring Boot's REST controller or unit tests to validate the functionality:

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService userService;

    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.createUser(user);
    }

    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }

    @GetMapping("/{id}")
    public User getUserById(@PathVariable String id) {
        return userService.getUserById(id);
    }

    @PutMapping("/{email}/age")
    public User updateUserAge(@PathVariable String email, @RequestParam int age) {
        return userService.updateUserAge(email, age);
    }

    @DeleteMapping("/{id}")
    public void deleteUserById(@PathVariable String id) {
        userService.deleteUserById(id);
    }
}
```

## Conclusion

Using **MongoTemplate**, you can implement fine-grained control over MongoDB operations in a Spring application. It provides flexibility for crafting custom queries and updates, making it a great choice for advanced use cases. For simpler applications, consider using MongoRepository for convenience.
