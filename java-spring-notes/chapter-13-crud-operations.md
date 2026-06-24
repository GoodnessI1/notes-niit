# Chapter 13: CRUD Operations with Spring Boot

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

This chapter ties together almost everything covered so far. By the end, students will understand how to build a complete API that can create, read, update, and delete data — a **CRUD** API.

## 🎯 Lesson Goal

Students should understand:

- What CRUD means
- How CRUD maps to HTTP methods
- How Controller, Service, and Repository work together
- The standard structure used in real applications

## 🗺️ Lesson Roadmap

1. What Is CRUD?
2. CRUD and HTTP Methods
3. The Student Entity
4. Repository
5. Service Layer
6. Create
7. Read
8. Update
9. Delete
10. Complete CRUD Flow
11. Common Student Confusions
12. Mini Exercise
13. Closing Statement

---

## 1. What Is CRUD?

CRUD stands for:

| Letter | Meaning |
|---|---|
| C | Create |
| R | Read |
| U | Update |
| D | Delete |

Think about a Student Management System — you need to add students, view students, edit students, and remove students. That's CRUD.

> 🧠 **Teaching line:** "Almost every business application is a CRUD application at its core."

---

## 2. CRUD and HTTP Methods

REST APIs map CRUD to HTTP methods:

| CRUD | HTTP Method |
|---|---|
| Create | POST |
| Read | GET |
| Update | PUT |
| Delete | DELETE |

> 🧠 **Teaching line:** "CRUD describes the operation; HTTP describes how we perform it."

---

## 3. The Student Entity

```java
package org.codex.model;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
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

> 📌 We're adding `@GeneratedValue` here, refining the entity from Chapter 11 — this lets the database assign IDs automatically, which is exactly what makes the `StudentRequest` DTO from Chapter 12 work cleanly for Create.

---

## 4. Repository

```java
package org.codex.repository;

import org.codex.model.Student;
import org.springframework.data.jpa.repository.JpaRepository;

public interface StudentRepository extends JpaRepository<Student, Integer> {
}
```

No implementation — Spring generates it automatically.

---

## 5. Service Layer

```java
package org.codex;

import org.codex.repository.StudentRepository;
import org.springframework.stereotype.Service;

@Service
public class StudentService {

    private final StudentRepository repository;

    public StudentService(StudentRepository repository) {
        this.repository = repository;
    }
}
```

We'll now build the CRUD methods one at a time.

---

## 6. Create 🟢

**Service:**
```java
public Student createStudent(StudentRequest request) {
    Student student = new Student();
    student.setName(request.getName());
    student.setAge(request.getAge());

    return repository.save(student);
}
```

**Controller:**
```java
@PostMapping("/students")
public Student createStudent(@RequestBody StudentRequest student) {
    return service.createStudent(student);
}
```

**Request:** `POST /students`
```json
{
  "name": "John",
  "age": 20
}
```

**Response** — note the database-assigned `id`:
```json
{
  "id": 1,
  "name": "John",
  "age": 20
}
```

**What happens?**
```
Controller
    ↓
Service
    ↓
Repository.save()
    ↓
Database
```

> 🧠 **Teaching line:** "`save()` inserts a new record when the ID is null — Hibernate knows there's nothing to update yet."

---

## 7. Read 🔵

**Get all students:**

```java
// Service
public List<Student> getStudents() {
    return repository.findAll();
}
```

```java
// Controller
@GetMapping("/students")
public List<Student> getStudents() {
    return service.getStudents();
}
```

**Request:** `GET /students`
**Response:**
```json
[
  {
    "id": 1,
    "name": "John",
    "age": 20
  }
]
```

**Get one student:**

```java
// Service
public Student getStudent(Integer id) {
    return repository.findById(id).orElse(null);
}
```

```java
// Controller
@GetMapping("/students/{id}")
public Student getStudent(@PathVariable Integer id) {
    return service.getStudent(id);
}
```

**Request:** `GET /students/1`

> 🧠 **Teaching line:** "`findById()` returns an `Optional` because the record may not exist."

---

## 8. Update 🟡

Students usually find this surprising.

> 💬 **Say:** "Spring uses `save()` again. Wait... didn't we use `save()` for Create? Yes. Why?"

Hibernate checks: does this ID already exist?
- If **NO** → `INSERT`
- If **YES** → `UPDATE`

**Service:**
```java
public Student updateStudent(Integer id, StudentRequest request) {
    Student updatedStudent = new Student();
    updatedStudent.setId(id);
    updatedStudent.setName(request.getName());
    updatedStudent.setAge(request.getAge());

    return repository.save(updatedStudent);
}
```

**Controller:**
```java
@PutMapping("/students/{id}")
public Student updateStudent(
        @PathVariable Integer id,
        @RequestBody StudentRequest student) {

    return service.updateStudent(id, student);
}
```

**Request:** `PUT /students/1`
```json
{
  "name": "Michael",
  "age": 25
}
```

> 🧠 **Teaching line:** "`save()` can both insert and update — the presence of an ID is what decides which one happens. Create lets the database assign the ID (so it's null going in); Update explicitly sets the existing ID first."

---

## 9. Delete 🔴

**Service:**
```java
public void deleteStudent(Integer id) {
    repository.deleteById(id);
}
```

**Controller:**
```java
@DeleteMapping("/students/{id}")
public void deleteStudent(@PathVariable Integer id) {
    service.deleteStudent(id);
}
```

**Request:** `DELETE /students/1`

**What happens?**
```sql
DELETE FROM student WHERE id = 1;
```

> 🧠 **Teaching line:** "`deleteById()` removes a row using its primary key."

---

## 10. Complete CRUD Flow

| Operation | Request |
|---|---|
| Create | `POST /students` |
| Read All | `GET /students` |
| Read One | `GET /students/1` |
| Update | `PUT /students/1` |
| Delete | `DELETE /students/1` |

**Visual flow** — every CRUD operation follows this same path:

```
Client
   ↓
Controller
   ↓
Service
   ↓
Repository
   ↓
Database
```

---

## 11. Common Student Confusions

> ❓ "Why does `save()` do both Create and Update?"
> 👉 Because JPA checks whether the entity's ID already exists in the database.

> ❓ "Why use PUT for Update?"
> 👉 Because REST conventions define PUT as the standard update operation.

> ❓ "Why use `@PathVariable` for Update/Delete?"
> 👉 Because you need to identify exactly which resource you're modifying.

> ❓ "Why does Create's response include an `id` the request never sent?"
> 👉 Because `@GeneratedValue` lets the database assign it. Hibernate fills it in during `save()`, and the now-complete entity — including its new `id` — is what gets returned.

---

## 12. Mini Exercise

Build a full CRUD API for **Books**.

Entity fields: `id`, `title`, `author`

Create endpoints:
- `POST /books`
- `GET /books`
- `GET /books/{id}`
- `PUT /books/{id}`
- `DELETE /books/{id}`

<details>
<summary>💡 Click to reveal a suggested solution</summary>

```java
package org.codex.model;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String author;

    public Book() {
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
package org.codex.dto;

public class BookRequest {

    private String title;
    private String author;

    public BookRequest() {
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

import org.codex.dto.BookRequest;
import org.codex.model.Book;
import org.codex.repository.BookRepository;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class BookService {

    private final BookRepository repository;

    public BookService(BookRepository repository) {
        this.repository = repository;
    }

    public Book createBook(BookRequest request) {
        Book book = new Book();
        book.setTitle(request.getTitle());
        book.setAuthor(request.getAuthor());
        return repository.save(book);
    }

    public List<Book> getBooks() {
        return repository.findAll();
    }

    public Book getBook(Long id) {
        return repository.findById(id).orElse(null);
    }

    public Book updateBook(Long id, BookRequest request) {
        Book book = new Book();
        book.setId(id);
        book.setTitle(request.getTitle());
        book.setAuthor(request.getAuthor());
        return repository.save(book);
    }

    public void deleteBook(Long id) {
        repository.deleteById(id);
    }
}
```

```java
package org.codex;

import org.codex.dto.BookRequest;
import org.codex.model.Book;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
public class BookController {

    private final BookService service;

    public BookController(BookService service) {
        this.service = service;
    }

    @PostMapping("/books")
    public Book createBook(@RequestBody BookRequest book) {
        return service.createBook(book);
    }

    @GetMapping("/books")
    public List<Book> getBooks() {
        return service.getBooks();
    }

    @GetMapping("/books/{id}")
    public Book getBook(@PathVariable Long id) {
        return service.getBook(id);
    }

    @PutMapping("/books/{id}")
    public Book updateBook(@PathVariable Long id, @RequestBody BookRequest book) {
        return service.updateBook(id, book);
    }

    @DeleteMapping("/books/{id}")
    public void deleteBook(@PathVariable Long id) {
        service.deleteBook(id);
    }
}
```

</details>

---

## 13. Closing Statement

## ✅ Key Takeaways

✅ CRUD = Create, Read, Update, Delete — maps to POST, GET, PUT, DELETE
✅ `save()` handles both insert and update — the entity's ID is what decides which one happens
✅ `findAll()`, `findById()`, and `deleteById()` come free from `JpaRepository`
✅ A clean request DTO (no `id`) plus `@GeneratedValue` on the entity keeps clients from setting server-controlled fields
✅ Every CRUD operation follows the same flow: Controller → Service → Repository → Database

> 🧠 "CRUD APIs allow clients to create, retrieve, update, and delete resources through standardized HTTP operations."

---

**← Previous: [Chapter 12 — Handling Request Data in Spring Boot](./chapter-12-handling-request-data.md)**
