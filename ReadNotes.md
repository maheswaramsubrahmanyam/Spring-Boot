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

---
---
# Spring Batch Processing

## Introduction

In our previous sessions, we discussed Spring Boot and an overview of the Spring Framework.

In this session, we will discuss in detail:

* What is Spring Batch Processing?
* Why do we use Spring Batch?
* Core concepts of Spring Batch
* How to implement Spring Batch
* How to process CSV data and store it in an H2 database

---

## Definition

**Spring Batch** is a lightweight open-source framework for batch processing in Java.

It provides a set of reusable components and patterns to simplify the development of batch applications.

Spring Batch enables developers to build robust and scalable batch applications with minimal effort.

It supports various types of batch processing, including:

* Data processing
* ETL (Extract, Transform, Load)
* Batch jobs

The framework provides features such as:

* Parallel processing
* Error handling
* Transaction management
* Job repository
* Batch processing metadata

Spring Batch also offers various job repository implementations, including:

* In-memory
* JDBC
* JPA

Spring Batch can be integrated with other Spring projects such as:

* Spring Boot
* Spring Integration

### Simple Definition

In simple words:

> **Spring Batch is a framework for building applications that process large volumes of data in batches, usually offline.**

Batch processing is commonly used for tasks such as:

* Data migration
* Data cleansing
* Generating reports
* Processing large files
* Importing data into databases

---

# 1. Why Spring Batch?

Let's consider a common enterprise problem.

Suppose an organization receives data in a `.csv` file and wants to insert all the data into an **H2 database**.

There are two ways to solve this problem.

### Approach 1: Plain Java

We can write normal Java code that:

1. Reads the CSV file.
2. Processes each line.
3. Connects to the H2 database.
4. Inserts the data using SQL.
5. Handles exceptions using `try-catch`.

A typical implementation would use:

* `BufferedReader`
* `FileReader`
* `DriverManager`
* `Connection`
* `PreparedStatement`

### Problems with Plain Java

For large-scale batch processing, plain Java can create several challenges:

#### 1. Performance

If the CSV file is large, processing it using plain Java can result in performance issues.

Spring Batch provides optimization techniques such as:

* Chunk processing
* Parallel processing

#### 2. Scalability

Plain Java code may not be scalable enough to handle large-scale batch processing.

Spring Batch provides a modular architecture that allows batch processing applications to scale based on requirements.

#### 3. Reliability

Plain Java batch processing may not provide built-in mechanisms for:

* Error handling
* Transaction management
* Job restartability

Spring Batch provides mechanisms to handle these requirements.

#### 4. Maintainability

Large batch-processing applications written entirely using plain Java can become difficult to maintain and extend.

Spring Batch provides reusable components and patterns that make batch applications easier to develop and maintain.

---

# 2. Core Concepts of Spring Batch

Spring Batch provides several important components for building batch applications.

## 1. Job

A **Job** is a collection of steps that are executed in a specific sequence.

Jobs can:

* Be started
* Be stopped
* Be restarted

If a job is interrupted, it can be restarted from where it left off.

---

## 2. Step

A **Step** is a single unit of work that is part of a Job.

A step can perform tasks such as:

* Reading data
* Processing data
* Writing data

---

## 3. ItemReader

`ItemReader` is responsible for reading data from a data source.

For example:

* CSV file
* Database
* Other input sources

It reads one item at a time and passes it to the `ItemProcessor`.

---

## 4. ItemProcessor

`ItemProcessor` processes each item received from the `ItemReader`.

It can:

* Transform data
* Validate data
* Filter data

The processed data is then passed to the `ItemWriter`.

---

## 5. ItemWriter

`ItemWriter` is responsible for writing processed data to a target data source.

For example:

* Database
* File
* Other output destinations

---

## 6. JobRepository

`JobRepository` stores information about:

* Jobs
* Job executions
* Execution status
* Batch processing metadata

It helps track the progress of a job and supports restarting interrupted jobs.

---

## 7. JobLauncher

`JobLauncher` is responsible for starting a Spring Batch Job.

---

## 8. Listener

Listeners can be attached to:

* Jobs
* Steps
* Individual items

They can be used to:

* Log processing information
* Send notifications
* Perform cleanup
* Execute custom actions

---

# 3. Spring Batch Architecture

The basic Spring Batch processing flow can be represented as:

```text
                 Spring Batch Job
                        |
                        ↓
                      Step
                        |
             ┌──────────┼──────────┐
             ↓          ↓          ↓
         ItemReader  ItemProcessor  ItemWriter
             |          |             |
             ↓          ↓             ↓
          CSV File   Process Data   Database
```

The basic idea is:

```text
Read → Process → Write
```

---

# 4. Implementation of Spring Batch

Let's implement Spring Batch for the following problem:

> Read user information from a CSV file, process it, and store the data in an H2 database.

---

## 4.1 Spring Batch Environment Setup

Add the following dependencies to your `pom.xml` file.

```xml
<dependencies>

    <dependency>
        <groupId>org.springframework.batch</groupId>
        <artifactId>spring-batch-core</artifactId>
        <version>5.0.0</version>
    </dependency>

    <dependency>
        <groupId>org.springframework.batch</groupId>
        <artifactId>spring-batch-infrastructure</artifactId>
        <version>${spring.batch.version}</version>
    </dependency>

</dependencies>
```

These dependencies provide the required Spring Batch components.

---

# 5. CSV File

Suppose our CSV file contains the following data:

```csv
id,name,firstname,lastname
1,Alexine,Dari,Nisbet
2,Sonni,Kary,Daveta
3,Cacilie,Livvyy,Breed
4,Donetta,Vinita,Raychel
5,Merle,Hope,Koehler
6,Hermione,Dorice,Gusella
7,Charissa,Rani,Sinegold
```

The CSV contains four columns:

| Column      | Description |
| ----------- | ----------- |
| `id`        | Employee ID |
| `name`      | Name        |
| `firstname` | First name  |
| `lastname`  | Last name   |

---

# 6. User Model

Let's create a `User` class that represents each row of the CSV file.

```java
public class User {

    private Integer empId;

    private String name;

    private String firstname;

    private String lastname;

    // Constructors

    // Getters and Setters
}
```

Each CSV row can now be represented as a `User` object.

---

# 7. Job

A Spring Batch Job is a collection of one or more steps.

Example:

```java
@Autowired
public JobBuilderFactory jobBuilderFactory;

@Bean
public Job importUserJob() {

    return this.jobBuilderFactory
            .get("UserUpdateJob")
            .incrementer(new RunIdIncrementer())
            .flow(step1())
            .end()
            .build();
}
```

### Explanation

#### `@Autowired`

```java
@Autowired
public JobBuilderFactory jobBuilderFactory;
```

`@Autowired` is used to inject an instance of `JobBuilderFactory`.

The `JobBuilderFactory` is used to create `Job` instances.

---

### `get()`

```java
this.jobBuilderFactory.get("UserUpdateJob")
```

This creates a new Job instance with the name:

```text
UserUpdateJob
```

---

### `RunIdIncrementer`

```java
.incrementer(new RunIdIncrementer())
```

This generates unique job instances.

---

### `flow()`

```java
.flow(step1())
```

This adds the `step1()` step to the Job flow.

---

### `end()`

```java
.end()
```

This ends the Job flow.

---

### `build()`

```java
.build()
```

This builds and returns the configured Job instance.

---

## Job Flow

The Job can be visualized as:

```text
UserUpdateJob
      |
      ↓
    Step1
      |
      ↓
     End
```

---

# 8. Step

A Step represents a unit of work inside a Job.

Example:

```java
@Autowired
public StepBuilderFactory stepBuilderFactory;

@Bean
public Step step1() {

    return this.stepBuilderFactory
            .get("Step1")
            .<User, User>chunk(1)
            .reader(userReader())
            .processor(processor())
            .writer(writer())
            .build();
}
```

A Step mainly contains three important components:

```text
Step
 |
 ├── ItemReader
 |
 ├── ItemProcessor
 |
 └── ItemWriter
```

---

## Chunk

The `chunk()` method specifies the size of the chunk processed by the step.

Example:

```java
.<User, User>chunk(1)
```

Here:

* Input type = `User`
* Output type = `User`
* Chunk size = `1`

The `<User, User>` part represents:

```text
Input Type  → User
Output Type → User
```

---

# 9. Reader

An `ItemReader` reads data from an input source and provides it to the processor.

In our example, the reader reads data from a CSV file.

```java
@Bean
public FlatFileItemReader<User> userReader() {

    FlatFileItemReader<User> reader =
            new FlatFileItemReader<>();

    reader.setResource(
            new ClassPathResource("users.csv")
    );

    reader.setLineMapper(
        new DefaultLineMapper<User>() {{

            setLineTokenizer(
                new DelimitedLineTokenizer() {{

                    setNames(
                        new String[] {
                            "empId",
                            "name",
                            "firstname",
                            "lastname"
                        }
                    );

                }}
            );

            setFieldSetMapper(
                new BeanWrapperFieldSetMapper<User>() {{

                    setTargetType(User.class);

                }}
            );

        }}
    );

    return reader;
}
```

---

## Reader Explanation

### `@Bean`

```java
@Bean
public FlatFileItemReader<User> userReader()
```

This defines a Spring-managed bean that creates a `FlatFileItemReader`.

---

### Creating the Reader

```java
FlatFileItemReader<User> reader =
        new FlatFileItemReader<>();
```

This creates the reader responsible for reading `User` objects.

---

### Setting the CSV File

```java
reader.setResource(
        new ClassPathResource("users.csv")
);
```

This tells Spring Batch to read the file:

```text
users.csv
```

from the application classpath.

---

# 10. Line Mapper

The `LineMapper` maps each CSV line to a `User` object.

```java
reader.setLineMapper(
    new DefaultLineMapper<User>() {{

        setLineTokenizer(
            new DelimitedLineTokenizer() {{

                setNames(
                    new String[] {
                        "empId",
                        "name",
                        "firstname",
                        "lastname"
                    }
                );

            }}
        );

        setFieldSetMapper(
            new BeanWrapperFieldSetMapper<User>() {{

                setTargetType(User.class);

            }}
        );

    }}
);
```

The `DelimitedLineTokenizer` separates each CSV line based on delimiters.

For example:

```text
1,Alexine,Dari,Nisbet
```

is divided into:

```text
empId     → 1
name      → Alexine
firstname → Dari
lastname  → Nisbet
```

The `BeanWrapperFieldSetMapper` maps these fields to the properties of the `User` class.

---

## Skipping the Header

The first line of the CSV contains column names.

We can skip it using:

```java
reader.setLinesToSkip(1);
```

This prevents the header from being processed as a `User` object.

---

# 11. Processor

The `ItemProcessor` processes the data read by the reader.

Example:

```java
public class UserItemProcessor
        implements ItemProcessor<User, User> {

    @Override
    public User process(User user) throws Exception {

        String name =
                user.getName().toUpperCase();

        String lastname =
                user.getLastname().toUpperCase();

        return new User(
                user.getEmpId(),
                name,
                user.getFirstname(),
                lastname
        );
    }
}
```

The processor converts the `name` and `lastname` values to uppercase.

### Example

Input:

```text
Alexine,Dari,Nisbet
```

After processing:

```text
ALEXINE,Dari,NISBET
```

The processor receives:

```text
User
```

and returns:

```text
User
```

Therefore:

```text
ItemProcessor<User, User>
```

means:

```text
Input  → User
Output → User
```

---

# 12. Writer

The `ItemWriter` receives the processed data and writes it to the database.

Example:

```java
@Bean
public JdbcBatchItemWriter<User> writer() {

    JdbcBatchItemWriter<User> writer =
            new JdbcBatchItemWriter<User>();

    writer.setItemSqlParameterSourceProvider(
            new BeanPropertyItemSqlParameterSourceProvider<User>()
    );

    writer.setSql(
        "INSERT INTO Users " +
        "(empId, name, firstname, lastname) " +
        "VALUES " +
        "(:empId, :name, :firstname, :lastname)"
    );

    writer.setDataSource(this.dataSource);

    return writer;
}
```

---

## `setItemSqlParameterSourceProvider()`

```java
writer.setItemSqlParameterSourceProvider(
    new BeanPropertyItemSqlParameterSourceProvider<User>()
);
```

This creates SQL parameters from the properties of the `User` object.

For example:

```text
user.getEmpId()
user.getName()
user.getFirstname()
user.getLastname()
```

can be mapped to:

```text
:empId
:name
:firstname
:lastname
```

---

## `setSql()`

The SQL query is defined using:

```java
writer.setSql(
    "INSERT INTO Users " +
    "(empId, name, firstname, lastname) " +
    "VALUES " +
    "(:empId, :name, :firstname, :lastname)"
);
```

The data is inserted into the `Users` table.

---

## `setDataSource()`

```java
writer.setDataSource(this.dataSource);
```

This configures the database connection used by the writer.

---

# 13. H2 Database Setup

We need a `schema.sql` file to create the database table if it does not already exist.

## `schema.sql`

```sql
CREATE TABLE IF NOT EXISTS Users (

    empId INT PRIMARY KEY NOT NULL,

    name VARCHAR(256),

    firstname VARCHAR(256),

    lastname VARCHAR(256)

);
```

This creates the `Users` table with four columns:

```text
empId
name
firstname
lastname
```

---

# 14. application.properties

Add the following configuration to `application.properties`:

```properties
spring.datasource.url=jdbc:h2:file:./spring-boot/src/main/resources/BatchProcessing/db

spring.h2.console.path=/h2-console

spring.datasource.driver-class-name=org.h2.Driver

spring.batch.jdbc.initialize-schema=always

spring.h2.console.enabled=true

spring.sql.init.mode=always
```

These properties configure:

* H2 database
* H2 console
* H2 JDBC driver
* Spring Batch database schema
* SQL initialization

---

# 15. Complete Spring Batch Flow

The complete application flow is:

```text
                    CSV FILE
                       |
                       ↓
                ┌──────────────┐
                │ ItemReader   │
                └──────────────┘
                       |
                       ↓
                ┌──────────────┐
                │ItemProcessor │
                └──────────────┘
                       |
                       ↓
                ┌──────────────┐
                │  ItemWriter  │
                └──────────────┘
                       |
                       ↓
                  H2 DATABASE
```

In simple terms:

```text
CSV
 ↓
Read
 ↓
Process
 ↓
Write
 ↓
Database
```

---

# 16. Complete Step Configuration

The Step connects the Reader, Processor, and Writer together:

```java
@Bean
public Step step1() {

    return this.stepBuilderFactory
            .get("Step1")
            .<User, User>chunk(1)
            .reader(userReader())
            .processor(processor())
            .writer(writer())
            .build();
}
```

The processing flow is:

```text
ItemReader
    ↓
User Object
    ↓
ItemProcessor
    ↓
Processed User Object
    ↓
ItemWriter
    ↓
Database
```

---

# 17. Complete Job Configuration

The Job contains the Step:

```java
@Bean
public Job importUserJob() {

    return this.jobBuilderFactory
            .get("UserUpdateJob")
            .incrementer(new RunIdIncrementer())
            .flow(step1())
            .end()
            .build();
}
```

The overall Job flow is:

```text
UserUpdateJob
      |
      ↓
    Step1
      |
      ↓
   Reader
      |
      ↓
  Processor
      |
      ↓
   Writer
      |
      ↓
 H2 Database
```

---

# 18. Main Components Used

The configuration includes the following important components.

| Component                   | Purpose                        |
| --------------------------- | ------------------------------ |
| `DataSource`                | Provides database connectivity |
| `JobBuilderFactory`         | Creates Spring Batch Jobs      |
| `StepBuilderFactory`        | Creates Spring Batch Steps     |
| `FlatFileItemReader<User>`  | Reads users from CSV           |
| `UserItemProcessor`         | Processes User objects         |
| `JdbcBatchItemWriter<User>` | Writes users to database       |
| `Job`                       | Defines the batch job          |
| `Step`                      | Defines a unit of work         |
| `RunIdIncrementer`          | Generates unique job instances |

---

# 19. Spring Batch Architecture Summary

The complete architecture can be visualized as:

```text
                         SPRING BATCH
                              |
                              ↓
                           JOB
                              |
                              ↓
                           STEP
                              |
              ┌───────────────┼───────────────┐
              ↓               ↓               ↓
          ItemReader     ItemProcessor     ItemWriter
              |               |               |
              ↓               ↓               ↓
          users.csv       Transform Data    H2 Database
```

---

# 20. How the Application Works

The complete processing sequence is:

### Step 1 — Job Starts

Spring Batch starts the `UserUpdateJob`.

```text
UserUpdateJob
```

### Step 2 — Step Executes

The Job executes:

```text
Step1
```

### Step 3 — Reader Reads CSV

The `ItemReader` reads the `users.csv` file.

```text
users.csv
    ↓
ItemReader
```

### Step 4 — User Object Is Created

Each CSV row is converted into a:

```text
User
```

object.

### Step 5 — Processor Processes Data

The `ItemProcessor` modifies the data.

For example:

```text
Alexine → ALEXINE
Nisbet  → NISBET
```

### Step 6 — Writer Stores Data

The `ItemWriter` executes the SQL query and stores the processed data in the H2 database.

```text
ItemWriter
    ↓
Users Table
```

---

# 21. Final Data Flow

```text
┌──────────────────┐
│    users.csv     │
└────────┬─────────┘
         │
         ↓
┌──────────────────┐
│   ItemReader     │
│                  │
│ Reads CSV Data   │
└────────┬─────────┘
         │
         ↓
┌──────────────────┐
│ ItemProcessor    │
│                  │
│ Processes Data   │
└────────┬─────────┘
         │
         ↓
┌──────────────────┐
│   ItemWriter     │
│                  │
│ Writes to DB     │
└────────┬─────────┘
         │
         ↓
┌──────────────────┐
│   H2 Database    │
│                  │
│   Users Table    │
└──────────────────┘
```

---

# 22. Summary

Spring Batch provides reusable components and patterns for building reliable and scalable batch-processing applications.

The main components are:

```text
Job
 ↓
Step
 ↓
Reader
 ↓
Processor
 ↓
Writer
```

For our example:

```text
CSV File
   ↓
ItemReader
   ↓
User Object
   ↓
ItemProcessor
   ↓
Processed User
   ↓
JdbcBatchItemWriter
   ↓
H2 Database
```

Spring Batch makes it easier to build applications that process large amounts of data by providing features such as:

* Batch processing
* Chunk processing
* Error handling
* Transaction management
* Job repositories
* Job restartability
* Reusable components

---

# 23. Key Takeaways

### Spring Batch

> A framework for processing large amounts of data in batches.

### Job

> A collection of steps that execute as part of a batch process.

### Step

> A single unit of work inside a Job.

### ItemReader

> Reads data from an input source.

### ItemProcessor

> Processes or transforms the input data.

### ItemWriter

> Writes processed data to an output source.

### JobRepository

> Stores job execution and batch metadata.

### JobLauncher

> Starts a Spring Batch Job.

### Listener

> Performs custom actions during different stages of batch processing.

---

# 24. Final Concept

The complete Spring Batch concept can be remembered as:

```text
             SPRING BATCH
                  |
                  ↓
                 JOB
                  |
                  ↓
                STEP
                  |
        ┌─────────┼─────────┐
        ↓         ↓         ↓
      READ     PROCESS    WRITE
        ↓         ↓         ↓
       CSV       DATA       DB
```

### In one line:

> **Spring Batch reads large amounts of data, processes it efficiently, and writes the processed data to a destination such as a database or file.**
---
---
# Spring AOP - Aspect Oriented Programming

## Introduction

In this tutorial, we will learn about **Aspect Oriented Programming (AOP)** and its important concepts with practical code examples.

Java applications are commonly developed in multiple layers to:

* Improve security
* Separate business logic
* Separate persistence logic
* Improve maintainability

A typical Java application has three main layers:

### Web Layer

The **Web Layer** provides services to end users through:

* REST APIs
* Web applications

### Business Layer

The **Business Layer** contains and handles the application's business logic.

### Data Layer

The **Data Layer** is responsible for implementing persistence logic.

---

# 1. What is AOP?

**Aspect Oriented Programming (AOP)** is a programming paradigm whose main aim is to increase modularity by separating **cross-cutting concerns**.

AOP allows developers to add additional behavior to existing code **without modifying the original code directly**.

For example, we can add:

* Logging
* Security
* Performance monitoring
* Error handling
* Transaction-related behavior

without changing the main business logic.

---

# 2. What are Cross-Cutting Concerns?

Imagine you are building a tower using LEGO blocks.

Some things affect only one particular block, while other things affect the **entire tower**.

For example:

* Making the tower stronger
* Adding lights
* Adding safety features

These concerns affect the whole tower rather than a single block.

Similarly, in software applications, some functionalities affect many different parts of the application.

These are called **Cross-Cutting Concerns**.

Examples include:

* Security
* Logging
* Performance monitoring
* Error handling
* Transactions

Instead of writing the same code repeatedly in different classes, AOP allows us to keep these concerns in a separate module.

---

# 3. Aspect

AOP allows us to define common cross-cutting concerns in a separate class.

This class is called an **Aspect**.

The Aspect contains the functionality that we want to apply to other methods without modifying those methods directly.

For example:

```text
Main Application Code
        |
        ↓
EmployeeService
        |
        ↓
createEmployee()
```

We can add logging externally:

```text
             EmployeeService
                    |
                    ↓
             createEmployee()
                    ↑
                    |
              Logging Aspect
```

The original `EmployeeService` does not need to contain the logging code.

---

# 4. Why Aspect Oriented Programming?

AOP is not only a Spring feature. It is a programming model used to separate cross-cutting concerns from the main application logic.

Consider a service method that starts a car engine.

Suppose we want to:

1. Print the fuel level before starting the engine.
2. Run the engine for 30 minutes.
3. Print the fuel level after the engine stops.

Instead of modifying the original engine method every time we want to add such functionality, we can create an external Aspect.

```text
Before Method
      ↓
   Aspect
      ↓
Main Method
      ↓
   Aspect
      ↓
After Method
```

This keeps the main business logic clean and reduces tight coupling.

---

# 5. Core Concepts of AOP

There are four important core concepts of AOP:

1. **Aspect**
2. **Join Point**
3. **Advice**
4. **Pointcut**

---

# 6. Aspect

An **Aspect** is a module that contains cross-cutting concerns by combining:

* Advice
* Pointcuts

An application can contain multiple aspects.

To define an Aspect in Spring, we use:

```java
@Aspect
```

Example:

```java
@Aspect
@Component
public class EmployeeAspect {

}
```

In simple words:

> **Aspect = A class that contains common functionality which needs to be applied to other methods.**

For example:

* Logging
* Security
* Performance monitoring

---

# 7. Join Point

A **Join Point** is a point in the application where an Aspect can be applied.

Spring AOP primarily supports **method execution join points**, meaning the execution of methods on Spring-managed beans.

For example:

```text
EmployeeService
      |
      ↓
createEmployee()
      ↑
      |
 Join Point
```

The execution of `createEmployee()` can be a Join Point where an Aspect is applied.

### Simple Definition

> **Join Point = A point in the program where additional behavior can be applied.**

---

# 8. Advice

**Advice** is the action performed by an Aspect at a particular Join Point.

In simple words:

> **Advice = The code/functionality that you want to execute before, after, or around a method.**

Spring AOP provides five major types of Advice:

1. Before
2. After
3. After Returning
4. After Throwing
5. Around

---

## 8.1 Before Advice

`@Before` executes the advice **before** the target method executes.

```java
@Before("execution(* com.example.service.EmployeeService.*(..))")
public void beforeAdvice() {

    System.out.println("Before method execution");
}
```

Flow:

```text
@Before Advice
      ↓
Target Method
```

---

## 8.2 After Advice

`@After` executes after the target method completes.

```java
@After("execution(* com.example.service.EmployeeService.*(..))")
public void afterAdvice() {

    System.out.println("After method execution");
}
```

Flow:

```text
Target Method
      ↓
@After Advice
```

---

## 8.3 After Returning Advice

`@AfterReturning` executes after a method successfully completes without throwing an exception.

```java
@AfterReturning(
    "execution(* com.example.service.EmployeeService.*(..))"
)
public void afterReturningAdvice() {

    System.out.println("Method executed successfully");
}
```

Flow:

```text
Target Method
      |
      ↓
Successfully Completed
      |
      ↓
@AfterReturning Advice
```

---

## 8.4 After Throwing Advice

`@AfterThrowing` executes when the target method throws an exception.

```java
@AfterThrowing(
    "execution(* com.example.service.EmployeeService.*(..))"
)
public void afterThrowingAdvice() {

    System.out.println("Method execution failed");
}
```

Flow:

```text
Target Method
      |
      ↓
Exception
      |
      ↓
@AfterThrowing Advice
```

It can be useful for implementing common exception-related logging or handling.

---

## 8.5 Around Advice

`@Around` surrounds the target method execution.

It can execute code:

* Before the method
* After the method
* Instead of the method, depending on how it is implemented

A proper `@Around` advice normally uses `ProceedingJoinPoint`.

Example:

```java
@Around("execution(* com.example.service.EmployeeService.*(..))")
public Object aroundAdvice(ProceedingJoinPoint joinPoint)
        throws Throwable {

    System.out.println("Before method execution");

    Object result = joinPoint.proceed();

    System.out.println("After method execution");

    return result;
}
```

Flow:

```text
        @Around Advice
              |
              ↓
     Before Method
              |
              ↓
        Target Method
              |
              ↓
      After Method
              |
              ↓
        Return Result
```

---

# 9. Pointcut

A **Pointcut** is a predicate or expression that identifies the Join Points where an Advice should be applied.

In simple words:

> **Pointcut = Defines which methods should be intercepted by an Aspect.**

Example:

```java
execution(* com.example.service.EmployeeService.*(..))
```

This expression can be used to match methods in `EmployeeService`.

---

## Pointcut Example

```java
@Before(
    "execution(* com.example.service.EmployeeService.*(..))"
)
public void beforeAdvice() {

    System.out.println("Before EmployeeService method");
}
```

Here:

```text
execution(...)
```

defines the method execution pattern.

The `*` can represent any matching return type or method name depending on its position.

---

# 10. Aspect + Advice + Join Point + Pointcut

The relationship between the concepts can be remembered as:

```text
                  ASPECT
                     |
          ┌──────────┴──────────┐
          ↓                     ↓
      POINTCUT                ADVICE
          |                     |
          ↓                     ↓
 Selects Join Points      What to Execute
          |
          ↓
     JOIN POINT
          |
          ↓
    Target Method
```

In simple words:

```text
Aspect
  ↓
Pointcut → Which method?
  ↓
Advice   → What should happen?
  ↓
Join Point → Where it happens?
```

---

# 11. Implementation of Spring AOP

Let's create a simple Spring Boot Employee application.

The application will contain:

* Employee Model
* Employee Controller
* Employee Service
* Employee Aspect
* Spring Boot Application

The application will provide REST APIs for creating and deleting Employee objects.

---

# 12. Add AOP Dependencies

Add the following dependencies to `pom.xml`:

```xml
<dependencies>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-aop</artifactId>
    </dependency>

</dependencies>
```

The important dependency here is:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

This enables Spring AOP functionality.

---

# 13. Employee Model

Create the `Employee` model class.

```java
package com.javainuse.model;

public class Employee {

    private String empId;

    private String name;

    public Employee() {
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmpId() {
        return empId;
    }

    public void setEmpId(String empId) {
        this.empId = empId;
    }
}
```

The `Employee` class contains:

* `empId`
* `name`

---

# 14. Employee Controller

The Controller exposes REST endpoints for the Employee service.

```java
package com.javainuse.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import com.javainuse.model.Employee;
import com.javainuse.service.EmployeeService;

@RestController
public class EmployeeController {

    @Autowired
    private EmployeeService employeeService;

    @RequestMapping(
        value = "/add/employee",
        method = RequestMethod.GET
    )
    public Employee addEmployee(
            @RequestParam String empId,
            @RequestParam String name) {

        return employeeService.createEmployee(name, empId);
    }

    @RequestMapping(
        value = "/delete/employee",
        method = RequestMethod.GET
    )
    public void deleteEmployee(
            @RequestParam String empId) {

        employeeService.deleteEmployee(empId);
    }
}
```

---

# 15. Employee Service

Create the Employee Service class.

```java
package com.javainuse.service;

import org.springframework.stereotype.Service;

import com.javainuse.model.Employee;

@Service
public class EmployeeService {

    public Employee createEmployee(
            String name,
            String empId) {

        Employee emp = new Employee();

        emp.setName(name);
        emp.setEmpId(empId);

        return emp;
    }

    public void deleteEmployee(String empId) {

        System.out.println(
            "Deleting employee with id: " + empId
        );
    }
}
```

---

# 16. Problem Without AOP

Suppose we want to print a message before every method in `EmployeeService`.

One approach would be to write the logging code directly inside the service:

```java
public Employee createEmployee(
        String name,
        String empId) {

    System.out.println(
        "Executing createEmployee method"
    );

    Employee emp = new Employee();

    emp.setName(name);
    emp.setEmpId(empId);

    return emp;
}
```

But if we have many methods, we would need to repeat the same logging code.

This creates:

* Code duplication
* Tight coupling
* Difficult maintenance

AOP solves this problem by moving the cross-cutting functionality outside the main business logic.

---

# 17. Employee Aspect

Create an Aspect class.

```java
package com.javainuse.aspect;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class EmployeeAspect {

    @Before(
        value = "execution(* com.javainuse.service.EmployeeService.*(..))"
    )
    public void beforeAdvice(JoinPoint joinPoint) {

        System.out.println(
            "Before method: "
            + joinPoint.getSignature()
        );
    }

    @After(
        value = "execution(* com.javainuse.service.EmployeeService.*(..))"
    )
    public void afterAdvice(JoinPoint joinPoint) {

        System.out.println(
            "After method: "
            + joinPoint.getSignature()
        );
    }

    @AfterReturning(
        value = "execution(* com.javainuse.service.EmployeeService.*(..))"
    )
    public void afterReturningAdvice(
            JoinPoint joinPoint) {

        System.out.println(
            "Successfully completed: "
            + joinPoint.getSignature()
        );
    }

    @AfterThrowing(
        value = "execution(* com.javainuse.service.EmployeeService.*(..))"
    )
    public void afterThrowingAdvice(
            JoinPoint joinPoint) {

        System.out.println(
            "Method failed: "
            + joinPoint.getSignature()
        );
    }

    @Around(
        value = "execution(* com.javainuse.service.EmployeeService.*(..))"
    )
    public Object aroundAdvice(
            org.aspectj.lang.ProceedingJoinPoint joinPoint)
            throws Throwable {

        System.out.println(
            "Before method execution: "
            + joinPoint.getSignature()
        );

        Object result = joinPoint.proceed();

        System.out.println(
            "After method execution: "
            + joinPoint.getSignature()
        );

        return result;
    }
}
```

---

# 18. Understanding the Aspect Class

## `@Aspect`

```java
@Aspect
```

This tells Spring that the class contains Aspect-related functionality.

---

## `@Component`

```java
@Component
```

This registers the Aspect as a Spring-managed bean.

---

## `@Before`

```java
@Before(
    "execution(* com.javainuse.service.EmployeeService.*(..))"
)
```

This executes before matching methods in `EmployeeService`.

---

## `@After`

```java
@After(
    "execution(* com.javainuse.service.EmployeeService.*(..))"
)
```

This executes after the matching method completes.

---

## `@AfterReturning`

```java
@AfterReturning(
    "execution(* com.javainuse.service.EmployeeService.*(..))"
)
```

This executes when the matching method completes successfully.

---

## `@AfterThrowing`

```java
@AfterThrowing(
    "execution(* com.javainuse.service.EmployeeService.*(..))"
)
```

This executes when the matching method throws an exception.

---

## `@Around`

```java
@Around(
    "execution(* com.javainuse.service.EmployeeService.*(..))"
)
```

This surrounds the execution of the matching method.

A proper Around Advice uses:

```java
joinPoint.proceed();
```

to continue execution of the target method.

---

# 19. JoinPoint

The `JoinPoint` object provides information about the method being intercepted.

Example:

```java
public void beforeAdvice(JoinPoint joinPoint) {

    System.out.println(
        joinPoint.getSignature()
    );
}
```

The signature can provide information about the intercepted method.

---

# 20. Enable Spring AOP

Create the main Spring Boot application class.

```java
package com.javainuse;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@SpringBootApplication
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class SpringAOPApplication {

    public static void main(String[] args) {

        SpringApplication.run(
            SpringAOPApplication.class,
            args
        );
    }
}
```

The important annotation is:

```java
@EnableAspectJAutoProxy(proxyTargetClass = true)
```

It enables AspectJ-based proxy support for Spring AOP.

---

# 21. Project Structure

The project can be organized like this:

```text
src
└── main
    └── java
        └── com.javainuse
            ├── SpringAOPApplication.java
            │
            ├── aspect
            │   └── EmployeeAspect.java
            │
            ├── controller
            │   └── EmployeeController.java
            │
            ├── model
            │   └── Employee.java
            │
            └── service
                └── EmployeeService.java
```

---

# 22. Run the Application

Compile and run:

```text
SpringAOPApplication.java
```

Once the Spring Boot application starts, you can call the Employee API.

---

# 23. Test the API

Open the following URL in your browser:

```text
http://localhost:8080/add/employee?empId=1&name=test
```

This calls the Employee creation endpoint.

The request flow is:

```text
Browser
   |
   ↓
EmployeeController
   |
   ↓
EmployeeService
   |
   ↓
AOP Aspect
   |
   ├── Before
   ├── Around
   ├── Method Execution
   ├── After Returning
   └── After
```

The console will show the corresponding Aspect logs.

---

# 24. AOP Execution Flow

The overall flow can be represented as:

```text
                  HTTP Request
                       |
                       ↓
              EmployeeController
                       |
                       ↓
               EmployeeService
                       |
                       ↓
                  Pointcut
                       |
                       ↓
                 Join Point
                       |
          ┌────────────┼────────────┐
          ↓            ↓            ↓
       @Before      @Around       Method
          |            |            |
          |            ↓            |
          |       proceed()         |
          |            |            |
          └────────────┼────────────┘
                       |
                       ↓
                  @After
                       |
                       ↓
              @AfterReturning
                       |
                       ↓
                  Response
```

---

# 25. AOP Advice Types Summary

| Advice            | When It Executes                    |
| ----------------- | ----------------------------------- |
| `@Before`         | Before the target method            |
| `@After`          | After the target method             |
| `@AfterReturning` | After successful method execution   |
| `@AfterThrowing`  | When the method throws an exception |
| `@Around`         | Around the target method execution  |

---

# 26. AOP vs Normal Code

### Without AOP

```text
EmployeeService
      |
      ├── Business Logic
      ├── Logging
      ├── Security
      ├── Performance
      └── Error Handling
```

This can make the service tightly coupled with cross-cutting concerns.

### With AOP

```text
EmployeeService
      |
      └── Business Logic


EmployeeAspect
      |
      ├── Logging
      ├── Security
      ├── Performance
      └── Error Handling
```

This keeps cross-cutting concerns separate from the business logic.

---

# 27. Key Takeaways

### Aspect

> A module/class that contains cross-cutting functionality.

### Join Point

> A point in the application where an Aspect can be applied.

### Advice

> The action that an Aspect performs.

### Pointcut

> An expression that determines which Join Points should receive an Advice.

### Spring AOP

> A Spring-based implementation for applying cross-cutting concerns using aspects, pointcuts, and advice.

---

# 28. Simple Way to Remember AOP

```text
ASPECT
   ↓
Contains Advice
   ↓
Pointcut selects methods
   ↓
Join Point identifies execution point
   ↓
Advice executes
```

Or simply:

```text
Pointcut → WHERE?
Advice   → WHAT?
Aspect   → WHO?
Join Point → AT WHICH POINT?
```

---

# 29. Final Summary

**Aspect Oriented Programming (AOP)** helps separate cross-cutting concerns from the main application logic.

Instead of adding logging, security, performance monitoring, or other common functionality directly into every service method, we can define them in a separate Aspect.

The major concepts are:

```text
Aspect
   ↓
Pointcut
   ↓
Join Point
   ↓
Advice
```

Spring AOP makes this possible using annotations such as:

```java
@Aspect
@Before
@After
@AfterReturning
@AfterThrowing
@Around
@Component
```

This approach helps improve:

* Code modularity
* Maintainability
* Reusability
* Separation of concerns

> **In simple words: AOP lets you add common functionality to existing methods without repeatedly changing the main business logic.**

---
---
