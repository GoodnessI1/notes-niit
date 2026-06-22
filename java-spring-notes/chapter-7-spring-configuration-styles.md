# Chapter 7: Spring Configuration Styles

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

So far we've relied on `@Component` and component scanning without asking how Spring actually finds out what to create. This chapter steps back and covers all three ways Spring can be configured — and which one is the modern standard.

## 🎯 Lesson Objective

Students should understand:

- The 3 ways to configure Spring
- When to use each
- Why modern Spring prefers annotations + Java config
- What `@Configuration` and `@Bean` really do

## 🗺️ Lesson Roadmap

1. Start With This Question
2. The 3 Configuration Styles
3. What is `@Configuration`?
4. Basic Example
5. Using It in Main
6. Full Example
7. Mixing Config Styles
8. When to Use `@Bean` vs `@Component`
9. Mini Exercise
10. Closing Statement

---

## 1. Start With This Question

> 💬 **Ask:** "How does Spring even know what beans to create?"

> 🧠 **Say:** "Spring must be told what objects exist — configuration is how we tell it."

---

## 2. The 3 Configuration Styles

### 2.1 XML Configuration (Old Way)

```xml
<beans>
    <bean id="course" class="org.codex.Course"/>
</beans>
```

❌ **Problems:**
- Verbose
- Hard to maintain
- Not type-safe

> 🧠 **Say:** "XML was powerful… but painful."

### 2.2 Annotation-Based (What You've Been Using)

A way of telling Spring how to create and manage objects/dependencies using annotations.

```java
@Component
public class Course {
}
```

👉 Bootstrapped with:

```java
new AnnotationConfigApplicationContext("org.codex");
```

✅ **Pros:**
- Simple
- Clean
- Automatic scanning

⚠️ **Limitation:** "What if you don't own the class?" — e.g. a third-party library class, or external config you can't add annotations to.

### 2.3 Java Configuration (`@Configuration`) ⭐

This is the modern, powerful approach — and the focus of the rest of this chapter.

---

## 3. What is `@Configuration`?

> 🧠 **Say:** "A class that tells Spring how to create beans manually."

---

## 4. Basic Example

```java
package org.codex;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public Course course() {
        return new Course();
    }
}
```

**What's happening here:**
- `@Configuration` marks this class as config
- `@Bean` tells Spring: "the object returned by this method is a bean"

> 🧠 **Teaching line:** "`@Bean` turns a method into a bean factory."

---

## 5. Using It in Main

```java
package org.codex;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Main {
    public static void main(String[] args) {

        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        Course course = context.getBean(Course.class);
        course.study();
    }
}
```

---

## 6. Full Example (Very Important)

👉 Without `@Component` anywhere this time — every bean is registered manually.

```java
// Course.java
package org.codex;

public class Course {
    public void study() {
        System.out.println("Studying...");
    }
}
```

```java
// Student.java
package org.codex;

public class Student {
    private Course course;

    public Student(Course course) {
        this.course = course;
    }

    public void work() {
        course.study();
    }
}
```

```java
// AppConfig.java
package org.codex;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public Course course() {
        return new Course();
    }

    @Bean
    public Student student() {
        return new Student(course());
    }
}
```

```java
// Main.java
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

Student student = context.getBean(Student.class);
student.work();
```

✅ **Output:**
```
Studying...
```

### Important Magic (This Blows Minds)

```java
return new Student(course());
```

> ❓ **Question:** "Does this create a new `Course` every time?"
> ❌ **No.** Spring intercepts this call and returns the same bean.

> 🧠 **Teaching line:** "Spring controls even method calls inside `@Configuration`."

---

## 7. Mixing Config Styles (Real World)

```java
@Configuration
@ComponentScan("org.codex")
public class AppConfig {
}
```

👉 Now you get:
- Automatic scanning (`@Component`)
- Manual beans (`@Bean`)

---

## 8. When to Use `@Bean` vs `@Component`

✅ **Use `@Component` when:**
- You own the class
- It's a simple object

✅ **Use `@Bean` when:**
- It's a third-party class
- Creation logic is complex
- You need full control

> 🧠 **Teaching line:** "Use `@Component` for simplicity, `@Bean` for control."

### Common Student Confusion

> ❓ "Why not always use `@Component`?"

Because:

```java
public class ExternalLibraryClass {
}
```

You **cannot** do:

```java
@Component // ❌ not your code
```

So you use:

```java
@Bean
public ExternalLibraryClass obj() {
    return new ExternalLibraryClass();
}
```

### Classroom Analogy

- **`@Component`** — "Spring finds and creates the object automatically"
- **`@Bean`** — "You build the object yourself and give it to Spring"

### Comparison

| Feature | `@Component` | `@Bean` |
|---|---|---|
| Who creates it | Spring | You |
| Flexibility | Low | High |
| Use case | Simple classes | Complex/external classes |

---

## 9. Mini Exercise

You need to wire in a `Library` class that you're told to treat as **third-party** — you can't add annotations to it. Meanwhile, `Student` is a class you own.

1. Create a `Library` class (no annotations allowed on it) with a `borrowBook()` method
2. Create a `Student` class, annotated with `@Component`, that depends on `Library` via constructor injection
3. Write an `AppConfig` that mixes both styles: `@ComponentScan` to pick up `Student` automatically, and a `@Bean` method to register `Library` manually
4. Run it and confirm Spring wires both together correctly

<details>
<summary>💡 Click to reveal a suggested solution</summary>

```java
// Library.java — pretend this is a third-party class you can't modify
package org.codex;

public class Library {
    public void borrowBook() {
        System.out.println("Borrowing a book...");
    }
}
```

```java
// Student.java — a class you own
package org.codex;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class Student {

    private final Library library;

    @Autowired
    public Student(Library library) {
        this.library = library;
    }

    public void visitLibrary() {
        library.borrowBook();
    }
}
```

```java
// AppConfig.java — mixes both styles
package org.codex;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan("org.codex")
public class AppConfig {

    @Bean
    public Library library() {
        return new Library();
    }
}
```

```java
// Main.java
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

Student student = context.getBean(Student.class);
student.visitLibrary();
```

✅ **Output:** `Borrowing a book...`

`Student` is discovered via `@ComponentScan` (you own it); `Library` is registered manually via `@Bean` (pretend you don't own it). Spring wires them together regardless of which style created each one.

</details>

---

## ✅ Key Takeaways

- **XML** — the old way: powerful but verbose, not type-safe, generally avoided now
- **Annotations (`@Component`)** — simple and clean, but only works for classes you own
- **Java Config (`@Configuration` + `@Bean`)** — powerful and flexible, the right tool for third-party or complex creation logic
- The two styles can be mixed freely in one `@Configuration` class via `@ComponentScan`
- Method calls between `@Bean` methods (e.g. `course()` inside `student()`) don't create new objects — Spring intercepts the call and returns the existing bean

> 🧠 "`@Component` lets Spring create objects — `@Bean` lets you create them."

---

**← Previous: [Chapter 6 — Advanced Dependency Injection](./chapter-6-advanced-dependency-injection.md)** | **Next: Chapter 8 →**
