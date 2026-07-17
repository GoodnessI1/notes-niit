# Chapter 19: DTOs & API Design

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

This chapter is arguably more important than Security for junior developers. Most real-world code review feedback for beginners is about exactly this: exposing the wrong data, leaking fields, and tying the API too tightly to the database.

## 🎯 Lesson Goal

Students should understand:

- What DTOs are
- Why DTOs exist
- Request DTOs
- Response DTOs
- Separation between API and database

## 🗺️ Lesson Roadmap

1. Start With the Problem
2. What Is a DTO?
3. Request DTO
4. Response DTO
5. The Full Flow
6. Creating a Student (Request → Entity)
7. Returning a Student (Entity → Response)
8. Service: Where the Conversion Lives
9. Why DTOs Matter
10. Good API Design Principles
11. Visual Flow
12. Mini Exercise
13. Closing Statement

---

## 1. Start With the Problem

```java
@Entity
public class Student {
    private Integer id;
    private String name;
    private Integer age;
    private String email;
    private String password;
}
```

**Controller:**
```java
@GetMapping("/students")
public List<Student> getStudents() {
    return service.getStudents();
}
```

**Response:**
```json
{
  "id": 1,
  "name": "John",
  "age": 20,
  "email": "john@gmail.com",
  "password": "secret123"
}
```

Oops. Password leaked. Why? Because we exposed the entity directly.

> 🧠 **Teaching line:** "Entities describe database data. APIs should expose only what clients need."

---

## 2. What Is a DTO?

**DTO = Data Transfer Object**

A DTO is an object whose sole purpose is moving data between layers. It is **not** an entity — it doesn't map to a database table. It's just a shaped container for data going in or out of the API.

---

## 3. Request DTO

Represents **incoming** data from the client.

```java
package org.codex.dto;

import jakarta.validation.constraints.*;

public class StudentRequest {

    @NotBlank
    @Size(min = 2, max = 50)
    private String name;

    @Min(1)
    @Max(120)
    private Integer age;

    @Email
    private String email;

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

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```

> 📌 This is the same `StudentRequest` established in Chapters 12–14 — already in your project. Notice it has no `id` (the database assigns that) and no `password` (not part of this API's create flow).

---

## 4. Response DTO

Represents **outgoing** data sent back to the client.

```java
package org.codex.dto;

public class StudentResponse {

    private Integer id;
    private String name;
    private String email;

    public StudentResponse() {
    }

    public StudentResponse(Integer id, String name, String email) {
        this.id = id;
        this.name = name;
        this.email = email;
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

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```

Notice: `password` does not exist here. `age` is also omitted — if the API doesn't need to return it, it doesn't go in the response shape.

---

## 5. The Full Flow

Instead of:

```
Controller → Entity → Client
```

Use:

```
Client → StudentRequest → Service → Entity → Database
Database → Entity → Service → StudentResponse → Client
```

The entity stays inside the service and repository layers. The client never sees it directly.

---

## 6. Creating a Student (Request → Entity)

**Request from client:**
```json
{
  "name": "John",
  "age": 20,
  "email": "john@gmail.com"
}
```

Spring maps this to `StudentRequest`. The service then converts it to a `Student` entity:

```java
private Student toEntity(StudentRequest request) {
    Student student = new Student();
    student.setName(request.getName());
    student.setAge(request.getAge());
    student.setEmail(request.getEmail());
    return student;
}
```

---

## 7. Returning a Student (Entity → Response)

After saving or fetching, the service converts the entity to a response DTO:

```java
private StudentResponse toResponse(Student student) {
    return new StudentResponse(
            student.getId(),
            student.getName(),
            student.getEmail()
    );
}
```

The controller returns `StudentResponse`, not `Student`.

---

## 8. Service: Where the Conversion Lives

```java
@Service
public class StudentService {

    private final StudentRepository repository;

    public StudentService(StudentRepository repository) {
        this.repository = repository;
    }

    public StudentResponse createStudent(StudentRequest request) {
        Student student = toEntity(request);
        Student saved = repository.save(student);
        return toResponse(saved);
    }

    public List<StudentResponse> getStudents() {
        return repository.findAll()
                .stream()
                .map(this::toResponse)
                .toList();
    }

    public StudentResponse getStudent(Integer id) {
        Student student = repository.findById(id)
                .orElseThrow(() -> new StudentNotFoundException("Student not found"));
        return toResponse(student);
    }

    private Student toEntity(StudentRequest request) {
        Student student = new Student();
        student.setName(request.getName());
        student.setAge(request.getAge());
        student.setEmail(request.getEmail());
        return student;
    }

    private StudentResponse toResponse(Student student) {
        return new StudentResponse(
                student.getId(),
                student.getName(),
                student.getEmail()
        );
    }
}
```

> 📌 `toEntity()` and `toResponse()` are private helper methods — they live in the service and are never exposed outside it. This is where the conversion responsibility belongs.

> 📌 **Heads up for later:** As projects grow, these manual mapping methods can become tedious. Libraries like **MapStruct** automate this entirely — you define an interface, and it generates the mapping code at compile time. Worth knowing it exists, even if you don't need it yet.

**Update the controller** to match:

```java
@PostMapping("/students")
public StudentResponse createStudent(@Valid @RequestBody StudentRequest request) {
    return service.createStudent(request);
}

@GetMapping("/students")
public List<StudentResponse> getStudents() {
    return service.getStudents();
}

@GetMapping("/students/{id}")
public StudentResponse getStudent(@PathVariable Integer id) {
    return service.getStudent(id);
}
```

---

## 9. Why DTOs Matter

| Reason | Explanation |
|---|---|
| **Security** | Hide sensitive fields — passwords, tokens, internal flags |
| **Validation** | Validate request objects independently of the entity |
| **Flexibility** | Database schema can change without breaking the API contract |
| **Separation of concerns** | Database and API become independent of each other |

> 🧠 **Teaching line:** "DTOs protect the API from database changes."

---

## 10. Good API Design Principles

### 1. Use Nouns for URLs

✅ Good:
```
/students
/books
/users
```

❌ Bad:
```
/getStudents
/createStudent
```

HTTP methods (GET, POST, PUT, DELETE) already describe the action — the URL should only describe the resource.

### 2. Use HTTP Methods Correctly

| Method | Action |
|---|---|
| `GET` | Fetch |
| `POST` | Create |
| `PUT` | Update |
| `DELETE` | Remove |

### 3. Use Meaningful Responses

✅ Good:
```json
{
  "id": 1,
  "name": "John"
}
```

❌ Vague:
```json
{
  "data": "ok"
}
```

### 4. Don't Leak Sensitive Internal Details

Never expose in a response:
- Passwords
- Tokens
- Security flags

> 📌 `id` is fine to include — clients need it to make follow-up requests like `PUT /students/1` or `DELETE /students/1`. What you're hiding is *sensitive* data, not all internal data.

---

## 11. Visual Flow

```
Client
    ↓
StudentRequest DTO
    ↓
Validation (@Valid)
    ↓
Service (toEntity)
    ↓
Entity
    ↓
Database
    ↓
Entity
    ↓
Service (toResponse)
    ↓
StudentResponse DTO
    ↓
Client
```

---

## 12. Mini Exercise

Current `getStudents()` in the controller returns `List<Student>`.

Refactor the full stack so it returns `List<StudentResponse>` instead:
1. Add `toResponse()` to `StudentService`
2. Update `getStudents()` in the service to convert each entity
3. Update the controller return type
4. Test with Postman — confirm `password` no longer appears in the response

<details>
<summary>💡 Click to reveal a suggested solution</summary>

**Service:**
```java
public List<StudentResponse> getStudents() {
    return repository.findAll()
            .stream()
            .map(this::toResponse)
            .toList();
}

private StudentResponse toResponse(Student student) {
    return new StudentResponse(
            student.getId(),
            student.getName(),
            student.getEmail()
    );
}
```

**Controller:**
```java
@GetMapping("/students")
public List<StudentResponse> getStudents() {
    return service.getStudents();
}
```

✅ Response before:
```json
{ "id": 1, "name": "John", "age": 20, "email": "john@gmail.com", "password": "secret123" }
```

✅ Response after:
```json
{ "id": 1, "name": "John", "email": "john@gmail.com" }
```

</details>

---

## ✅ Key Takeaways

- Never return entities directly from controllers — they expose database structure and sensitive fields
- A **Request DTO** shapes incoming data; a **Response DTO** shapes outgoing data
- Conversion between DTOs and entities belongs in the service layer, in private helper methods
- DTOs protect the API from database changes — the client sees a stable shape even if the entity evolves
- `id` is fine to include in responses; what you're hiding is sensitive data like passwords and tokens
- Manual mapping works fine for small projects; MapStruct automates it at scale

---

**← Previous: [Chapter 18 — Configuration & Profiles](./chapter-18-configuration-and-profiles.md)**
