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
import com.example.goodreads.Books;
import com.example.goodreads.BookRepository;
import org.springframework.http.HttpStatus;
import org.springframework.web.server.ResponseStatusException;
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
        ArrayList<Books> books = new ArrayList<>(bookCollection);
        return books;

    }

    @Override
    public Books getBookById(int bookId) {
        Books book = hmap.get(bookId);
        if (book == null){
            throw new ResponseStatusException(HttpStatus.NOT_FOUND);

        }
        return book;

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

# Introduction

In the previous unit, we learned to build a simple Helloworld Spring Boot application. In large-scale applications, four types of operations are necessary to manage the application which are known as CRUD operations (i.e., Create, Read, Update, and Delete the data)

In this unit, we'll build a Goodreads application, and write REST APIs to read the data.

---

## 1. Goodreads Application

Goodreads is a popular website where we can find a lot of books to read. Let's build a similar application where the user can create, read, update and delete a book.

To have cleaner code and for ease of maintenance, programmers divide the code and create different classes to handle different types of responsibilities.

Basically they are,

1. Models: These are the classes that holds the actual data representations.
2. Services: These consist the business logic.
3. Controllers: These are the classes that handle all our HTTP requests.

Let's create a Spring Boot project/application and name it

goodreads

---

## 2. Creating a Model Class

Model class holds the actual data representations. In our case,

Book class is the model that is responsible to create book objects and consists of attributes and methods that are required for a book object.

We create a new file

Book.java

in the

goodreads

directory, and write

Book class in that file.

Every book consists of an

id, name, and an imageUrl. So, let's add these as non-static attributes.

```java
package com.example.goodreads;

public class Book {

int id;
String name;
String imageUrl;

}
```

To initialize these attributes, we have to add a constructor method in the Book class.

```java
package com.example.goodreads;

public class Book {

int id;
String name;
String imageUrl;

public Book(int id, String name, String imageUrl) {

this.id = id;
this.name = name;
this.imageUrl = imageUrl;

}
```

To restrict access to the non-static attributes, let's declare them as private and provide getters and setters to access these attributes.

```java
package com.example.goodreads;

public class Book {

private int id;
private String name;
private String imageUrl;

public Book(int id, String name, String imageUrl) {

this.id = id;
this.name = name;
this.imageUrl = imageUrl;

}
```

In the above code, we have defined methods to get and set each non-static attribute of the Book object. We have also defined a constructor in the Book class with three parameters id, name, and imageUrl.

Hence, our Book class is ready to create new book objects.

---

## 3. Creating Service and Controller Classes

We have to create a service class that holds the logic and a controller class to handle the HTTP requests.

### 3.1 Creating Service Class

In our case, the service class holds the logic of creating, reading, updating and deleting the books.

Let's create a service class

BookService

in a new file

BookService.java

```java
package com.example.goodreads;

public class BookService {

// business logic

}
```

In order to store the book objects, let's use HashMap with the book id as a key.

```java
package com.example.goodreads;

import java.util.*;

public class BookService {

private HashMap<Integer, Book> hmap = new HashMap<>();

}
```

In the above code, we have made the hmap as private to restrict the access of the hmap outside the class to avoid unnecessary modifications.

To start with, let's add a constructor and create some sample books,

```java
package com.example.goodreads;

import java.util.*;

public class BookService {

private HashMap<Integer, Book> hmap = new HashMap<>();

public BookService() {

Book b1 = new Book(1, "harry potter","harry_potter.jpg");
Book b2 = new Book(2, "Rise","rise.jpeg");

hmap.put(b1.getId(), b1);

}
```

In the above code, the constructor BookService creates two books whenever it is invoked.

---

### 3.2 Creating Controller Class

Let's create a controller class

BookController

in a new file

BookController.java

```java
package com.example.goodreads;

import org.springframework.web.bind.annotation.RestController;

@RestController
public class BookController {

// controller methods

}
```

Now, we are ready to write the APIs and the required logic to handle the HTTP requests.

---

## 4. Getting Books

To get the list of all books, we need to write the logic to get all the books from the hmap.

Let's create an interface for consistency and abstraction across the services. The interface will provide standardized methods for managing books in the service classes to implement.

BookRepository.java

```java
package com.example.goodreads;

import java.util.*;

public interface BookRepository {

ArrayList<Book> getBooks();

}
```

We have an interface with an abstract method getBooks(). So, the classes that implement the BookRepository should override and provide implementation to the getBooks() method.

In the BookService class we have to implement the BookRepository interface.

```java
package com.example.goodreads;

import java.util.*;
import com.example.goodreads.BookRepository;

public class BookService implements BookRepository {

private HashMap<Integer, Book> hmap = new HashMap<>();

public BookService() {

Book b1 = new Book(1, "harry potter","harry_potter.jpg");
Book b2 = new Book(2, "Rise","rise.jpeg");

}
```

In the above code, the HashMap method values() returns a collection of objects of Book class. We have converted it to an ArrayList and returned it.

We have also used a @Override annotation in our code.

Note:

In Java, the @Override annotation is used to indicate that a method is intended to override a method in a superclass.

It helps ensure the correct method is being overridden and improves code clarity.

Now, our logic for getting all the books in the hmap is ready. So, we have to write a method in our Restcontroller class to handle the HTTP request.

So, let's create a get() method that handles the HTTP GET request to the path as /books and returns the list of all books.

```java
@GetMapping("/books")
```

This method serves as a handler for the HTTP GET requests to the path /books. It returns a Collection of all the books present in the hmap attribute.

Whenever we make an HTTP request through the browser, by default it is considered as an HTTP GET request.

---

## 5. Getting a Specific Book

In the real world, we may find use cases where we want to get data of a specific item. In our case, let's say we want to get the details of a specific book.

To get a specific book, we need to write the logic to get a specific book from the hmap.

Let's update our BookRepository interface with an abstract method to get a specific book.

```java
public interface BookRepository {

ArrayList<Book> getBooks();
Book getBookById(int bookId);

}
```

We have added an abstract method getBookById() that takes a bookId as an argument and returns the Book object.

In the BookService class we have to implement the getBookById() method as it is an abstract method.

In the above code, the HashMap method get() returns a single Book object based on the bookId.

Now, our logic for a specific book from the hmap is ready. So, we have to write a method in our BookController class to handle the HTTP request.

Let's use /books/{bookId} as a path to identify a single book resource, where bookId is a path parameter.

For example:

https://example.com/books/1

Here the bookId is 1.

So, let's create a getBookById() method that handles the HTTP GET request.

```java
@GetMapping("/books/{bookId}")
public Book getBookById(@PathVariable("bookId") int bookId) {
return bookService.getBookById();
}
```

In above code, we have used new annotation called @PathVariable.

In Spring Boot, the @PathVariable annotation is used to extract a value from the URL path and bind it to a method parameter.

---

## 6. Handling Exceptions

In Spring Boot we can use the ResponseStatusException to raise an exception. It is used to signal that a request has failed and that a specific HTTP status code should be returned as the response.

In our case, if a book requested is not found the hmap.get(id) returns a null value. So, let's raise a ResponseStatusException when the hmap.get(id) returns a null value.

```java
throw new ResponseStatusException(HttpStatus.NOT_FOUND);
```

In the above code, if the hmap.get(id) returns null, the method throws a ResponseStatusException with the HttpStatus.NOT_FOUND status code, which indicates that the requested resource was not found.

---

## Summary

Getting Books

* The Spring Boot annotation @GetMapping() is used to handle the HTTP GET requests to the specified path.
* The @PathVariable annotation is used to bind the path parameter to the method parameter.

Handling Exceptions

* In Spring Boot we can use ResponseStatusException to raise an exception with a specified status code.
* The status code HttpStatus.NOT_FOUND specifies that the requested resource was not found.


---

## Conclusion

This project serves as a foundational step in learning Spring Boot REST APIs. It establishes a clear understanding of how backend services are structured and how data is exposed through HTTP endpoints. This forms the base for implementing full CRUD operations and integrating with databases in the next phase.
