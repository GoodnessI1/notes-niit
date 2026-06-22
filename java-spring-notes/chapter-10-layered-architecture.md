# Chapter 10: Layered Architecture (Controller → Service → Repository)

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

Chapter 9 put all the logic for an endpoint directly inside the controller. That's fine for a tiny example, but it breaks down fast in a real app. This chapter introduces the standard 3-layer structure most Spring Boot applications use to keep things organized. No new dependencies needed — this builds directly on the project from Chapters 8–9.

## 🎯 Lesson Objective

Students should understand:

- Why putting everything in one class becomes a problem
- The responsibility of each layer: Controller, Service, Repository
- How to wire the three layers together using constructor injection
- The request flow from browser to database and back
- Common beginner mistakes when splitting responsibilities

## 🗺️ Lesson Roadmap

1. Start With the Bad Example
2. What Is Layered Architecture?
3. Controller Layer
4. Service Layer
5. Controller + Service Together
6. Repository Layer
7. Complete Flow
8. Mini Exercise
9. Closing Statement

---

## 1. Start With the Bad Example

> 💬 **Ask:** "What if we put ALL logic inside one class?"

```java
@RestController
public class StudentController {

    @GetMapping("/students")
    public List<String> getStudents() {

        // Database logic
        // Business logic
        // Validation
        // Formatting
        // Everything here 😭

        return List.of("John", "Mary");
    }
}
```

❌ **The problems:**
- Hard to maintain
- Hard to test
- Hard to scale
- Confusing responsibilities

> 🧠 **Say:** "Just because code works doesn't mean the design is good."

---

## 2. What Is Layered Architecture?

> 🧠 **Say:** "Layered architecture separates application responsibilities into different layers."

**The 3 main layers:**

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

> 🧠 **Say:** "Each layer has one responsibility."

---

## 3. Controller Layer 🌐

✅ **Responsibility:** Handles HTTP requests and responses.

```java
@RestController
public class StudentController {

    @GetMapping("/students")
    public List<String> getStudents() {
        return List.of("John", "Mary");
    }
}
```

❌ **What the controller should NOT do:**
- Database logic
- Complex business logic

> 🧠 **Say:** "Controllers should coordinate, not think."

---

## 4. Service Layer 🧠

✅ **Responsibility:** Contains business logic.

```java
import org.springframework.stereotype.Service;

@Service
public class StudentService {

    public List<String> getStudents() {
        return List.of("John", "Mary");
    }
}
```

**What belongs here:**
- Calculations
- Rules
- Validation
- Decision-making

> 🧠 **Say:** "The service layer is the brain of the application."

---

## 5. Controller + Service Together

```java
@RestController
public class StudentController {

    private final StudentService service;

    public StudentController(StudentService service) {
        this.service = service;
    }

    @GetMapping("/students")
    public List<String> getStudents() {
        return service.getStudents();
    }
}
```

**What just happened?**
- **Controller:** receives the request, delegates the work
- **Service:** handles the logic

> 🧠 **Say:** "Controllers delegate responsibility to services."

---

## 6. Repository Layer 🗄️

✅ **Responsibility:** Handles data access.

```java
import org.springframework.stereotype.Repository;

@Repository
public class StudentRepository {

    public List<String> findAll() {
        return List.of("John", "Mary");
    }
}
```

> 🧠 **Say:** "Repositories talk to the database."

---

## 7. Complete Flow

**Repository:**
```java
@Repository
public class StudentRepository {

    public List<String> findAll() {
        return List.of("John", "Mary");
    }
}
```

**Service:**
```java
@Service
public class StudentService {

    private final StudentRepository repository;

    public StudentService(StudentRepository repository) {
        this.repository = repository;
    }

    public List<String> getStudents() {
        return repository.findAll();
    }
}
```

**Controller:**
```java
@RestController
public class StudentController {

    private final StudentService service;

    public StudentController(StudentService service) {
        this.service = service;
    }

    @GetMapping("/students")
    public List<String> getStudents() {
        return service.getStudents();
    }
}
```

**Request flow:**

```
Browser Request
      ↓
Controller
      ↓
Service
      ↓
Repository
      ↓
Data Returned
```

> 🧠 **Say:** "Requests move downward, responses move upward."

### Why This Design Is Powerful

✅ **1. Easier testing** — you can test each layer independently
✅ **2. Easier maintenance** — logic is organized
✅ **3. Easier scaling** — large teams can work separately
✅ **4. Cleaner code** — each class has one purpose

> 🧠 **Say:** "Good architecture reduces chaos."

### Common Student Mistakes

❌ **Putting everything in the Controller** — a very common beginner problem
❌ **Repository containing business logic** — repositories should focus on data only
❌ **Services returning HTTP responses** — that belongs to the controller layer

### Real-World Analogy

| Layer | Real-World Role |
|---|---|
| Controller | Receptionist |
| Service | Manager |
| Repository | File clerk |
| Database | Storage room |

> 🧠 **Say:** "The controller receives requests, the service decides, the repository retrieves."

---

## 8. Mini Exercise

Build the full 3-layer chain for a new resource: **books**.

1. Create a `BookRepository` (`@Repository`) with a `findAll()` method returning a hardcoded list of book titles
2. Create a `BookService` (`@Service`) that depends on `BookRepository` and exposes a `getBooks()` method
3. Create a `BookController` (`@RestController`) with a `GET /books` endpoint that delegates to `BookService`
4. Run it and confirm the endpoint returns your list

<details>
<summary>💡 Click to reveal a suggested solution</summary>

```java
package org.codex;

import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public class BookRepository {

    public List<String> findAll() {
        return List.of("Clean Code", "Effective Java");
    }
}
```

```java
package org.codex;

import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class BookService {

    private final BookRepository repository;

    public BookService(BookRepository repository) {
        this.repository = repository;
    }

    public List<String> getBooks() {
        return repository.findAll();
    }
}
```

```java
package org.codex;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
public class BookController {

    private final BookService service;

    public BookController(BookService service) {
        this.service = service;
    }

    @GetMapping("/books")
    public List<String> getBooks() {
        return service.getBooks();
    }
}
```

✅ **Response:**
```json
["Clean Code", "Effective Java"]
```

</details>

---

## 9. Closing Statement

## ✅ Key Takeaways

- **Controller** — handles requests, doesn't think
- **Service** — business logic, the brain of the app
- **Repository** — data access, doesn't reason about business rules
- Each layer is wired to the next via constructor injection — requests flow down, responses flow up
- Splitting responsibilities this way makes code easier to test, maintain, and scale

> 🧠 "Layered architecture separates concerns to keep applications clean and scalable."

---

**← Previous: [Chapter 9 — Building Your First REST API with Spring Boot](./chapter-9-building-your-first-rest-api.md)** | **Next: Chapter 11 →**
