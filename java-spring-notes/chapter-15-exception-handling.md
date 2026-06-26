# Chapter 15: Exception Handling

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

When something goes wrong — a missing student, a bad calculation — an unhandled error currently means a raw, ugly error page. This chapter replaces that with clean, predictable error responses.

## 🎯 Lesson Goal

Students should understand:

- What exceptions are
- Why exception handling matters
- `try`-`catch`
- Spring exception handling
- `@ExceptionHandler`
- `@ControllerAdvice`

## 🗺️ Lesson Roadmap

1. Start With the Problem
2. What Is an Exception?
3. Traditional Java Handling
4. Why Spring Needs Exception Handling
5. Creating a Custom Exception
6. `@ExceptionHandler`
7. The Problem With That
8. Enter `@ControllerAdvice`
9. Common Exceptions
10. Visual Flow
11. Mini Exercise
12. Closing Statement

---

## 1. Start With the Problem

Suppose a client requests:

```
GET /students/999
```

But student 999 doesn't exist. Your service:

```java
public Student getStudent(Integer id) {
    return repository.findById(id).orElseThrow();
}
```

Spring throws `NoSuchElementException`, and the user receives a huge, ugly error page.

> 🧠 **Teaching line:** "Exceptions are unexpected situations that prevent normal execution."

---

## 2. What Is an Exception?

An exception is an object that represents an error condition.

**Example:**
```java
int result = 10 / 0;
```

Produces: `ArithmeticException`

---

## 3. Traditional Java Handling

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
}
```

> 🧠 **Teaching line:** "Exceptions allow programs to react to problems instead of crashing."

---

## 4. Why Spring Needs Exception Handling

```java
@GetMapping("/students/{id}")
public Student getStudent(@PathVariable Integer id) {
    return service.getStudent(id);
}
```

- Student exists? ✅ Return the student
- Student missing? ❌ Exception thrown

Without handling: `500 Internal Server Error`.

---

## 5. Creating a Custom Exception

Instead of:
```java
throw new RuntimeException();
```

Create:
```java
package org.codex.exception;

public class StudentNotFoundException extends RuntimeException {

    public StudentNotFoundException(String message) {
        super(message);
    }
}
```

**Usage:**
```java
public Student getStudent(Integer id) {
    return repository.findById(id)
            .orElseThrow(() -> new StudentNotFoundException("Student not found"));
}
```

> 📌 This refines `getStudent()` from Chapter 13, which previously returned `null` when a student wasn't found. Throwing a proper exception is far better — it's explicit about what went wrong, instead of leaving the caller to guess why they got `null`.

> 🧠 **Teaching line:** "Custom exceptions make errors meaningful."

---

## 6. `@ExceptionHandler`

Handle exceptions inside a controller:

```java
@ExceptionHandler(StudentNotFoundException.class)
public String handleStudentNotFound(StudentNotFoundException ex) {
    return ex.getMessage();
}
```

When the exception occurs, `"Student not found"` is returned.

> 📌 Notice this returns a plain `String`, which defaults to a `200 OK` status — misleading for an actual error. The `@ControllerAdvice` version below fixes this with a proper status code.

---

## 7. The Problem With That

What if `StudentController`, `BookController`, and `UserController` all need the same handling? Duplicate code.

---

## 8. Enter `@ControllerAdvice`

This is **global** exception handling.

```java
@ControllerAdvice
public class GlobalExceptionHandler {
}
```

Spring says: "Use this class for handling exceptions across the entire application."

**Example:**
```java
package org.codex;

import org.codex.exception.StudentNotFoundException;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(StudentNotFoundException.class)
    public ResponseEntity<String> handleStudentNotFound(StudentNotFoundException ex) {
        return ResponseEntity.status(404).body(ex.getMessage());
    }
}
```

Now every controller benefits.

**Real API response:**

Request: `GET /students/999`

Response: `404 Not Found`
```json
{
  "message": "Student not found"
}
```

> 🧠 **Teaching line:** "`@ControllerAdvice` centralizes error handling."

---

## 9. Common Exceptions

| Exception | Meaning |
|---|---|
| `NullPointerException` | Object is null |
| `ArithmeticException` | Illegal arithmetic |
| `IllegalArgumentException` | Bad argument |
| `RuntimeException` | Generic runtime error |

---

## 10. Visual Flow

```
Request
   ↓
Controller
   ↓
Service
   ↓
Exception Thrown
   ↓
@ControllerAdvice
   ↓
Friendly Response
```

---

## 11. Mini Exercise

1. Create a `BookNotFoundException`
2. Update `BookService.getBook()` (from Chapter 13) to throw it instead of returning `null`
3. Add a handler for it in `GlobalExceptionHandler`, returning a `404`

<details>
<summary>💡 Click to reveal a suggested solution</summary>

```java
package org.codex.exception;

public class BookNotFoundException extends RuntimeException {

    public BookNotFoundException(String message) {
        super(message);
    }
}
```

```java
// BookService.java — updated getBook()
public Book getBook(Long id) {
    return repository.findById(id)
            .orElseThrow(() -> new BookNotFoundException("Book not found"));
}
```

```java
// GlobalExceptionHandler.java — add alongside the existing handler
@ExceptionHandler(BookNotFoundException.class)
public ResponseEntity<String> handleBookNotFound(BookNotFoundException ex) {
    return ResponseEntity.status(404).body(ex.getMessage());
}
```

`GET /books/999` for a nonexistent book now returns a clean `404` with `"Book not found"`, instead of a raw error page.

</details>

---

## 12. Closing Statement

## ✅ Key Takeaways

- An exception is an object representing an error condition — it stops normal execution
- Custom exceptions (extending `RuntimeException`) make errors meaningful instead of generic
- `@ExceptionHandler` handles exceptions per-controller; `@ControllerAdvice` handles them globally, avoiding duplicate code
- `ResponseEntity` lets you control both the status code and the body of an error response
- Without handling, an unhandled exception becomes a raw `500 Internal Server Error`

> 🧠 "Exception handling converts application errors into meaningful responses."

---

**← Previous: [Chapter 14 — Validation](./chapter-14-validation.md)** | **Next: Chapter 16 →**
