# Chapter 9: Building Your First REST API with Spring Boot

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

Chapter 8 got a Spring Boot app running internally. Now we expose that app to the outside world: a REST API that other systems — a frontend, a mobile app, Postman — can actually talk to over HTTP.

## 🎯 Lesson Objective

Students should understand:

- What a REST API is and why it's needed
- How to use `@RestController` and `@GetMapping` to expose endpoints
- How Java objects get automatically serialized to JSON
- How to return lists of objects as JSON arrays
- The main HTTP methods (GET, POST, PUT, DELETE)
- How to test an API with a browser and with Postman

## 🧰 Quick Setup

> 📌 This chapter needs the **Spring Web** dependency, which Chapter 8 didn't require. If you're continuing that same project, add `spring-boot-starter-web` to your `pom.xml` — or regenerate the project from [Spring Initializr](https://start.spring.io) with "Spring Web" checked this time. Without it, `@RestController` and `@GetMapping` won't be available, and there's no embedded server to actually hit `localhost:8080`.

## 🗺️ Lesson Roadmap

1. Start With This Question
2. What Is a REST API?
3. The Main Annotation (`@RestController`)
4. Your First Endpoint (`@GetMapping`)
5. Running the Application
6. Returning Objects
7. Return JSON
8. Returning Lists
9. HTTP Methods
10. Intro to POST Requests
11. Testing APIs
12. Mini Exercise
13. Closing Statement

---

## 1. Start With This Question

> 💬 **Ask:** "How does a frontend or mobile app communicate with a backend?"

✅ **Answer:** Through APIs.

> 🧠 **Say:** "An API is a bridge between systems."

---

## 2. What Is a REST API?

*(Representational State Transfer Application Programming Interface)*

> 🧠 **Say:** "A REST API allows applications to communicate using HTTP requests."

**Real example:**

📱 Frontend asks:
```
GET /students
```

🖥️ Backend responds:
```json
[
  {
    "name": "John"
  }
]
```

> 🧠 **Teaching line:** "REST APIs expose data through URLs."

---

## 3. The Main Annotation

✅ `@RestController`

```java
package org.codex;

import org.springframework.web.bind.annotation.RestController;

@RestController
public class StudentController {
}
```

**What it means:** "This class handles web requests and returns data."

> 🧠 **Say:** "`@RestController` turns a class into an API controller."

---

## 4. Your First Endpoint

✅ `@GetMapping`

```java
package org.codex;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class StudentController {

    @GetMapping("/hello")
    public String hello() {
        return "Hello Student";
    }
}
```

**What's happening?** When a browser visits `http://localhost:8080/hello`, Spring:
1. Receives the request
2. Finds the matching endpoint
3. Runs the method
4. Returns the response

✅ **Output:**
```
Hello Student
```

> 🧠 **Say:** "URLs are mapped to Java methods."

---

## 5. Running the Application

```java
package org.codex;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Main {
    public static void main(String[] args) {
        SpringApplication.run(Main.class, args);
    }
}
```

**What is `localhost:8080`?**

| Part | Meaning |
|---|---|
| `localhost` | Your own computer |
| `8080` | The server port |

> 🧠 **Say:** "Spring Boot starts a web server inside your application."

---

## 6. Returning Objects (Very Important)

```java
package org.codex;

public class Student {
    private String name;
    private int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

---

## 7. Return JSON

```java
package org.codex;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class StudentController {

    @GetMapping("/student")
    public Student getStudent() {
        return new Student("John", 20);
    }
}
```

✅ **Response:**
```json
{
  "name": "John",
  "age": 20
}
```

### Important Magic

You returned a `Student`. The browser got JSON.

> ❓ **Why?**
> 👉 Spring Boot automatically converts Java objects into JSON.

> 🧠 **Say:** "Spring Boot serializes Java objects into JSON automatically."

---

## 8. Returning Lists

```java
@GetMapping("/students")
public List<Student> getStudents() {
    return List.of(
            new Student("John", 20),
            new Student("Mary", 22)
    );
}
```

✅ **Response:**
```json
[
  {
    "name": "John",
    "age": 20
  },
  {
    "name": "Mary",
    "age": 22
  }
]
```

---

## 9. HTTP Methods

| Method | Purpose |
|---|---|
| `GET` | Fetch data |
| `POST` | Create data |
| `PUT` | Update data |
| `DELETE` | Remove data |

> 🧠 **Teaching line:** "REST APIs use HTTP verbs to describe actions."

---

## 10. Intro to POST Requests

✅ `@PostMapping`

```java
@PostMapping("/student")
public String addStudent() {
    return "Student Added";
}
```

> 📌 This is just a first look at the annotation — it doesn't actually receive or save any data yet. Accepting data in the request body (`@RequestBody`) is covered in a later chapter.

---

## 11. Testing APIs

✅ **Browser** — for GET requests only

✅ **Postman** (Important) — used for:
- GET
- POST
- PUT
- DELETE

> 🧠 **Say:** "Postman is like a remote control for APIs."

---

## 12. Mini Exercise

1. Create a `Course` class with `name` and `instructor` fields
2. Create a `CourseController` annotated with `@RestController`
3. Add a `GET /courses` endpoint that returns a list of at least 2 `Course` objects
4. Test it in the browser or Postman

<details>
<summary>💡 Click to reveal a suggested solution</summary>

```java
package org.codex;

public class Course {
    private String name;
    private String instructor;

    public Course(String name, String instructor) {
        this.name = name;
        this.instructor = instructor;
    }

    public String getName() {
        return name;
    }

    public String getInstructor() {
        return instructor;
    }
}
```

```java
package org.codex;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
public class CourseController {

    @GetMapping("/courses")
    public List<Course> getCourses() {
        return List.of(
                new Course("Spring Boot", "Mr. Codex"),
                new Course("Java Fundamentals", "Mrs. Lane")
        );
    }
}
```

✅ **Response:**
```json
[
  {
    "name": "Spring Boot",
    "instructor": "Mr. Codex"
  },
  {
    "name": "Java Fundamentals",
    "instructor": "Mrs. Lane"
  }
]
```

</details>

---

## 13. Closing Statement

## ✅ Key Takeaways

- A REST API lets systems communicate over HTTP, exposing data through URLs
- `@RestController` marks a class as an API controller; `@GetMapping` maps a URL to a Java method
- Returned Java objects (and lists of them) are automatically serialized to JSON — no manual conversion needed
- HTTP verbs describe intent: GET fetches, POST creates, PUT updates, DELETE removes
- Browsers can only test GET requests; Postman can test all of them

---

**← Previous: [Chapter 8 — Spring Boot Introduction](./chapter-8-spring-boot-introduction.md)** | **Next: Chapter 10 →**
