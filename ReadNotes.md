# Spring Framework – Dependency Injection and Inversion of Control

## Introduction

Spring Framework is a powerful and widely used open-source application development framework for building enterprise-grade Java applications.

It provides a comprehensive infrastructure that simplifies the development of complex applications by offering a range of features and tools that enable developers to create:

* Scalable applications
* Maintainable applications
* Robust applications

One of the key features of Spring is its **modular architecture**, which allows developers to choose which components they need for their specific projects.

This modular approach makes it easy to:

* Manage dependencies
* Improve code reusability
* Improve maintainability

---

# 1. Core Components of Spring Framework

Spring Framework has a vast ecosystem of concepts, including:

1. Dependency Injection
2. Inversion of Control
3. Aspect Oriented Programming (AOP)
4. Spring Batch Processing
5. Spring Integration
6. Spring Cloud

At its core, Spring Framework is essentially a **Dependency Injection container**, with additional convenience layers such as:

* Database access
* Proxies
* Aspect-Oriented Programming
* RPC
* Web MVC framework

Spring helps developers build Java applications faster and more conveniently.

> In this reading document, we will mainly focus on **Dependency Injection**, **Inversion of Control**, and their implementation. Remaining topics will be covered in upcoming sessions.

---

# 2. Dependency Injection

Dependency Injection is a design pattern commonly used in software development, particularly in Object-Oriented Programming.

The basic idea behind Dependency Injection is:

> Objects should be given their dependencies rather than creating them internally.

In simpler terms, Dependency Injection is a way to **pass objects (dependencies) to a class instead of the class creating them itself**.

This makes the class:

* More modular
* More flexible
* Easier to test
* Less tightly coupled

## Example

Let's say we have two classes:

* `Car`
* `Engine`

The `Car` class depends on the `Engine` class to run.

### Traditional Approach

Traditionally, we would create the `Engine` object inside the `Car` class:

```java
public class Car {

    private Engine engine;

    public Car() {
        this.engine = new Engine();
    }
}
```

However, this approach is inflexible and can make it difficult to test the `Car` class in isolation.

---

## Dependency Injection Approach

Instead, we can use Dependency Injection to pass the `Engine` object to the `Car` class from outside:

```java
public class Car {

    private Engine engine;

    public Car(Engine engine) {
        this.engine = engine;
    }
}
```

In this case, the `Car` class no longer creates the `Engine` object itself.

Instead:

1. The `Engine` object is created outside the `Car` class.
2. The `Engine` object is passed to the `Car` class.
3. The `Car` object receives the dependency through its constructor.

This is known as **Constructor Injection**.

### Creating the Objects

```java
public static void main(String[] args) {

    Engine engine = new Engine();

    Car car = new Car(engine);
}
```

By using Dependency Injection, we have **decoupled** the `Car` and `Engine` classes.

This makes them:

* More modular
* Easier to test
* Easier to maintain

We can also easily:

* Substitute the `Engine` object with a mock object for testing.
* Create different types of engines.
* Use different engine implementations for different cars.

---

# 3. Inversion of Control

In software engineering, **Inversion of Control (IoC)** is a design pattern that allows the control of:

* Object creation
* Flow of control
* Object lifecycle management

to be shifted from the application code to an external framework or container.

In traditional programming:

```text
Application
    ↓
Creates Objects
    ↓
Controls Execution
```

With IoC:

```text
Application
    ↓
IoC Container
    ↓
Creates & Manages Objects
    ↓
Injects Dependencies
```

The flow of control is therefore inverted, and the framework manages the creation and execution of objects.

---

## Components of IoC

The IoC pattern consists of two main components:

### I. Container

The container is responsible for:

* **Creating objects** – The container creates and initializes the objects required by the application.
* **Configuring objects** – The container configures objects by setting their properties and injecting dependencies.
* **Managing object lifecycle** – The container manages the lifecycle of objects, including creating, destroying, and reusing them.
* **Wiring objects together** – The container wires objects together by injecting the dependencies of one object into another.

### II. Components

Components are the objects that provide the functionality of the application.

Components are responsible for:

* Providing functionality
* Declaring dependencies
* Implementing interfaces

---

## Benefits of IoC

The IoC pattern provides several benefits:

### 1. Decoupling of Components

IoC decouples the components of an application, making it easier to replace or modify them without affecting other components.

### 2. Testability

IoC makes it easier to write unit tests for individual components because their dependencies can be easily mocked or stubbed.

### 3. Reusability

IoC promotes the reuse of components by making them more modular and easier to integrate into other applications.

### 4. Scalability

IoC makes it easier to scale an application by allowing the container to manage the creation and lifecycle of objects.

---

## IoC Summary

Inversion of Control is a design pattern that allows the control of object creation, flow of control, and object lifecycle management to be shifted from the application code to an external framework or container.

The major benefits include:

* Decoupling
* Testability
* Reusability
* Scalability

---

# 4. Implementation of IoC Using Dependency Injection

In Spring Framework for Java, IoC is implemented using **Dependency Injection (DI)** through the **Spring IoC Container**.

One example is using `JdbcTemplate` with Constructor Injection.

---

## JdbcTemplate Using Constructor Injection

In one of the previous sessions of `JdbcTemplate`, it was implemented using Constructor Injection.

The service class receives `JdbcTemplate` as a constructor dependency.

```java
@Service
public class BookH2Service implements BookRepository {

    private JdbcTemplate db;

    public BookH2Service(JdbcTemplate db) {
        this.db = db;
    }
}
```

The important concept here is that the `BookH2Service` class does **not create the `JdbcTemplate` object itself**.

Instead, the dependency is provided from an external source.

---

## Without Dependency Injection

The controller could traditionally create the service and its dependency itself:

```java
@RestController
public class BookController {

    private final BookH2Service bookH2Service;

    public BookController() {
        this.bookH2Service = new BookH2Service(new JdbcTemplate());
    }
}
```

Here, the controller is responsible for creating the dependency.

This creates tighter coupling between the classes.

---

## With Dependency Injection

Next, we modify the `BookH2Service` class to use Dependency Injection.

The `BookH2Service` class takes `JdbcTemplate` as a constructor argument instead of creating it itself.

```java
@Service
public class BookH2Service implements BookRepository {

    private JdbcTemplate db;

    public BookH2Service(JdbcTemplate db) {
        this.db = db;
    }
}
```

Now the dependency is supplied externally.

Instead of a module or class controlling the flow of execution, the control is transferred to an external source, usually an **IoC Container**, which manages the objects and their dependencies.

---

# 5. Creating Beans Using `@Configuration` and `@Bean`

To implement IoC, we can create a Spring configuration class that defines the required beans.

Spring provides two important annotations:

* `@Configuration`
* `@Bean`

## `@Configuration`

The `@Configuration` annotation indicates that a class contains Spring configuration metadata.

This metadata is used by the Spring Container to create and manage beans.

Example:

```java
@Configuration
public class AppConfig {

}
```

---

## `@Bean`

A `@Bean` is a Spring-managed object that can be used throughout the application.

Spring-managed objects are:

* Created
* Injected
* Managed throughout their lifecycle

The `@Bean` annotation is used to indicate that a method inside a Spring configuration class should be used to create and configure a bean.

Example:

```java
@Configuration
public class AppConfig {

    @Bean
    public DataSource dataSource() {

        HikariConfig config = new HikariConfig();

        config.setJdbcUrl(
            "jdbc:h2:file:~/Workspace/content/java-spring-code-samples/spring-boot/src/main/resources/booksdb"
        );

        return new HikariDataSource(config);
    }

    @Bean
    public JdbcTemplate jdbcTemplate() {

        return new JdbcTemplate(dataSource());
    }
}
```

---

# 6. How Spring Manages Beans

When Spring starts up, it reads the configuration classes and creates beans based on the bean definitions.

The general process is:

```text
Spring Application Starts
          ↓
Reads @Configuration
          ↓
Finds @Bean Methods
          ↓
Creates Bean Objects
          ↓
Initializes Beans
          ↓
Resolves Dependencies
          ↓
Injects Dependencies
          ↓
Application Components Use Beans
```

Each bean is created and initialized before it is injected into dependent objects.

Once all the beans have been created and initialized, Spring creates instances of dependent objects and injects the required beans into them.

This includes **Constructor Injection**, where Spring resolves the constructor arguments and injects the appropriate beans.

---

# 7. Complete Dependency Flow

The dependency flow can be understood as:

```text
BookH2Service
      |
      | requires
      ↓
JdbcTemplate
      |
      | requires
      ↓
DataSource
      |
      ↓
HikariDataSource
```

Spring manages this dependency chain through the IoC Container.

For example:

```text
@Configuration
     |
     +---- @Bean DataSource
     |
     +---- @Bean JdbcTemplate
                    |
                    ↓
             BookH2Service
```

The application code does not need to manually create every dependency.

Instead, Spring creates and manages the required objects.

---

# 8. Important Annotations

| Annotation        | Purpose                              |
| ----------------- | ------------------------------------ |
| `@Configuration`  | Defines a Spring configuration class |
| `@Bean`           | Creates a Spring-managed bean        |
| `@Service`        | Marks a class as a service component |
| `@RestController` | Marks a class as a REST controller   |

---

# 9. Dependency Injection vs Inversion of Control

| Dependency Injection                   | Inversion of Control                              |
| -------------------------------------- | ------------------------------------------------- |
| A technique for providing dependencies | A broader design principle                        |
| Dependencies are supplied from outside | Control is transferred to the framework/container |
| Helps reduce tight coupling            | Helps manage objects and their lifecycle          |
| Can be implemented using constructors  | Spring implements IoC using Dependency Injection  |

### Simple Relationship

```text
Inversion of Control
        ↓
Dependency Injection
        ↓
Spring IoC Container
        ↓
Creates + Manages + Injects Objects
```

---

# 10. Key Takeaways

### Dependency Injection

> Instead of a class creating its own dependencies, dependencies are provided from outside.

Example:

```java
public Car(Engine engine) {
    this.engine = engine;
}
```

### Inversion of Control

> Instead of application code controlling object creation and lifecycle, the Spring Container manages them.

### `@Configuration`

> Defines configuration information for Spring.

### `@Bean`

> Tells Spring to create and manage an object.

### Constructor Injection

> Dependencies are provided through a class constructor.

---

# 11. Summary

Overall, the `@Configuration` annotation is an important part of Spring's configuration model and is used to create and manage beans that make up a Spring application.

In this example, we use the `@Bean` annotation to create instances of:

* `JdbcTemplate`
* `DataSource`

We are using **Inversion of Control** because we allow the Spring Container to manage the creation and injection of these objects instead of creating them ourselves inside the `BookH2Service` class.

When `JdbcTemplate` is required by `BookH2Service`, Spring checks the available bean definitions in the `AppConfig` class and provides the required bean.

The `JdbcTemplate` bean depends on the `DataSource` bean.

Therefore, Spring:

1. Creates the `DataSource`.
2. Creates the `JdbcTemplate` using the `DataSource`.
3. Creates the `BookH2Service`.
4. Injects the `JdbcTemplate` into `BookH2Service`.

This allows dependencies to be managed by Spring without changing the main business logic.

---

# 12. Final Concept

The complete concept can be remembered as:

```text
              SPRING FRAMEWORK
                     |
                     ↓
          ┌─────────────────────┐
          │   IoC Container     │
          └─────────────────────┘
                     |
          Creates & Manages Beans
                     |
                     ↓
          ┌─────────────────────┐
          │    DataSource       │
          └─────────────────────┘
                     |
                     ↓
          ┌─────────────────────┐
          │    JdbcTemplate     │
          └─────────────────────┘
                     |
              Dependency
               Injection
                     |
                     ↓
          ┌─────────────────────┐
          │   BookH2Service     │
          └─────────────────────┘
```

### In one line:

> **Spring IoC Container creates and manages objects, while Dependency Injection provides those objects to the classes that need them.**
