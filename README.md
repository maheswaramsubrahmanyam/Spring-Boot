# Spring-Boot

<img width="393" height="826" alt="image" src="https://github.com/user-attachments/assets/5f45ffee-18f9-4ca5-b267-743f8b53e7a2" />

BookService.java
```java
package com.example.goodreads;

import java.util.*;
import com.example.goodreads.Books;
import com.example.goodreads.BookRepository;

public class BookService implements BookRepository {

    private HashMap<Integer, Books> hmap = new HashMap<>();

    public BookService() {
        Books b1 = new Books(1, "Harry poter", "harry_potter.jpeg");
        Books b2 = new Books(2, "Rise", "rise.jpeg");

        hmap.put(b1.getId(), b1);
        hmap.put(b1.getId(), b2);

    }

    @Override
    public ArrayList<Books> getBooks() {
        Collection<Books> bookCollection = hmap.values();
        ArrayList<Books> book = new ArrayList<>(bookCollection);

    }

}
```
Books.java
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

    public void getImageurl(String Imageurl) {
        this.Imageurl = Imageurl;
    }

}
```

