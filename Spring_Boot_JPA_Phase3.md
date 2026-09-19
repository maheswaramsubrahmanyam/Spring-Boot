# Goodreads API – Phase 3 (Spring Boot JPA)

## Overview

This project extends the previous Goodreads API application by adding:

- Author functionality
- Publisher functionality
- Book functionality
- Book → Publisher relationship using `@ManyToOne`

The project uses:

- Java 8
- Spring Boot 2.7.8
- Spring Web
- Spring Data JPA
- Hibernate
- H2 Database
- Maven
- Spring Data JDBC

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
│   │   │               │   ├── AuthorController.java
│   │   │               │   ├── BookController.java
│   │   │               │   └── PublisherController.java
│   │   │               │
│   │   │               ├── model/
│   │   │               │   ├── Author.java
│   │   │               │   ├── Book.java
│   │   │               │   └── Publisher.java
│   │   │               │
│   │   │               ├── repository/
│   │   │               │   ├── AuthorJpaRepository.java
│   │   │               │   ├── AuthorRepository.java
│   │   │               │   ├── BookJpaRepository.java
│   │   │               │   ├── BookRepository.java
│   │   │               │   ├── PublisherJpaRepository.java
│   │   │               │   └── PublisherRepository.java
│   │   │               │
│   │   │               └── service/
│   │   │                   ├── AuthorJpaService.java
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

```text
                     Client
                        |
                        v
                 REST Controller
              /        |         \
             /         |          \
            v          v           v
     AuthorController BookController PublisherController
            |          |           |
            v          v           v
     AuthorJpaService BookJpaService PublisherJpaService
            |          |           |
            v          v           v
    AuthorRepository BookRepository PublisherRepository
            |          |           |
            v          v           v
    AuthorJpaRepository BookJpaRepository PublisherJpaRepository
                         |
                         v
                    H2 Database
```

---

# 1. AuthorController.java

Location:

```text
src/main/java/com/example/goodreads/controller/AuthorController.java
```

```java
package com.example.goodreads.controller;

import com.example.goodreads.model.Author;
import com.example.goodreads.service.AuthorJpaService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;

@RestController
public class AuthorController {

    @Autowired
    private AuthorJpaService authorJpaService;

    @GetMapping("/authors")
   public ArrayList<Author> getAuthors(){
       return authorJpaService.getAuthors();
   }

   @GetMapping("/authors/{id}")
   public Author getAuthorById(@PathVariable("id") int id){
           return authorJpaService.getAuthorById(id);
   }

   @PostMapping("/authors")
    public Author addAuthor(@RequestBody Author author){
        return authorJpaService.addAuthor(author);
    }

    @PutMapping("/authors/{id}")
    public Author updateAuthor(@RequestBody Author author, @PathVariable("id") int id) {
        return authorJpaService.updateAuthor(id, author);
    }

    @DeleteMapping("/authors/{id}")
    public void deleteAuthor(@PathVariable("id") int id) {
        authorJpaService.deleteAuthor(id);
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

import com.example.goodreads.model.Publisher;
import com.example.goodreads.service.BookJpaService;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.DeleteMapping;

import com.example.goodreads.model.Book;
import org.springframework.beans.factory.annotation.Autowired;

import java.util.*;

@RestController
class BookController {
    @Autowired
    public BookJpaService bookService;

    @GetMapping("/books")
    public ArrayList<Book> getBooks() {
        return bookService.getBooks();
    }

    @GetMapping("/books/{bookId}")
    public Book getBookById(@PathVariable("bookId") int bookId) {
        return bookService.getBookById(bookId);
    }

    @PostMapping("/publishers/books")
    public Book addBook(@RequestBody Book book) {

        return bookService.addBook(book);
    }

    @PutMapping("/publishers/books/{bookId}")
    public Book updateBook(@PathVariable("bookId") int bookId, @RequestBody Book book) {
        return bookService.updateBook(bookId, book);
    }

    @DeleteMapping("/books/{bookId}")
    public void deleteBook(@PathVariable("bookId") int bookId) {
        bookService.deleteBook(bookId);
    }

    @GetMapping("/books/{bookId}/publisher")
    public Publisher getBookPublisher(@PathVariable("bookId") int bookId) {
        return bookService.getBookPublisher(bookId);
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
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;

@RestController
public class PublisherController {
    @Autowired
    private PublisherJpaService publisherJpaService;

    @GetMapping("/publishers")
    public ArrayList<Publisher> getPublishers(){
        return publisherJpaService.getPublishers();
    }

    @GetMapping("/publishers/{publisherId}")
    public Publisher getPublisherById(@PathVariable("publisherId") int publisherId){
        return publisherJpaService.getPublisherById(publisherId);
    }

    @PostMapping("/publishers")
    public Publisher addPublisher(@RequestBody Publisher publisher){
        return publisherJpaService.addPublisher(publisher);
    }

    @PutMapping("/publishers/{publisherId}")
    public Publisher updatePublisher(@RequestBody Publisher publisher, @PathVariable("publisherId") int publisherId) {
        return publisherJpaService.updatePublisher(publisherId, publisher);
    }

    @DeleteMapping("/publishers/{publisherId}")
    public void deletePublisher(@PathVariable("publisherId") int publisherId) {
        publisherJpaService.deletePublisher(publisherId);
    }
}
```

---

# 4. Author.java

Location:

```text
src/main/java/com/example/goodreads/model/Author.java
```

```java
package com.example.goodreads.model;

import javax.persistence.*;

@Entity
@Table(name = "author")
public class Author {

    @Id
    @Column(name = "authorid")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int authorId;

    @Column(name = "authorname")
    private String authorName;

    public Author(){

    }
    public Author(int authorid, String authorName){
        this.authorId = authorid;
        this.authorName = authorName;
    }

    public int getAuthorId(){
        return authorId;
    }
    public void setAuthorIdId(int authorId){
        this.authorId = authorId;
    }

    public String getAuthorName(){
        return authorName;
    }
    public void setAuthorName(String authorName){
        this.authorName = authorName;
    }

}
```

---

# 5. Book.java

Location:

```text
src/main/java/com/example/goodreads/model/Book.java
```

```java
package com.example.goodreads.model;

import javax.persistence.*;

@Entity
@Table(name="book")
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private int id;

    @Column(name = "name")
    private String name;

    @Column(name = "imageurl")
    private String imageUrl;

    @ManyToOne
    @JoinColumn(name = "publisherid")
    private Publisher publisher;

    public Book() {
    }

    public Book(int id, String name, String imageUrl, Publisher publisher) {
        this.id = id;
        this.name = name;
        this.imageUrl = imageUrl;
        this.publisher = publisher;
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

    public Publisher getPublisher() {
        return publisher;
    }

    public void setPublisher(Publisher publisher) {
        this.publisher = publisher;
    }
}
```

---

# 6. Publisher.java

Location:

```text
src/main/java/com/example/goodreads/model/Publisher.java
```

```java
package com.example.goodreads.model;

import javax.persistence.*;

@Entity
public class Publisher {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "publisherid")
    private int publisherId;

    @Column(name = "publishername")
    private String publisherName;

    public Publisher(){}

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

# 7. AuthorJpaRepository.java

Location:

```text
src/main/java/com/example/goodreads/repository/AuthorJpaRepository.java
```

```java
package com.example.goodreads.repository;

import com.example.goodreads.model.Author;

import org.springframework.data.jpa.repository.JpaRepository;

import org.springframework.stereotype.Repository;


@Repository

public interface AuthorJpaRepository extends JpaRepository<Author,Integer> {

}
```

---

# 8. AuthorRepository.java

Location:

```text
src/main/java/com/example/goodreads/repository/AuthorRepository.java
```

```java
package com.example.goodreads.repository;

import com.example.goodreads.model.Author;

import java.util.ArrayList;

public interface AuthorRepository {
    ArrayList<Author> getAuthors();

    Author getAuthorById(int authorId);

    Author addAuthor(Author author);

    Author updateAuthor(int authorId, Author author);

    void deleteAuthor(int authorId);
}
```

---

# 9. BookJpaRepository.java

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

# 10. BookRepository.java

Location:

```text
src/main/java/com/example/goodreads/repository/BookRepository.java
```

```java
package com.example.goodreads.repository;

import java.util.ArrayList;

import com.example.goodreads.model.Book;
import com.example.goodreads.model.Publisher;

public interface BookRepository {
    ArrayList<Book> getBooks();

    Book getBookById(int bookId);

    Book addBook(Book book);

    Book updateBook(int bookId, Book book);

    void deleteBook(int bookId);

    Publisher getBookPublisher(int bookId);
}
```

---

# 11. PublisherJpaRepository.java

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

public interface PublisherJpaRepository extends JpaRepository<Publisher,Integer> {

}
```

---

# 12. PublisherRepository.java

Location:

```text
src/main/java/com/example/goodreads/repository/PublisherRepository.java
```

```java
package com.example.goodreads.repository;

import com.example.goodreads.model.Publisher;
import org.springframework.stereotype.Repository;

import java.util.ArrayList;

@Repository
public interface PublisherRepository {

    ArrayList<Publisher> getPublishers();

    Publisher getPublisherById(int publisherId);

    Publisher addPublisher(Publisher publisher);

    Publisher updatePublisher(int publisherId, Publisher publisher);

    void deletePublisher(int publisherId);
}
```

---

# 13. AuthorJpaService.java

Location:

```text
src/main/java/com/example/goodreads/service/AuthorJpaService.java
```

```java
package com.example.goodreads.service;

import com.example.goodreads.model.Author;
import com.example.goodreads.repository.AuthorJpaRepository;
import com.example.goodreads.repository.AuthorRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Service;
import org.springframework.web.server.ResponseStatusException;

import java.util.ArrayList;
import java.util.List;

@Service
public class AuthorJpaService implements AuthorRepository {

    @Autowired
    private AuthorJpaRepository authorJpaRepository;

    @Override
    public ArrayList<Author> getAuthors() {
        List<Author> authorList = authorJpaRepository.findAll();
        ArrayList<Author> authors = new ArrayList<>(authorList);
        return authors;
    }

    @Override
    public Author getAuthorById(int authorId) {
        try {
            Author author = authorJpaRepository.findById(authorId).get();
            return author;
        }catch (Exception e) {
            throw new ResponseStatusException(HttpStatus.NOT_FOUND);
        }
    }

    @Override
    public Author addAuthor(Author author) {
        authorJpaRepository.save(author);
        return author;
    }

    @Override
    public Author updateAuthor(int authorId, Author author) {
        try{
            Author new_author = authorJpaRepository.findById(authorId).get();
            if(author.getAuthorName()!=null)new_author.setAuthorName(author.getAuthorName());
            authorJpaRepository.save(new_author);
            return new_author;
        }catch(Exception e){
            throw new ResponseStatusException(HttpStatus.NOT_FOUND);
        }
    }

    @Override
    public void deleteAuthor(int authorId) {
        try {
            authorJpaRepository.deleteById(authorId);
        } catch (Exception e) {
            throw new ResponseStatusException(HttpStatus.NOT_FOUND);
        }
        throw new ResponseStatusException(HttpStatus.NO_CONTENT);
    }
}
```

---

# 14. BookJpaService.java

Location:

```text
src/main/java/com/example/goodreads/service/BookJpaService.java
```

```java
package com.example.goodreads.service;

import com.example.goodreads.model.Book;
import com.example.goodreads.model.Publisher;
import com.example.goodreads.repository.BookJpaRepository;
import com.example.goodreads.repository.PublisherJpaRepository;
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

    @Autowired
    private PublisherJpaRepository publisherJpaRepository;

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
            throw new ResponseStatusException(HttpStatus.NOT_FOUND);
        }
    }

    @Override
    public Book addBook(Book book) {
        Publisher publisher = book.getPublisher();
        int publisherId = publisher.getPublisherId();
        try{

            publisher = publisherJpaRepository.findById(publisherId).get();
            book.setPublisher(publisher);
            bookJpaRepository.save(book);
            return book;
        } catch (Exception e) {

            throw new ResponseStatusException(HttpStatus.NOT_FOUND, "Wrong publisherId");
        }
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
            if (book.getPublisher() != null) {
                Publisher publisher = book.getPublisher();
                int publisherId = publisher.getPublisherId();
                Publisher newPublisher = publisherJpaRepository.findById(publisherId).get();
                newBook.setPublisher(newPublisher);
            }
            bookJpaRepository.save(newBook);
            return newBook;
        } catch (Exception e) {
            throw new ResponseStatusException(HttpStatus.NOT_FOUND);
        }
    }

    @Override
    public void deleteBook(int bookId) {
        try {
            bookJpaRepository.deleteById(bookId);
        } catch (Exception e) {
            throw new ResponseStatusException(HttpStatus.NOT_FOUND);
        }
        throw new ResponseStatusException(HttpStatus.NO_CONTENT);
    }

    @Override
    public Publisher getBookPublisher(int bookId) {
        try {
            Book book = bookJpaRepository.findById(bookId).get();
            return book.getPublisher();
        } catch(Exception e){
            throw new ResponseStatusException(HttpStatus.NOT_FOUND);
        }
    }
}
```

---

# 15. PublisherJpaService.java

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
public class PublisherJpaService implements PublisherRepository {

    @Autowired
    private PublisherJpaRepository publisherJpaRepository;


    @Override
    public ArrayList<Publisher> getPublishers() {
        List<Publisher> publisherList = publisherJpaRepository.findAll();
        ArrayList<Publisher> publishers = new ArrayList<>(publisherList);
        return publishers;
    }

    @Override
    public Publisher getPublisherById(int publisherId) {
        try{
            Publisher publisher = publisherJpaRepository.findById(publisherId).get();
            return publisher;
        }catch (Exception e) {
            throw new ResponseStatusException(HttpStatus.NOT_FOUND);
        }
    }

    @Override
    public Publisher addPublisher(Publisher publisher) {
        publisherJpaRepository.save(publisher);
        return publisher;
    }

    @Override
    public Publisher updatePublisher(int publisherId, Publisher publisher) {
        try{
            Publisher new_publisher = publisherJpaRepository.findById(publisherId).get();
            if(publisher.getPublisherName()!=null)new_publisher.setPublisherName(publisher.getPublisherName());
            publisherJpaRepository.save(new_publisher);
            return new_publisher;
        }catch(Exception e){
            throw new ResponseStatusException(HttpStatus.NOT_FOUND);
        }
    }

    @Override
    public void deletePublisher(int publisherId) {
        try {
            publisherJpaRepository.deleteById(publisherId);
        } catch (Exception e) {
            throw new ResponseStatusException(HttpStatus.NOT_FOUND);
        }
        throw new ResponseStatusException(HttpStatus.NO_CONTENT);
    }
}
```

---

# 16. GoodreadsApplication.java

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

# 17. application.properties

Location:

```text
src/main/resources/application.properties
```

```properties
spring.datasource.url=jdbc:h2:file:~/goodreads/src/main/goodreads

spring.h2.console.enabled=true

spring.h2.console.settings.web-allow-others=true

spring.sql.init.mode=always

server.error.include-message = always
```

---

# 18. data.sql

Location:

```text
src/main/resources/data.sql
```

```sql
insert into publisher (publisherName) VALUES ('Bloomsbury Publishing');

insert into publisher (publisherName) VALUES ('Rupa and Co');

insert into publisher (publisherName) VALUES ('Knopf Canada');

insert into publisher (publisherName) VALUES ('Simon & Schuster');

insert into publisher (publisherName) VALUES ('Penguin Random House');

insert into publisher (publisherName) VALUES ('Viking Press');

insert into author(authorName) values('J.K.Rowling');

insert into author(authorName) values('Larry Niven');

insert into author(authorName) values('Jerry Pournelle');

insert into author(authorName) values('Stephen King');

insert into author(authorName) values('Peter Straub');

insert into author(authorName) values('Chetan Bhagat');

insert into author(authorName) values('Rhonda Byrne');

insert into book (name, imageUrl, publisherId) VALUES ('Harry Potter and the Sorcerer''s Stone', 'harry_potter_and_the_sorcerers_stone.jpg', 1);

insert into book (name, imageUrl, publisherId) VALUES ('The Mote in Gods Eye', 'mote_in_gods_eye.jpg', 3);

insert into book (name, imageUrl, publisherId) VALUES ('The Talisman', 'the_talisman.jpg', 1);

insert into book (name, imageUrl, publisherId) VALUES ('The Stand', 'the_stand.jpg', 6);

insert into book (name, imageUrl, publisherId) VALUES ('Black House', 'black_house.jpg', 2);

insert into book (name, imageUrl, publisherId) VALUES ('Half Girlfriend', 'half_girlfriend.jpg', 2);

insert into book (name, imageUrl, publisherId) VALUES ('The Secret', 'the_secret.jpg', 4);

insert into book (name, imageUrl, publisherId) VALUES ('The 3 Mistakes of My Life', 'the_3_mistakes_of_my_life.jpg', 2);
```

---

# 19. schema.sql

Location:

```text
src/main/resources/schema.sql
```

```sql
create table if not exists publisher (

  publisherId INT PRIMARY KEY AUTO_INCREMENT,

  publisherName varchar(255)

);

create table if not exists author (

  authorId INT PRIMARY KEY AUTO_INCREMENT,

  authorName varchar(255)

);

create table if not exists book (

    id INT PRIMARY KEY AUTO_INCREMENT,

    name varchar(255),

    imageUrl varchar(255),

    publisherId INT,

    FOREIGN KEY (publisherId) REFERENCES publisher(publisherId)

);
```

---

# 20. pom.xml

Location:

```text
pom.xml
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.7.8</version>
		<relativePath/> <!-- lookup parent from repository -->
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

## Author APIs

| Method | Endpoint | Description |
|---|---|---|
| GET | `/authors` | Get all authors |
| GET | `/authors/{id}` | Get author by ID |
| POST | `/authors` | Add author |
| PUT | `/authors/{id}` | Update author |
| DELETE | `/authors/{id}` | Delete author |

## Book APIs

| Method | Endpoint | Description |
|---|---|---|
| GET | `/books` | Get all books |
| GET | `/books/{bookId}` | Get book by ID |
| POST | `/publishers/books` | Add book |
| PUT | `/publishers/books/{bookId}` | Update book |
| DELETE | `/books/{bookId}` | Delete book |
| GET | `/books/{bookId}/publisher` | Get publisher of a book |

## Publisher APIs

| Method | Endpoint | Description |
|---|---|---|
| GET | `/publishers` | Get all publishers |
| GET | `/publishers/{publisherId}` | Get publisher by ID |
| POST | `/publishers` | Add publisher |
| PUT | `/publishers/{publisherId}` | Update publisher |
| DELETE | `/publishers/{publisherId}` | Delete publisher |

---

# Book → Publisher Relationship

The new `Book` model contains:

```java
@ManyToOne
@JoinColumn(name = "publisherid")
private Publisher publisher;
```

This means the `book` table contains the `publisherId` foreign-key column.

Database relationship:

```text
PUBLISHER
-------------------------
publisherId (PK)
publisherName
        |
        |
        | 1
        |
        | 
        | *
        v
BOOK
-------------------------
id (PK)
name
imageUrl
publisherId (FK)
```

A publisher can therefore be associated with multiple books, while each book has a publisher.

---

# Adding a Book

The `BookJpaService` receives the publisher information from the request:

```java
Publisher publisher = book.getPublisher();
int publisherId = publisher.getPublisherId();
```

It then finds the publisher from the database:

```java
publisher = publisherJpaRepository.findById(publisherId).get();
```

The managed publisher is assigned to the book:

```java
book.setPublisher(publisher);
```

Finally, the book is saved:

```java
bookJpaRepository.save(book);
```

If the publisher ID is invalid, the service returns:

```text
404 NOT_FOUND
Wrong publisherId
```

---

# Getting a Book's Publisher

Endpoint:

```text
GET /books/{bookId}/publisher
```

The controller calls:

```java
return bookService.getBookPublisher(bookId);
```

The service gets the book:

```java
Book book = bookJpaRepository.findById(bookId).get();
```

Then returns the publisher:

```java
return book.getPublisher();
```

---

# Updating a Book

The update operation can update:

- Book name
- Book image URL
- Book publisher

The publisher is changed by providing the publisher ID:

```java
if (book.getPublisher() != null) {
    Publisher publisher = book.getPublisher();
    int publisherId = publisher.getPublisherId();
    Publisher newPublisher = publisherJpaRepository.findById(publisherId).get();
    newBook.setPublisher(newPublisher);
}
```

---

# H2 Database

The application uses a file-based H2 database:

```text
jdbc:h2:file:~/goodreads/src/main/goodreads
```

H2 console:

```text
http://localhost:8080/h2-console
```

The database contains:

```text
PUBLISHER
AUTHOR
BOOK
```

---

# Database Tables

## PUBLISHER

```text
PUBLISHER
--------------------------------
publisherId
publisherName
```

## AUTHOR

```text
AUTHOR
--------------------------------
authorId
authorName
```

## BOOK

```text
BOOK
--------------------------------
id
name
imageUrl
publisherId
```

Relationship:

```text
BOOK.publisherId
        |
        v
PUBLISHER.publisherId
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

## Step 4 – Test APIs

Authors:

```text
http://localhost:8080/authors
```

Books:

```text
http://localhost:8080/books
```

Publishers:

```text
http://localhost:8080/publishers
```

H2 Console:

```text
http://localhost:8080/h2-console
```

---

# Example Request – Add Author

```http
POST http://localhost:8080/authors
```

```json
{
    "authorName": "A. P. J. Abdul Kalam"
}
```

---

# Example Request – Add Book

Because `Book` has a publisher relationship, the request contains publisher information:

```http
POST http://localhost:8080/publishers/books
```

```json
{
    "name": "Wings of Fire",
    "imageUrl": "wings_of_fire.jpg",
    "publisher": {
        "publisherId": 1
    }
}
```

---

# Example Request – Update Book Publisher

```http
PUT http://localhost:8080/publishers/books/1
```

```json
{
    "publisher": {
        "publisherId": 2
    }
}
```

---

# Example Request – Get Book Publisher

```http
GET http://localhost:8080/books/1/publisher
```

---

# Phase Progression

## Previous Phase

The previous project had:

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

Book and Publisher were independent entities.

## Current Class

The current implementation adds:

```text
Author
 |
 +-- Controller
 +-- Service
 +-- Repository
 +-- JPA Repository
```

And Book now has a relationship with Publisher:

```text
Publisher
    |
    | 1
    |
    | *
    v
  Book
```

The project now contains:

```text
Author
Publisher
Book
```

with:

```text
Book → Publisher
```

relationship support.

---

# Complete File List

```text
goodreads/
│
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/goodreads/
│   │   │       │
│   │   │       ├── GoodreadsApplication.java
│   │   │       │
│   │   │       ├── controller/
│   │   │       │   ├── AuthorController.java
│   │   │       │   ├── BookController.java
│   │   │       │   └── PublisherController.java
│   │   │       │
│   │   │       ├── model/
│   │   │       │   ├── Author.java
│   │   │       │   ├── Book.java
│   │   │       │   └── Publisher.java
│   │   │       │
│   │   │       ├── repository/
│   │   │       │   ├── AuthorJpaRepository.java
│   │   │       │   ├── AuthorRepository.java
│   │   │       │   ├── BookJpaRepository.java
│   │   │       │   ├── BookRepository.java
│   │   │       │   ├── PublisherJpaRepository.java
│   │   │       │   └── PublisherRepository.java
│   │   │       │
│   │   │       └── service/
│   │   │           ├── AuthorJpaService.java
│   │   │           ├── BookJpaService.java
│   │   │           └── PublisherJpaService.java
│   │   │
│   │   └── resources/
│   │       ├── application.properties
│   │       ├── data.sql
│   │       └── schema.sql
│   │
│   └── test/
│
├── pom.xml
└── README.md
```

---

# Summary

This Goodreads API implementation contains three main entities:

```text
Author
Publisher
Book
```

CRUD functionality is available for:

```text
Authors
Publishers
Books
```

The Book entity now contains a JPA relationship with Publisher:

```java
@ManyToOne
@JoinColumn(name = "publisherid")
private Publisher publisher;
```

The database relationship is:

```text
Publisher 1 ─────────── * Book
```

The complete request flow is:

```text
Client
   ↓
Controller
   ↓
Service
   ↓
Repository Interface
   ↓
Spring Data JPA Repository
   ↓
Hibernate
   ↓
H2 Database
```
