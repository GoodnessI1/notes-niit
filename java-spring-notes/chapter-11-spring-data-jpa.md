# Chapter 11: Spring Data JPA & Database Integration

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

Every example so far has used hardcoded `List.of(...)` data that resets every time the app restarts. This chapter replaces that with a real database, using Spring Data JPA to avoid writing SQL by hand.

## 🎯 Lesson Objective

Students should understand:

- Why databases are needed
- What JPA is
- What an Entity is
- How Spring talks to a database
- How repositories become powerful

## 🧰 Quick Setup (MySQL)

**1. Add dependencies to `pom.xml`:**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <scope>runtime</scope>
</dependency>
```

**2. Create a database** (in MySQL Workbench, the CLI, or any client):

```sql
CREATE DATABASE codex_school;
```

**3. Configure `src/main/resources/application.properties`:**

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/codex_school
spring.datasource.username=root
spring.datasource.password=yourpassword
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

> 📌 `ddl-auto=update` lets Hibernate create and update tables automatically based on your `@Entity` classes. It's convenient for learning — in a real production app, you'd usually manage schema changes more carefully (e.g. with migration tools like Flyway).

## 🗺️ Lesson Roadmap

1. Why Databases
2. What Is a Database?
3. What Is JPA?
4. What Is Hibernate?
5. Entity
6. Repository Revolution
7. What Do We Get For Free?
8. Understanding the Generics
9. Mini Exercise
10. Closing Statement

---

## 1. Why Databases

Databases exist because data stored in variables is lost the moment the application stops running.

> 🧠 **Say:** "Variables live in memory. Databases live beyond the application's lifetime."

---

## 2. What Is a Database?

A database is a system designed to store, organize, and retrieve data.

**Popular databases:**
- MySQL
- PostgreSQL
- Oracle Database
- MongoDB

---

## 3. What Is JPA?

❌ JPA is **NOT** a database
❌ JPA is **NOT** Spring
✅ JPA — **Java Persistence API** — is a specification (a set of rules) for mapping Java objects to database tables

> 🧠 **Say:** "JPA defines the rules. Hibernate does the work."

---

## 4. What Is Hibernate?

Hibernate is an intermediary between Spring and the database.

**Where does Hibernate fit?**

```
Application
     ↓
Spring Data JPA
     ↓
Hibernate
     ↓
Database
```

> 🧠 **Teaching line:** "Spring talks to Hibernate. Hibernate talks to the database."

---

## 5. Entity

An Entity represents a database table.

```java
package org.codex.model;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;

@Entity
public class Student {

    @Id
    private Integer id;
    private String name;
    private Integer age;

    public Student() {
    }

    public Student(Integer id, String name, Integer age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```

> 📌 This `Student` supersedes the plain POJO version from Chapter 9 — from here on, this JPA-backed version is what your repository, service, and controller layers work with.

**What does this create?**

| id | name | age |
|---|---|---|
| 1 | John | 20 |
| 2 | Mary | 22 |

> 🧠 **Say:** "An Entity is a Java representation of a database table."

---

## 6. Repository Revolution

A Repository is a component responsible for accessing and managing data — retrieving, storing, updating, and deleting it from a data source. It acts as a bridge between the application's business logic and the underlying storage system.

The data source could be:
- A database
- A file
- An API
- In-memory data

**Before** — we manually wrote every method:
```java
@Repository
public class StudentRepository {
}
```

**Now:**
```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface StudentRepository
        extends JpaRepository<Student, Integer> {
}
```

No implementation. No SQL. No code.

> 🧠 **Say:** "Yes. Spring generates the implementation automatically."

---

## 7. What Do We Get For Free?

By extending `JpaRepository<Student, Integer>`, we automatically get:
- `save()`
- `findAll()`
- `findById()`
- `deleteById()`
- `count()`
- `existsById()`

**Example:**
```java
studentRepository.findAll();
studentRepository.save(student);
studentRepository.deleteById(1);
```

> 🧠 **Say:** "Spring writes the CRUD code so you don't have to."

---

## 8. Understanding the Generics

`JpaRepository<Student, Integer>` — what does this mean?

- **First parameter** — `Student` → the entity type
- **Second parameter** — `Integer` → the primary key type

**Example:**
```java
@Entity
public class Book {
    @Id
    private Long id;
}
```

A repository for this would be `JpaRepository<Book, Long>`.

> 🧠 **Say:** "The first generic is the table. The second generic is the ID type."
>
> "Spring Data JPA lets us work with Java objects while Hibernate handles the SQL behind the scenes."

---

## 9. Mini Exercise

1. Create a `Book` entity (`@Entity`) with `id`, `title`, and `author` fields
2. Create a `BookRepository` interface extending `JpaRepository<Book, Long>`
3. Use a `CommandLineRunner` to save two books and print all books from the repository
4. Run it, then check MySQL — a `book` table should now exist with your data

<details>
<summary>💡 Click to reveal a suggested solution</summary>

```java
package org.codex.model;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;

@Entity
public class Book {

    @Id
    private Long id;
    private String title;
    private String author;

    public Book() {
    }

    public Book(Long id, String title, String author) {
        this.id = id;
        this.title = title;
        this.author = author;
    }

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
}
```

```java
package org.codex.repository;

import org.codex.model.Book;
import org.springframework.data.jpa.repository.JpaRepository;

public interface BookRepository extends JpaRepository<Book, Long> {
}
```

```java
package org.codex;

import org.codex.model.Book;
import org.codex.repository.BookRepository;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class BookRunner implements CommandLineRunner {

    private final BookRepository bookRepository;

    public BookRunner(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }

    @Override
    public void run(String... args) {
        bookRepository.save(new Book(1L, "Clean Code", "Robert C. Martin"));
        bookRepository.save(new Book(2L, "Effective Java", "Joshua Bloch"));

        bookRepository.findAll().forEach(book ->
                System.out.println(book.getTitle() + " by " + book.getAuthor())
        );
    }
}
```

✅ **Output:**
```
Clean Code by Robert C. Martin
Effective Java by Joshua Bloch
```

Check MySQL afterward — Hibernate will have created a `book` table for you automatically, thanks to `ddl-auto=update`.

</details>

---

## 10. Closing Statement

## ✅ Key Takeaways

- Databases outlive the application — variables in memory don't
- JPA is a specification, not a database or a framework — Hibernate is what actually implements it
- An `@Entity` class maps directly to a database table; each field becomes a column
- Extending `JpaRepository<Entity, IdType>` gives you `save()`, `findAll()`, `findById()`, `deleteById()`, and more — with zero implementation code
- The repository's generic parameters are the entity type and its primary key type, in that order

---

**← Previous: [Chapter 10 — Layered Architecture](./chapter-10-layered-architecture.md)** | **Next: Chapter 12 →**
