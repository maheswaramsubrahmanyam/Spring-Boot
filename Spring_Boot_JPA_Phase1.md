# Goodreads API – Phase 1 (Spring Boot JPA)

## Overview

This project implements **CRUD operations for Books** using:

* Spring Boot
* Spring Web
* Spring Data JPA
* Hibernate
* H2 Database
* Maven

In **Phase 1**, the application manages only **Book** data.

> **Publisher functionality is not included in Phase 1.** Publisher functionality is added in Phase 2.

---

# Project Structure

```text
goodreads/
│
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── example/
│   │   │           └── goodreads/
│   │   │               │
│   │   │               ├── GoodreadsApplication.java
│   │   │               │
│   │   │               ├── controller/
│   │   │               │   └── BookController.java
│   │   │               │
│   │   │               ├── model/
│   │   │               │   └── Book.java
│   │   │               │
│   │   │               ├── repository/
│   │   │               │   ├── BookJpaRepository.java
│   │   │               │   └── BookRepository.java
│   │   │               │
│   │   │               └── service/
│   │   │                   └── BookJpaService.java
│   │   │
│   │   └── resources/
│   │       ├── application.properties
│   │       ├── schema.sql
│   │       └── data.sql
│   │
│   └── test/
│
├── pom.xml
└── README.md
```

---

# Technologies Used

| Technology        | Purpose                       |
| ----------------- | ----------------------------- |
| Java 8            | Programming language          |
| Spring Boot 2.7.8 | Application framework         |
| Spring Web        | REST APIs                     |
| Spring Data JPA   | Database operations           |
| Hibernate         | ORM                           |
| H2 Database       | In-memory/file-based database |
| Maven             | Dependency management         |

---

# 1. GoodreadsApplication.java

Location:

```text
src/main/java/com/example/goodreads/GoodreadsApplication.java
```

```java
package com.example.goodreads;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class GoodreadsApplication {

    public static void main(String[] args) {
        SpringApplication.run(GoodreadsApplication.class, args);
    }

}
```

---

# 2. BookController.java

Location:

```text
src/main/java/com/example/goodreads/controller/BookController.java
```

```java
package com.example.goodreads.controller;

import com.example.goodreads.model.Book;
import com.example.goodreads.service.BookJpaService;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;

@RestController
public class BookController {

    @Autowired
    private BookJpaService bookService;

    @GetMapping("/books")
    public ArrayList<Book> getBooks() {
        return bookService.getBooks();
    }

    @GetMapping("/books/{bookId}")
    public Book getBookById(@PathVariable("bookId") int bookId) {
        return bookService.getBookById(bookId);
    }

    @PostMapping("/books")
    public Book addBook(@RequestBody Book book) {
        return bookService.addBook(book);
    }

    @PutMapping("/books/{bookId}")
    public Book updateBook(
            @PathVariable("bookId") int bookId,
            @RequestBody Book book) {

        return bookService.updateBook(bookId, book);
    }

    @DeleteMapping("/books/{bookId}")
    public void deleteBook(@PathVariable("bookId") int bookId) {
        bookService.deleteBook(bookId);
    }
}
```

---

# 3. Book.java

Location:

```text
src/main/java/com/example/goodreads/model/Book.java
```

```java
package com.example.goodreads.model;

import javax.persistence.*;

@Entity
@Table(name = "book")
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private int id;

    @Column(name = "name")
    private String name;

    @Column(name = "imageurl")
    private String imageUrl;

    public Book() {
    }

    public Book(int id, String name, String imageUrl) {
        this.id = id;
        this.name = name;
        this.imageUrl = imageUrl;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getImageUrl() {
        return imageUrl;
    }

    public void setImageUrl(String imageUrl) {
        this.imageUrl = imageUrl;
    }
}
```

---

# 4. BookJpaRepository.java

Location:

```text
src/main/java/com/example/goodreads/repository/BookJpaRepository.java
```

```java
package com.example.goodreads.repository;

import com.example.goodreads.model.Book;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface BookJpaRepository extends JpaRepository<Book, Integer> {

}
```

---

# 5. BookRepository.java

Location:

```text
src/main/java/com/example/goodreads/repository/BookRepository.java
```

```java
package com.example.goodreads.repository;

import com.example.goodreads.model.Book;

import java.util.ArrayList;

public interface BookRepository {

    ArrayList<Book> getBooks();

    Book getBookById(int bookId);

    Book addBook(Book book);

    Book updateBook(int bookId, Book book);

    void deleteBook(int bookId);
}
```

---

# 6. BookJpaService.java

Location:

```text
src/main/java/com/example/goodreads/service/BookJpaService.java
```

```java
package com.example.goodreads.service;

import com.example.goodreads.model.Book;
import com.example.goodreads.repository.BookJpaRepository;
import com.example.goodreads.repository.BookRepository;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Service;
import org.springframework.web.server.ResponseStatusException;

import java.util.ArrayList;
import java.util.List;

@Service
public class BookJpaService implements BookRepository {

    @Autowired
    private BookJpaRepository bookJpaRepository;

    @Override
    public ArrayList<Book> getBooks() {

        List<Book> bookList = bookJpaRepository.findAll();

        ArrayList<Book> books = new ArrayList<>(bookList);

        return books;
    }

    @Override
    public Book getBookById(int bookId) {

        try {

            Book book = bookJpaRepository.findById(bookId).get();

            return book;

        } catch (Exception e) {

            throw new ResponseStatusException(
                    HttpStatus.NOT_FOUND
            );
        }
    }

    @Override
    public Book addBook(Book book) {

        bookJpaRepository.save(book);

        return book;
    }

    @Override
    public Book updateBook(int bookId, Book book) {

        try {

            Book newBook = bookJpaRepository.findById(bookId).get();

            if (book.getName() != null) {
                newBook.setName(book.getName());
            }

            if (book.getImageUrl() != null) {
                newBook.setImageUrl(book.getImageUrl());
            }

            bookJpaRepository.save(newBook);

            return newBook;

        } catch (Exception e) {

            throw new ResponseStatusException(
                    HttpStatus.NOT_FOUND
            );
        }
    }

    @Override
    public void deleteBook(int bookId) {

        try {

            bookJpaRepository.deleteById(bookId);

        } catch (Exception e) {

            throw new ResponseStatusException(
                    HttpStatus.NOT_FOUND
            );
        }

        throw new ResponseStatusException(
                HttpStatus.NO_CONTENT
        );
    }
}
```

---

# 7. application.properties

Location:

```text
src/main/resources/application.properties
```

```properties
spring.datasource.url=jdbc:h2:file:~/goodreads/src/main/goodreads

spring.h2.console.enabled=true

spring.h2.console.settings.web-allow-others=true

spring.sql.init.mode=always

spring.jpa.hibernate.ddl-auto=update
```

---

# 8. schema.sql

Location:

```text
src/main/resources/schema.sql
```

```sql
create table if not exists book (

    id INT PRIMARY KEY AUTO_INCREMENT,

    name varchar(255),

    imageUrl varchar(255)

);
```

### Important

Phase 1 contains **only the book table**.

There is no publisher table in Phase 1.

---

# 9. data.sql

Location:

```text
src/main/resources/data.sql
```

```sql
insert into book(name, imageUrl)
values(
    'Harry Potter and the Philosopher''s Stone',
    'harry_potter_1.jpg'
);

insert into book(name, imageUrl)
values(
    'Harry Potter and the Chamber of Secrets',
    'harry_potter_2.jpg'
);

insert into book(name, imageUrl)
values(
    'Harry Potter and the Goblet of Fire',
    'harry_potter_3.jpg'
);

insert into book(name, imageUrl)
values(
    'Harry Potter and the Cursed Child',
    'harry_potter_4.jpg'
);

insert into book(name, imageUrl)
values(
    'The 3 Mistakes of My Life',
    'mistakes_life.jpg'
);

insert into book(name, imageUrl)
values(
    'Life of Pi',
    'life_of_pi.jpg'
);

insert into book(name, imageUrl)
values(
    'One Night at the Call Center',
    'one_night_acc.jpg'
);

insert into book(name, imageUrl)
values(
    'Half Girlfriend',
    'half_gf.jpg'
);

insert into book(name, imageUrl)
values(
    'The Secret',
    'secret.jpg'
);

insert into book(name, imageUrl)
values(
    'Rise',
    'rise.jpg'
);
```

---

# 10. pom.xml

Location:

```text
pom.xml
```

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <parent>

        <groupId>org.springframework.boot</groupId>

        <artifactId>spring-boot-starter-parent</artifactId>

        <version>2.7.8</version>

        <relativePath/>

    </parent>

    <groupId>com.example</groupId>

    <artifactId>goodreads</artifactId>

    <version>0.0.1-SNAPSHOT</version>

    <name>goodreads</name>

    <description>Demo project for Spring Boot</description>

    <properties>

        <java.version>1.8</java.version>

    </properties>

    <dependencies>

        <dependency>

            <groupId>org.springframework.boot</groupId>

            <artifactId>spring-boot-starter-web</artifactId>

        </dependency>

        <dependency>

            <groupId>org.springframework.boot</groupId>

            <artifactId>spring-boot-starter-test</artifactId>

            <scope>test</scope>

        </dependency>

        <dependency>

            <groupId>com.h2database</groupId>

            <artifactId>h2</artifactId>

            <scope>runtime</scope>

        </dependency>

        <dependency>

            <groupId>org.springframework.boot</groupId>

            <artifactId>spring-boot-starter-data-jdbc</artifactId>

        </dependency>

        <dependency>

            <groupId>org.springframework.boot</groupId>

            <artifactId>spring-boot-starter-data-jpa</artifactId>

        </dependency>

    </dependencies>

    <build>

        <plugins>

            <plugin>

                <groupId>org.springframework.boot</groupId>

                <artifactId>spring-boot-maven-plugin</artifactId>

            </plugin>

        </plugins>

    </build>

</project>
```

---

# API Endpoints

Phase 1 provides the following REST APIs:

| HTTP Method | Endpoint          | Purpose          |
| ----------- | ----------------- | ---------------- |
| GET         | `/books`          | Get all books    |
| GET         | `/books/{bookId}` | Get a book by ID |
| POST        | `/books`          | Add a new book   |
| PUT         | `/books/{bookId}` | Update a book    |
| DELETE      | `/books/{bookId}` | Delete a book    |

---

# GET All Books

### Request

```http
GET http://localhost:8080/books
```

### Example Response

```json
[
    {
        "id": 1,
        "name": "Harry Potter and the Philosopher's Stone",
        "imageUrl": "harry_potter_1.jpg"
    },
    {
        "id": 2,
        "name": "Harry Potter and the Chamber of Secrets",
        "imageUrl": "harry_potter_2.jpg"
    }
]
```

---

# GET Book by ID

### Request

```http
GET http://localhost:8080/books/1
```

### Example Response

```json
{
    "id": 1,
    "name": "Harry Potter and the Philosopher's Stone",
    "imageUrl": "harry_potter_1.jpg"
}
```

If the book does not exist, the API returns:

```http
404 NOT_FOUND
```

---

# POST Add Book

### Request

```http
POST http://localhost:8080/books
```

### JSON Body

```json
{
    "name": "Wings of Fire",
    "imageUrl": "wings_of_fire.jpg"
}
```

### Example Response

```json
{
    "id": 11,
    "name": "Wings of Fire",
    "imageUrl": "wings_of_fire.jpg"
}
```

The ID is automatically generated by the database.

---

# PUT Update Book

### Request

```http
PUT http://localhost:8080/books/1
```

### JSON Body

```json
{
    "name": "Harry Potter Updated",
    "imageUrl": "updated.jpg"
}
```

### Example Response

```json
{
    "id": 1,
    "name": "Harry Potter Updated",
    "imageUrl": "updated.jpg"
}
```

The update operation also supports updating individual fields.

For example:

```json
{
    "name": "New Book Name"
}
```

Only the name will be changed.

---

# DELETE Book

### Request

```http
DELETE http://localhost:8080/books/1
```

The book with ID `1` will be deleted.

If the book does not exist, the service returns:

```http
404 NOT_FOUND
```

---

# H2 Database Console

The project uses H2 as the database.

After starting the Spring Boot application, open:

```text
http://localhost:8080/h2-console
```

Use the following JDBC URL:

```text
jdbc:h2:file:~/goodreads/src/main/goodreads
```

The database contains the following table:

```text
BOOK
```

---

# Application Flow

The request follows this flow:

```text
Client
   |
   v
BookController
   |
   v
BookJpaService
   |
   v
BookRepository
   |
   v
BookJpaRepository
   |
   v
Hibernate / JPA
   |
   v
H2 Database
```

### Example

When the client sends:

```http
GET /books
```

The request goes:

```text
BookController
       ↓
BookJpaService
       ↓
BookJpaRepository
       ↓
H2 Database
       ↓
List<Book>
       ↓
JSON Response
```

---

# Running the Project

## Step 1: Open the project

Open the `goodreads` project in Eclipse or IntelliJ IDEA.

## Step 2: Update Maven

In Eclipse:

```text
Right Click Project
        ↓
Maven
        ↓
Update Project
        ↓
Select the project
        ↓
Click OK
```

## Step 3: Run the application

Run:

```text
GoodreadsApplication.java
```

as:

```text
Run As → Java Application
```

or:

```text
Run As → Spring Boot App
```

## Step 4: Verify the server

The default server port is:

```text
8080
```

Therefore:

```text
http://localhost:8080/books
```

should return the books.

---

# Phase 1 Scope

Phase 1 contains:

* Book Entity
* Book Controller
* Book Service
* Book Repository
* JPA Repository
* H2 Database
* Book CRUD operations
* Initial book data

Phase 1 does **not** contain:

* Publisher Entity
* Publisher Controller
* Publisher Service
* Publisher Repository
* Publisher JPA Repository
* Publisher database table
* Book-Publisher relationship

---

# Phase 2

In **Phase 2**, Publisher functionality is added to the project.

Phase 2 will contain:

```text
Book
Publisher
```

along with their respective:

```text
Controller
Service
Repository
JPA Repository
```

and Publisher CRUD APIs.

---

# Conclusion

Goodreads API Phase 1 is a Spring Boot REST API application that demonstrates how to build **Book CRUD operations using Spring Data JPA and H2 Database**.

The project provides a clean layered architecture:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
JPA Repository
    ↓
Database
```

