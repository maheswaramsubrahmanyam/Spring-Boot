# Goodreads API – Phase 2 (Spring Boot JPA)

## Overview

This project is the second phase of the Goodreads API application.

In **Phase 1**, the application implemented CRUD operations for books.

In **Phase 2**, **Publisher functionality is added**.

The application now supports CRUD operations for:

* Books
* Publishers

The project uses:

* Java 8
* Spring Boot 2.7.8
* Spring Web
* Spring Data JPA
* Hibernate
* H2 Database
* Maven

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
│   │   │               │   ├── BookController.java
│   │   │               │   └── PublisherController.java
│   │   │               │
│   │   │               ├── model/
│   │   │               │   ├── Book.java
│   │   │               │   └── Publisher.java
│   │   │               │
│   │   │               ├── repository/
│   │   │               │   ├── BookJpaRepository.java
│   │   │               │   ├── BookRepository.java
│   │   │               │   ├── PublisherJpaRepository.java
│   │   │               │   └── PublisherRepository.java
│   │   │               │
│   │   │               └── service/
│   │   │                   ├── BookJpaService.java
│   │   │                   └── PublisherJpaService.java
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

# Architecture

The application follows a layered architecture:

```text
                    Client
                      |
                      v
               REST Controller
                 /          \
                /            \
               v              v
        BookController   PublisherController
               |              |
               v              v
        BookJpaService  PublisherJpaService
               |              |
               v              v
       BookRepository   PublisherRepository
               |              |
               v              v
      BookJpaRepository PublisherJpaRepository
               |              |
               +-------> H2 Database
```

---

# Technologies Used

| Technology        | Purpose               |
| ----------------- | --------------------- |
| Java 8            | Programming language  |
| Spring Boot 2.7.8 | Application framework |
| Spring Web        | REST APIs             |
| Spring Data JPA   | Database access       |
| Hibernate         | ORM                   |
| H2                | Database              |
| Maven             | Dependency management |

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
import org.springframework.http.HttpStatus;
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
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public void deleteBook(@PathVariable("bookId") int bookId) {
        bookService.deleteBook(bookId);
    }
}
```

---

# 3. PublisherController.java

Location:

```text
src/main/java/com/example/goodreads/controller/PublisherController.java
```

```java
package com.example.goodreads.controller;

import com.example.goodreads.model.Publisher;
import com.example.goodreads.service.PublisherJpaService;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;

@RestController
public class PublisherController {

    @Autowired
    private PublisherJpaService publisherJpaService;

    @GetMapping("/publishers")
    public ArrayList<Publisher> getPublishers() {
        return publisherJpaService.getPublishers();
    }

    @GetMapping("/publishers/{publisherId}")
    public Publisher getPublisherById(
            @PathVariable("publisherId") Integer publisherId) {

        return publisherJpaService.getPublisherById(publisherId);
    }

    @PostMapping("/publishers")
    public Publisher addPublisher(
            @RequestBody Publisher publisher) {

        return publisherJpaService.addPublisher(publisher);
    }

    @PutMapping("/publishers/{publisherId}")
    public Publisher updatePublisher(
            @PathVariable("publisherId") int publisherId,
            @RequestBody Publisher publisher) {

        return publisherJpaService.updatePublisher(
                publisherId,
                publisher
        );
    }

    @DeleteMapping("/publishers/{publisherId}")
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public void deletePublisher(
            @PathVariable("publisherId") int publisherId) {

        publisherJpaService.deletePublisher(publisherId);
    }
}
```

---

# 4. Book.java

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

# 5. Publisher.java

Location:

```text
src/main/java/com/example/goodreads/model/Publisher.java
```

```java
package com.example.goodreads.model;

import javax.persistence.*;

@Entity
@Table(name = "publisher")
public class Publisher {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "publisherid")
    private int publisherId;

    @Column(name = "publishername")
    private String publisherName;

    public Publisher() {
    }

    public int getPublisherId() {
        return publisherId;
    }

    public void setPublisherId(int publisherId) {
        this.publisherId = publisherId;
    }

    public String getPublisherName() {
        return publisherName;
    }

    public void setPublisherName(String publisherName) {
        this.publisherName = publisherName;
    }
}
```

---

# 6. BookJpaRepository.java

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

# 7. BookRepository.java

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

# 8. PublisherJpaRepository.java

Location:

```text
src/main/java/com/example/goodreads/repository/PublisherJpaRepository.java
```

```java
package com.example.goodreads.repository;

import com.example.goodreads.model.Publisher;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface PublisherJpaRepository
        extends JpaRepository<Publisher, Integer> {

}
```

---

# 9. PublisherRepository.java

Location:

```text
src/main/java/com/example/goodreads/repository/PublisherRepository.java
```

```java
package com.example.goodreads.repository;

import com.example.goodreads.model.Publisher;

import java.util.ArrayList;

public interface PublisherRepository {

    ArrayList<Publisher> getPublishers();

    Publisher getPublisherById(Integer publisherId);

    Publisher addPublisher(Publisher publisher);

    Publisher updatePublisher(
            int publisherId,
            Publisher publisher
    );

    void deletePublisher(int publisherId);
}
```

---

# 10. BookJpaService.java

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

        return new ArrayList<>(bookList);
    }

    @Override
    public Book getBookById(int bookId) {

        try {

            return bookJpaRepository.findById(bookId).get();

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

            Book newBook =
                    bookJpaRepository.findById(bookId).get();

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

        if (!bookJpaRepository.existsById(bookId)) {

            throw new ResponseStatusException(
                    HttpStatus.NOT_FOUND
            );
        }

        bookJpaRepository.deleteById(bookId);
    }
}
```

---

# 11. PublisherJpaService.java

Location:

```text
src/main/java/com/example/goodreads/service/PublisherJpaService.java
```

```java
package com.example.goodreads.service;

import com.example.goodreads.model.Publisher;
import com.example.goodreads.repository.PublisherJpaRepository;
import com.example.goodreads.repository.PublisherRepository;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Service;
import org.springframework.web.server.ResponseStatusException;

import java.util.ArrayList;
import java.util.List;

@Service
public class PublisherJpaService
        implements PublisherRepository {

    @Autowired
    private PublisherJpaRepository publisherJpaRepository;

    @Override
    public ArrayList<Publisher> getPublishers() {

        List<Publisher> publisherList =
                publisherJpaRepository.findAll();

        return new ArrayList<>(publisherList);
    }

    @Override
    public Publisher getPublisherById(Integer publisherId) {

        try {

            return publisherJpaRepository
                    .findById(publisherId)
                    .get();

        } catch (Exception e) {

            throw new ResponseStatusException(
                    HttpStatus.NOT_FOUND
            );
        }
    }

    @Override
    public Publisher addPublisher(Publisher publisher) {

        publisherJpaRepository.save(publisher);

        return publisher;
    }

    @Override
    public Publisher updatePublisher(
            int publisherId,
            Publisher publisher) {

        try {

            Publisher newPublisher =
                    publisherJpaRepository
                            .findById(publisherId)
                            .get();

            if (publisher.getPublisherName() != null) {

                newPublisher.setPublisherName(
                        publisher.getPublisherName()
                );
            }

            publisherJpaRepository.save(newPublisher);

            return newPublisher;

        } catch (Exception e) {

            throw new ResponseStatusException(
                    HttpStatus.NOT_FOUND
            );
        }
    }

    @Override
    public void deletePublisher(int publisherId) {

        if (!publisherJpaRepository.existsById(publisherId)) {

            throw new ResponseStatusException(
                    HttpStatus.NOT_FOUND
            );
        }

        publisherJpaRepository.deleteById(publisherId);
    }
}
```

---

# 12. application.properties

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

# 13. schema.sql

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

create table if not exists publisher (

    publisherId INT PRIMARY KEY AUTO_INCREMENT,

    publisherName varchar(255)

);
```

---

# 14. data.sql

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


insert into publisher(publisherName)
values('Bloomsbury Publishing');

insert into publisher(publisherName)
values('Rupa Publications');

insert into publisher(publisherName)
values('Knopf Canada');

insert into publisher(publisherName)
values('Simon & Schuster');

insert into publisher(publisherName)
values('Penguin Random House');

insert into publisher(publisherName)
values('Viking Press');
```

---

# 15. pom.xml

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

    <description>Goodreads Spring Boot JPA Project</description>

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

            <artifactId>spring-boot-starter-data-jpa</artifactId>

        </dependency>

        <dependency>

            <groupId>com.h2database</groupId>

            <artifactId>h2</artifactId>

            <scope>runtime</scope>

        </dependency>

        <dependency>

            <groupId>org.springframework.boot</groupId>

            <artifactId>spring-boot-starter-test</artifactId>

            <scope>test</scope>

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

# Book APIs

## 1. Get All Books

```http
GET http://localhost:8080/books
```

Example response:

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

# 2. Get Book by ID

```http
GET http://localhost:8080/books/1
```

Example:

```json
{
    "id": 1,
    "name": "Harry Potter and the Philosopher's Stone",
    "imageUrl": "harry_potter_1.jpg"
}
```

If the ID does not exist:

```text
404 NOT_FOUND
```

---

# 3. Add Book

```http
POST http://localhost:8080/books
```

Request body:

```json
{
    "name": "Wings of Fire",
    "imageUrl": "wings_of_fire.jpg"
}
```

Example response:

```json
{
    "id": 11,
    "name": "Wings of Fire",
    "imageUrl": "wings_of_fire.jpg"
}
```

---

# 4. Update Book

```http
PUT http://localhost:8080/books/1
```

Request body:

```json
{
    "name": "Harry Potter Updated",
    "imageUrl": "updated.jpg"
}
```

Example response:

```json
{
    "id": 1,
    "name": "Harry Potter Updated",
    "imageUrl": "updated.jpg"
}
```

---

# 5. Delete Book

```http
DELETE http://localhost:8080/books/1
```

Successful response:

```text
204 NO_CONTENT
```

If the book does not exist:

```text
404 NOT_FOUND
```

---

# Publisher APIs

## 1. Get All Publishers

```http
GET http://localhost:8080/publishers
```

Example response:

```json
[
    {
        "publisherId": 1,
        "publisherName": "Bloomsbury Publishing"
    },
    {
        "publisherId": 2,
        "publisherName": "Rupa Publications"
    },
    {
        "publisherId": 3,
        "publisherName": "Knopf Canada"
    }
]
```

---

# 2. Get Publisher by ID

```http
GET http://localhost:8080/publishers/1
```

Example response:

```json
{
    "publisherId": 1,
    "publisherName": "Bloomsbury Publishing"
}
```

If the publisher does not exist:

```text
404 NOT_FOUND
```

---

# 3. Add Publisher

```http
POST http://localhost:8080/publishers
```

Request body:

```json
{
    "publisherName": "Oxford University Press"
}
```

Example response:

```json
{
    "publisherId": 7,
    "publisherName": "Oxford University Press"
}
```

---

# 4. Update Publisher

```http
PUT http://localhost:8080/publishers/1
```

Request body:

```json
{
    "publisherName": "Bloomsbury Updated"
}
```

Example response:

```json
{
    "publisherId": 1,
    "publisherName": "Bloomsbury Updated"
}
```

---

# 5. Delete Publisher

```http
DELETE http://localhost:8080/publishers/1
```

Successful response:

```text
204 NO_CONTENT
```

If the publisher does not exist:

```text
404 NOT_FOUND
```

---

# Complete API Table

| Method | Endpoint                    | Description         |
| ------ | --------------------------- | ------------------- |
| GET    | `/books`                    | Get all books       |
| GET    | `/books/{bookId}`           | Get book by ID      |
| POST   | `/books`                    | Add book            |
| PUT    | `/books/{bookId}`           | Update book         |
| DELETE | `/books/{bookId}`           | Delete book         |
| GET    | `/publishers`               | Get all publishers  |
| GET    | `/publishers/{publisherId}` | Get publisher by ID |
| POST   | `/publishers`               | Add publisher       |
| PUT    | `/publishers/{publisherId}` | Update publisher    |
| DELETE | `/publishers/{publisherId}` | Delete publisher    |

---

# H2 Database

The project uses H2 Database.

Start the application and open:

```text
http://localhost:8080/h2-console
```

Use this JDBC URL:

```text
jdbc:h2:file:~/goodreads/src/main/goodreads
```

The database contains:

```text
BOOK
PUBLISHER
```

---

# Database Tables

## BOOK

```text
BOOK
--------------------------------
ID
NAME
IMAGEURL
```

## PUBLISHER

```text
PUBLISHER
--------------------------------
PUBLISHERID
PUBLISHERNAME
```

---

# Running the Project

## Step 1 – Open Project

Open the `goodreads` project in Eclipse.

## Step 2 – Update Maven

Right-click the project:

```text
Maven
   ↓
Update Project
   ↓
Select goodreads
   ↓
OK
```

## Step 3 – Run Application

Open:

```text
GoodreadsApplication.java
```

Right-click:

```text
Run As
   ↓
Spring Boot App
```

## Step 4 – Test API

Open:

```text
http://localhost:8080/books
```

You can also test:

```text
http://localhost:8080/publishers
```

---

# Phase 1 vs Phase 2

## Phase 1

```text
Book
 |
 +-- Controller
 +-- Service
 +-- Repository
 +-- JPA Repository
```

Database:

```text
BOOK
```

APIs:

```text
/books
```

---

## Phase 2

```text
Book
 |
 +-- Controller
 +-- Service
 +-- Repository
 +-- JPA Repository

Publisher
 |
 +-- Controller
 +-- Service
 +-- Repository
 +-- JPA Repository
```

Database:

```text
BOOK
PUBLISHER
```

APIs:

```text
/books
/publishers
```

---

# Phase 2 Scope

Phase 2 adds:

* Publisher Entity
* Publisher Controller
* Publisher Service
* Publisher Repository
* Publisher JPA Repository
* Publisher database table
* Publisher initial data
* Publisher CRUD APIs

The existing Book CRUD functionality from Phase 1 remains available.

---

# Important Note

At this stage, **Book and Publisher are separate entities**.

There is currently **no `@OneToMany`, `@ManyToOne`, or `@ManyToMany` relationship** between Book and Publisher.

Therefore:

```text
Book
```

and

```text
Publisher
```

are independent tables and independent REST resources.

---

# Conclusion

Goodreads API Phase 2 extends the Phase 1 Spring Boot JPA application by introducing Publisher management.

The application now provides complete CRUD functionality for both:

```text
Books
Publishers
```

using the following architecture:

```text
REST Controller
       ↓
Service Layer
       ↓
Repository Interface
       ↓
Spring Data JPA Repository
       ↓
Hibernate
       ↓
H2 Database
```

This completes the **Goodreads API – Phase 2** implementation.
