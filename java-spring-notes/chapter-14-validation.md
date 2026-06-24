# Chapter 14: Validation

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

So far, nothing has stopped a client from sending garbage data — an empty name, a negative age, a malformed email — straight into the database. This chapter fixes that with Bean Validation.

## 🎯 Lesson Goal

Students should understand:

- Why validation is necessary
- How to validate incoming requests
- Bean Validation annotations
- `@Valid`
- How validation prevents bad data

## 🧰 Quick Setup

> 📌 Bean Validation annotations (`@NotBlank`, `@Email`, `@Min`, etc.) and `@Valid` need the `spring-boot-starter-validation` dependency — it's **not** included with `spring-boot-starter-web` by default. Add it to `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

## 🗺️ Lesson Roadmap

1. Start With the Problem
2. What Is Validation?
3. The Student Entity (Recap)
4. Bean Validation Annotations
5. Building a Validated DTO
6. Why Use a DTO?
7. The Magic Annotation: `@Valid`
8. What Does the Error Response Look Like?
9. Why Validation Matters
10. Common Validation Annotations
11. Common Student Mistakes
12. Mini Exercise
13. Closing Statement

---

## 1. Start With the Problem

Suppose your API allows this:

```json
{
  "name": "",
  "age": -50
}
```

or this:

```json
{
  "name": null,
  "age": 999
}
```

Without validation:

```java
repository.save(student);
```

Spring happily saves garbage data.

> 🧠 **Teaching line:** "Validation protects your application from bad input."

---

## 2. What Is Validation?

**Definition:** Validation is the process of checking whether incoming data satisfies predefined rules before it is processed or stored.

**Real-world analogy:** Think about a university admission form. Rules might be:
- Name cannot be empty
- Age must be positive
- Email must be valid

If the form violates the rules: ❌ rejected. Spring validation works the same way.

---

## 3. The Student Entity (Recap)

> 📌 Simplified here to just the fields that matter for this lesson — the real entity (Chapters 11–13) also has `id` and `@GeneratedValue`.

```java
public class Student {
    private String name;
    private Integer age;
}
```

Currently, this is accepted with no complaints:

```json
{
  "name": "",
  "age": -5
}
```

Let's fix that.

---

## 4. Bean Validation Annotations

These annotations define rules.

### `@NotNull`

```java
@NotNull
private String name;
```

Means: this field cannot be null.

✅ Valid: `{ "name": "John" }`
❌ Invalid: `{ "name": null }`

### `@NotBlank`

```java
@NotBlank
private String name;
```

Means: cannot be null, empty, or only spaces.

❌ Invalid: `{ "name": "" }`
❌ Invalid: `{ "name": "     " }`

> 🧠 **Teaching line:** "`@NotNull` checks existence. `@NotBlank` checks usefulness."

### `@Size`

```java
@Size(min = 2, max = 50)
private String name;
```

✅ Valid: `John`
❌ Invalid: `J`

### `@Min`

```java
@Min(1)
private Integer age;
```

Means: `age >= 1`.

❌ Invalid: `{ "age": -5 }`

### `@Max`

```java
@Max(120)
private Integer age;
```

Means: `age <= 120`.

### `@Email`

```java
@Email
private String email;
```

✅ Valid: `john@gmail.com`
❌ Invalid: `john.com`

---

## 5. Building a Validated DTO

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

> 📌 This refines the `StudentRequest` from Chapters 12–13, adding an `email` field. The `Student` entity gains a matching field too, so this is wired all the way through to persistence:
>
> ```java
> // Student.java — add alongside the existing id, name, age fields
> private String email;
>
> public String getEmail() {
>     return email;
> }
>
> public void setEmail(String email) {
>     this.email = email;
> }
> ```
>
> And in `StudentService`, both `createStudent()` and `updateStudent()` now also call `student.setEmail(request.getEmail())` when building the entity.

---

## 6. Why Use a DTO?

Instead of validating the entity directly, we validate the incoming request shape.

> 🧠 **Teaching line:** "DTOs represent incoming request data."

*(We'll discuss DTOs vs entities more later.)*

---

## 7. The Magic Annotation: `@Valid`

This is where students usually get confused.

```java
@PostMapping("/students")
public Student createStudent(@RequestBody StudentRequest request) {
    return service.create(request);
}
```

**Will validation happen?** ❌ No.

We need:

```java
@PostMapping("/students")
public Student createStudent(@Valid @RequestBody StudentRequest request) {
    return service.create(request);
}
```

Now Spring checks `@NotBlank`, `@Email`, `@Min`, etc. **before** the method body even runs.

> 🧠 **Teaching line:** "`@Valid` tells Spring to enforce the validation rules."

**Example request:**
```json
{
  "name": "",
  "age": -5,
  "email": "wrong-email"
}
```

Spring rejects it automatically — the controller method never runs.

**Visual flow:**

```
Request
   ↓
Validation
   ↓
 Valid?
 /    \
Yes    No
 ↓      ↓
Controller   Error Response
```

---

## 8. What Does the Error Response Look Like?

For the rejected request above, Spring returns a `400 Bad Request` automatically, with a body describing what failed:

```json
{
  "status": 400,
  "errors": [
    "name: must not be blank",
    "age: must be greater than or equal to 1",
    "email: must be a well-formed email address"
  ]
}
```

> 📌 The exact shape of this response varies depending on Spring Boot version and whether you've customized error handling — the important part is that it's a `400`, and the controller method body never executes.

---

## 9. Why Validation Matters

Imagine, without validation:

```json
{
  "name": "",
  "age": -500
}
```

...saved straight into the database. Now reports, analytics, and business logic all become unreliable.

> 🧠 **Teaching line:** "Bad data is harder to fix than bad code."

---

## 10. Common Validation Annotations

| Annotation | Purpose |
|---|---|
| `@NotNull` | Must exist |
| `@NotBlank` | Must contain text |
| `@Size` | Length limits |
| `@Min` | Minimum value |
| `@Max` | Maximum value |
| `@Email` | Valid email format |

---

## 11. Common Student Mistakes

❌ **Mistake 1 — Using `@NotNull` when they really need `@NotBlank`**
An empty string `""` passes `@NotNull` — it's not null, just useless.

❌ **Mistake 2 — Forgetting `@Valid`**
Without it, all the validation annotations on the DTO are completely ignored.

❌ **Mistake 3 — Thinking validation happens in the database**
Validation occurs *before* data ever reaches the business logic — the database never even sees invalid data.

---

## 12. Mini Exercise

Create a `BookRequest` DTO with rules:
- Title required
- Author required
- Pages: minimum 1, maximum 5000

Then build `POST /books` using `@Valid`.

<details>
<summary>💡 Click to reveal a suggested solution</summary>

```java
package org.codex.dto;

import jakarta.validation.constraints.*;

public class BookRequest {

    @NotBlank
    private String title;

    @NotBlank
    private String author;

    @Min(1)
    @Max(5000)
    private Integer pages;

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

    public Integer getPages() {
        return pages;
    }

    public void setPages(Integer pages) {
        this.pages = pages;
    }
}
```

```java
@PostMapping("/books")
public Book createBook(@Valid @RequestBody BookRequest book) {
    return service.createBook(book);
}
```

A request with `{ "title": "", "author": "Joshua Bloch", "pages": 10000 }` gets rejected with a `400` before `createBook()` ever runs — both the blank title and the out-of-range page count fail validation.

</details>

---

## 13. Closing Statement

## ✅ Key Takeaways

- Validation checks incoming data against rules *before* it's processed or stored
- `@NotNull`, `@NotBlank`, `@Size`, `@Min`, `@Max`, and `@Email` define those rules on a DTO's fields
- `@Valid` is what actually activates the checks — without it, the annotations do nothing
- Failed validation returns a `400 Bad Request` automatically; the controller method body never runs
- Validating a DTO instead of the entity keeps request-shape rules separate from your persistence model

---

**← Previous: [Chapter 13 — CRUD Operations with Spring Boot](./chapter-13-crud-operations.md)**
