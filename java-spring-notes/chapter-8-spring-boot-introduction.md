# Chapter 8: Spring Boot Introduction

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

Throughout Chapters 3–7, every `@Component`/`@Autowired` example came with a "conceptual preview" disclaimer, because we hadn't yet covered how to actually bootstrap a real, runnable Spring project. This chapter is that missing piece: **Spring Boot**.

## 🎯 Lesson Objective

Students should understand:

- The pain points of Spring before Spring Boot existed
- What Spring Boot actually is and does
- What `@SpringBootApplication` really combines
- How to build and run a first real Spring Boot app
- How to use `CommandLineRunner` to execute logic at startup
- The project structure rule that makes component scanning work automatically

## 🧰 Quick Setup

> 📌 This chapter assumes a project generated from [Spring Initializr](https://start.spring.io) (Maven, Java, latest Spring Boot 3.x, no extra dependencies needed for these examples). That gives you all the `spring-boot-starter` dependencies referenced below. We'll walk through generating a project together in class.

## 🗺️ Lesson Roadmap

1. Start With the Pain
2. What Is Spring Boot?
3. What Does Spring Boot Actually Do?
4. The Magic Annotation (`@SpringBootApplication`)
5. Your First Spring Boot App
6. Add a Component
7. Use `CommandLineRunner`
8. Project Structure
9. Why Spring Boot Is Powerful
10. Mini Exercise
11. Closing Statement

---

## 1. Start With the Pain

> 💬 **Ask:** "If Spring is so powerful… why do people complain about it?"

😤 **The old Spring problems, before Spring Boot:**
- Too much configuration
- XML everywhere
- Manual setup for *everything*
- Hard to even start a project

---

## 2. What Is Spring Boot?

> 🧠 **Say:** "Spring Boot is a tool that makes Spring faster and easier to use."
>
> "Spring Boot doesn't replace Spring — it removes the pain of using it."

---

## 3. What Does Spring Boot Actually Do?

✅ **1. Auto Configuration** — Spring decides things for you automatically
✅ **2. Embedded Server** — no need for an external server like Tomcat
✅ **3. Starter Dependencies** — no need to manually hunt down compatible libraries

> 🧠 **Say:** "Spring Boot makes smart decisions so you don't have to."

---

## 4. The Magic Annotation

```java
@SpringBootApplication
```

**What it really means** — it combines:
- `@Configuration`
- `@ComponentScan`
- `@EnableAutoConfiguration`

👉 It combines everything you've learned so far.

> 🧠 **Say:** "`@SpringBootApplication` is like turning on 'Spring Super Mode.'"

---

## 5. Your First Spring Boot App

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

**What's happening here?**

```java
SpringApplication.run(Main.class, args);
```

👉 This single line:
- Starts the Spring container
- Scans components
- Applies auto-configuration
- Starts the server (if it's a web app)

---

## 6. Add a Component

```java
package org.codex;

import org.springframework.stereotype.Component;

@Component
public class Course {
    public void study() {
        System.out.println("Studying...");
    }
}
```

---

## 7. Use `CommandLineRunner` (Very Important)

👉 This is how you run logic at startup.

```java
package org.codex;

import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class Runner implements CommandLineRunner {

    private final Course course;

    public Runner(Course course) {
        this.course = course;
    }

    @Override
    public void run(String... args) {
        course.study();
    }
}
```

✅ **Output:**
```
Studying...
```

> 🧠 **Say:** "`CommandLineRunner` lets you run code when the app starts."

### Why We Don't Use `main` Like Before

**Before:**
```java
context.getBean(Student.class).work();
```

**Now:** Spring Boot runs everything automatically.

> 🧠 **Say:** "In Spring Boot, you don't pull beans — Spring pushes execution."

---

## 8. Project Structure (Important)

```
org.codex
 ├── Main.java
 ├── Course.java
 └── Runner.java
```

⚠️ **Rule:** the main class should sit in the **root package**, so component scanning works automatically.

> 🧠 **Say:** "Spring scans downward from the main class."

---

## 9. Why Spring Boot Is Powerful

| | Without Boot | With Boot |
|---|---|---|
| Configuration | Manual | Zero config (mostly) |
| Server | Manual setup | Built-in |
| Dependency wiring | Manual | Fast startup, mostly automatic |

### Real-World Analogy

> 🎯 "Spring is cooking from scratch. Spring Boot is using a ready-made kitchen with ingredients prepared."

### Common Student Confusions

> ❓ "Where is `ApplicationContext`?"
> 👉 It still exists — Boot just hides it.

> ❓ "Where is `@ComponentScan`?"
> 👉 Inside `@SpringBootApplication`.

> ❓ "Where is `@Configuration`?"
> 👉 Also included.

---

## 10. Mini Exercise

1. Add a new `@Component` called `Library` with an `openLibrary()` method
2. Inject **both** `Course` and `Library` into `Runner` via constructor injection
3. Call both methods inside `run()`
4. Run the app and check the output order

<details>
<summary>💡 Click to reveal a suggested solution</summary>

```java
package org.codex;

import org.springframework.stereotype.Component;

@Component
public class Library {
    public void openLibrary() {
        System.out.println("Library is open.");
    }
}
```

```java
package org.codex;

import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class Runner implements CommandLineRunner {

    private final Course course;
    private final Library library;

    public Runner(Course course, Library library) {
        this.course = course;
        this.library = library;
    }

    @Override
    public void run(String... args) {
        library.openLibrary();
        course.study();
    }
}
```

✅ **Output:**
```
Library is open.
Studying...
```

Since both `Course` and `Library` are `@Component`-annotated and live in the scanned package, Spring just adds the second constructor parameter and resolves it automatically — no extra configuration needed.

</details>

---

## 11. Closing Statement

## ✅ Key Takeaways

- Spring Boot doesn't replace Spring — it removes the manual setup pain
- `@SpringBootApplication` = `@Configuration` + `@ComponentScan` + `@EnableAutoConfiguration`, all in one
- `SpringApplication.run(...)` starts the container, scans components, applies auto-configuration, and starts the server if needed
- `CommandLineRunner` is how you run logic right after the app starts — no manually pulling beans from a context
- The main class must sit in the root package for component scanning to find everything below it

> 🧠 "Spring Boot turns complex setup into simple startup."

---

**← Previous: [Chapter 7 — Spring Configuration Styles](./chapter-7-spring-configuration-styles.md)** | **Next: Chapter 9 →**
