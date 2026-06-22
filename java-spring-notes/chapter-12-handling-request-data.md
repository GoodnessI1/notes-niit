# Chapter 12: Handling Request Data in Spring Boot

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

This is one of the most practical chapters in Spring. Up until now, your endpoints have looked like:

```java
@GetMapping("/students")
public List<Student> getStudents() {
    return service.getStudents();
}
```

But real applications need data *from* users — a specific ID, a search filter, or a whole object to create. This chapter covers the three ways that data arrives, and how Spring gets it into your Java method.

## 🎯 Lesson Goal

Students should understand:

- `@PathVariable`
- `@RequestParam`
- `@RequestBody`
- When to use each
- How Spring maps request data to Java objects

## 🗺️ Lesson Roadmap

1. Path Variables
2. Request Parameters
3. Path Variable vs Request Param
4. Request Body
5. Visual Summary
6. Real CRUD Examples
7. Common Student Mistakes
8. Mini Exercise
9. Closing Statement

---

> 💬 **Ask:** "If the URL contains data, how can my Java method access it?"

**Three main ways data arrives:**

```
Client Request
      ↓
Path Variable
      ↓
Request Parameter
      ↓
Request Body
```

---

## 1. Path Variables

**What is a path variable?** A value embedded inside the URL path.

**Example:** `GET /students/1` — here, `1` is part of the URL itself.

**Spring solution:**

```java
@GetMapping("/students/{id}")
public String getStudent(@PathVariable Integer id) {
    return "Student ID = " + id;
}
```

**Request:** `GET /students/5`
**Output:** `Student ID = 5`

**What's happening?** Spring sees `@PathVariable Integer id` and says: "Take the value from the URL and place it into this parameter."

> 🧠 **Teaching line:** "Path variables capture values embedded in the URL."

**Real example** — each of these refers to a specific student:
```
/students/1
/students/2
/students/3
```

---

## 2. Request Parameters

Also called **query parameters**.

**Example:** `GET /students?name=John` — breaks down into `/students` + `?name=John`.

**Spring solution:**

```java
@GetMapping("/students")
public String getStudentByName(@RequestParam String name) {
    return "Searching for " + name;
}
```

**Request:** `GET /students?name=Mary`
**Response:** `Searching for Mary`

> 🧠 **Teaching line:** "Request parameters are optional pieces of information attached to a URL."

**Multiple parameters:**

```
GET /students?name=John&age=20
```

```java
@GetMapping("/students")
public String searchStudent(
        @RequestParam String name,
        @RequestParam Integer age) {
    return name + " " + age;
}
```

---

## 3. Path Variable vs Request Param

> 📌 Students confuse these constantly.

| | Path Variable | Request Param |
|---|---|---|
| Example | `/students/5` | `/students?name=John` |
| Meaning | "Give me student number 5" | "Search students using this filter" |

> 🧠 **Teaching line:** "Path variables identify resources. Request parameters filter resources."

---

## 4. Request Body

This is **the big one** — most business applications use this constantly.

Imagine creating a student. The client sends:

```json
{
  "name": "John",
  "age": 20
}
```

**How do we receive it?**

```java
package org.codex.dto;

public class StudentRequest {

    private String name;
    private Integer age;

    public StudentRequest() {
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

> 📌 Notice this is a **separate class** from the `Student` entity in Chapter 11 — it has no `id`. The client shouldn't be able to set an `id`; the database assigns that. Keeping the request shape separate from the persistence model like this is a common real-world pattern, usually called a **DTO** (Data Transfer Object).

**Controller:**

```java
@PostMapping("/students")
public StudentRequest createStudent(@RequestBody StudentRequest student) {
    return student;
}
```

**Request:** `POST /students` with body:
```json
{
  "name": "John",
  "age": 20
}
```

**Response:**
```json
{
  "name": "John",
  "age": 20
}
```

**What happened?** Spring automatically:
1. Read the JSON
2. Created a `StudentRequest` object
3. Filled in its fields
4. Passed the object into the method

> 🧠 **Teaching line:** "`@RequestBody` converts incoming JSON into Java objects."

---

## 5. Visual Summary

| Style | URL / Body | Annotation |
|---|---|---|
| Path Variable | `/students/5` | `@PathVariable Integer id` |
| Request Param | `/students?name=John` | `@RequestParam String name` |
| Request Body | `{ "name": "John" }` | `@RequestBody StudentRequest student` |

---

## 6. Real CRUD Examples

**Read one student:**
```java
@GetMapping("/students/{id}")
public Student getStudent(@PathVariable Integer id) {
    return service.getStudent(id);
}
```

**Search students:**
```java
@GetMapping("/students")
public List<Student> searchStudents(@RequestParam String name) {
    return service.searchByName(name);
}
```

**Create a student:**
```java
@PostMapping("/students")
public Student createStudent(@RequestBody StudentRequest student) {
    return service.create(student);
}
```

> 📌 Here, `service.create(student)` would convert the incoming `StudentRequest` into a `Student` entity before saving it. We'll look at exactly how that conversion works in a later chapter — for now, focus on how the data arrives.

---

## 7. Common Student Mistakes

❌ **Mistake 1 — Missing `@PathVariable`:**
```java
@GetMapping("/students/{id}")
public Student getStudent(Integer id) { // ❌ missing @PathVariable
    ...
}
```

❌ **Mistake 2 — Using `@RequestParam` for a path segment:**
```java
@GetMapping("/students/{id}")
public Student getStudent(@RequestParam Integer id) { // ❌ should be @PathVariable
    ...
}
```

❌ **Mistake 3 — Forgetting `@RequestBody` on POST requests:**
```java
@PostMapping("/students")
public Student createStudent(StudentRequest student) { // ❌ missing @RequestBody
    ...
}
```

---

## 8. Mini Exercise

Create these endpoints for a `Book` resource:

1. `GET /books/10` — using `@PathVariable`
2. `GET /books?author=James` — using `@RequestParam`
3. `POST /books` — using `@RequestBody`

<details>
<summary>💡 Click to reveal a suggested solution</summary>

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
package org.codex;

import org.codex.dto.BookRequest;
import org.springframework.web.bind.annotation.*;

@RestController
public class BookController {

    @GetMapping("/books/{id}")
    public String getBook(@PathVariable Integer id) {
        return "Book ID = " + id;
    }

    @GetMapping("/books")
    public String searchBooks(@RequestParam String author) {
        return "Searching books by " + author;
    }

    @PostMapping("/books")
    public BookRequest createBook(@RequestBody BookRequest book) {
        return book;
    }
}
```

</details>

---

## 9. Closing Statement

## ✅ Key Takeaways

✅ `@PathVariable` — extracts a value embedded in the URL path
✅ `@RequestParam` — extracts an optional query string value
✅ `@RequestBody` — converts incoming JSON into a Java object
✅ Path variables identify a specific resource; request parameters filter a collection
✅ Keeping a separate request DTO (like `StudentRequest`) apart from your JPA entity avoids letting clients set server-controlled fields like `id`

---

**← Previous: [Chapter 11 — Spring Data JPA & Database Integration](./chapter-11-spring-data-jpa.md)** | **Next: Chapter 13 →**
