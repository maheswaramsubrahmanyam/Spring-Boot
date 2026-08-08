# Goodreads API – Phase 1 (Spring Boot REST API)

## Overview

This project is a basic implementation of REST APIs using Spring Boot. It demonstrates how to create simple endpoints to retrieve data. The application manages book information using an in-memory data structure.

This phase focuses on understanding:

* REST API fundamentals
* Spring Boot project structure
* Controller, Service, and Repository layers
* Handling HTTP GET requests
* Returning JSON responses

---

## Tech Stack

* Java
* Spring Boot
* Maven
* Eclipse IDE

---

## Project Structure

com.example.goodreads

* GoodreadsApplication.java
* BookController.java
* BookService.java
* BookRepository.java
* Books.java

---

## API Endpoints

### 1. Get All Books

Method: GET
URL: http://localhost:8080/books

Description: Returns a list of all books.

---

### 2. Get Book by ID

Method: GET
URL: http://localhost:8080/books/{bookId}

Example: http://localhost:8080/books/1

Description: Returns a single book based on the given ID.

---

## How to Run the Application

1. Open the project in Eclipse
2. Run the main class: GoodreadsApplication.java
3. Check console output for server start confirmation
4. Use browser or Postman to test endpoints

---

## Testing

You can test the APIs using:

* Browser (for GET requests)
* Postman (recommended for API testing)

---

## Concepts Covered

* REST Controller using @RestController
* Mapping requests using @GetMapping
* Path variables using @PathVariable
* Layered architecture (Controller → Service → Repository)
* JSON response generation

---

## Limitations

* Data is stored in memory using HashMap
* No database integration
* Only GET APIs are implemented

---

## Source Code

### 1. Books.java (Model Class)

```java
package com.example.goodreads;

public class Books {
    private int id;
    private String name;
    private String Imageurl;

    public Books(int id, String name, String Imageurl) {
        this.id = id;
        this.name = name;
        this.Imageurl = Imageurl;
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

    public String getImageurl() {
        return Imageurl;
    }

    public void setImageurl(String Imageurl) {
        this.Imageurl = Imageurl;
    }
}
```

---

### 2. BookRepository.java (Interface)

```java
package com.example.goodreads;

import java.util.ArrayList;

public interface BookRepository {
    ArrayList<Books> getBooks();
    Books getBookById(int bookId);
}
```

---

### 3. BookService.java (Service Layer)

```java
package com.example.goodreads;

import java.util.*;

public class BookService implements BookRepository {

    private HashMap<Integer, Books> hmap = new HashMap<>();

    public BookService() {
        Books b1 = new Books(1, "Harry poter", "harry_potter.jpeg");
        Books b2 = new Books(2, "Rise", "rise.jpeg");

        hmap.put(b1.getId(), b1);
        hmap.put(b2.getId(), b2);
    }

    @Override
    public ArrayList<Books> getBooks() {
        Collection<Books> bookCollection = hmap.values();
        return new ArrayList<>(bookCollection);
    }

    @Override
    public Books getBookById(int bookId) {
        return hmap.get(bookId);
    }
}
```

---

### 4. BookController.java (Controller Layer)

```java
package com.example.goodreads;

import org.springframework.web.bind.annotation.*;
import java.util.*;

@RestController
class BookController {

    BookService bookService = new BookService();

    @GetMapping("/books")
    public ArrayList<Books> getBooks() {
        return bookService.getBooks();
    }

    @GetMapping("/books/{bookId}")
    public Books getBookById(@PathVariable("bookId") int bookId) {
        return bookService.getBookById(bookId);
    }
}
```

---

### 5. GoodreadsApplication.java (Main Class)

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

## Conclusion

This project serves as a foundational step in learning Spring Boot REST APIs. It establishes a clear understanding of how backend services are structured and how data is exposed through HTTP endpoints. This forms the base for implementing full CRUD operations and integrating with databases in the next phase.
