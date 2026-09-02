# Chapter 8: Spring Boot Introduction

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. This file is your delivery script: you dictate/describe, students write notes and build the code live as you guide them.

> 🆕 **Revision note (not for dictation):** Unlike Chapter 7, this chapter's "Quick Setup" is genuinely new content — Spring Boot replaces the manual `AppConfig` rig from Chapter 3, it doesn't duplicate it. The build-alongs below lean hard into that comparison: students should feel `@SpringBootApplication` collapse five chapters of manual work, not encounter it as an unrelated new thing.

Throughout Chapters 3–7, every `@Component`/`@Autowired` example came with a "conceptual preview" disclaimer, because we hadn't yet covered how to actually bootstrap a real, runnable Spring project. This chapter is that missing piece: **Spring Boot**.

## 🎯 Lesson Objective

Students should understand — and have run code demonstrating:

- The pain points of Spring before Spring Boot existed
- What Spring Boot actually is and does
- What `@SpringBootApplication` really combines — and that it's the same three things built by hand since Chapter 3
- How to build and run a first real Spring Boot app
- How to use `CommandLineRunner` to execute logic at startup
- The project structure rule that makes component scanning work automatically

## 🧰 Quick Setup — Build-Along (chunked)

> 📌 This chapter starts a **new** project, generated from [Spring Initializr](https://start.spring.io) (Maven, Java, latest Spring Boot 3.x, no extra dependencies needed for these examples). This is a real new setup, not a repeat of Chapter 3's rig — walk through it live.

**Chunk 1 — say:** "Go to start.spring.io. Select Maven, Java, and the latest stable Spring Boot 3.x version. Set the package name to something like `org.codex`. Leave dependencies empty for now — we don't need any yet. Generate and open the project in your editor."

**Chunk 2 — say:** "Find the generated main class — it already has `@SpringBootApplication` and a `main` method with `SpringApplication.run(...)` in it. Don't delete or rewrite it — that's your starting point for the rest of this chapter."

---

## 🗺️ Lesson Roadmap

1. Start With the Pain
2. What Is Spring Boot?
3. What Does Spring Boot Actually Do?
4. The Magic Annotation (`@SpringBootApplication`) — **compared directly to what you built by hand**
5. Your First Spring Boot App — **build-along**
6. Add a Component — **build-along**
7. Use `CommandLineRunner` — **build-along**
8. Project Structure — **build-along (break it on purpose)**
9. Why Spring Boot Is Powerful
10. Mini Exercise — **build-along, independent**
11. Closing Statement

---

## 1. Start With the Pain

> 💬 **Ask:** "If Spring is so powerful… why do people complain about it?"

😤 **The old Spring problems, before Spring Boot:**
- Too much configuration
- XML everywhere
- Manual setup for *everything*

> 💬 **Say:** "You've felt a small version of this yourself — think about how many times you've retyped `AnnotationConfigApplicationContext` and an `AppConfig` class since Chapter 3. Imagine that at the scale of a real production application."

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

> 💬 **Say, and make this land:** "Look at the first two items on that list — `@Configuration` and `@ComponentScan`. That's not a coincidence. That's the *exact* `AppConfig` class you hand-wrote in Chapter 3, and have been reusing every chapter since. One annotation just replaced a class you've typed out five separate times."

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

#### 🛠️ Build-Along — Run it, empty as it is

**Say:** "Run `Main` exactly as generated, with nothing added. Watch the console."

✅ **Expected result:** a startup log ending in a line like `Started Main in X.XXX seconds` — then the app exits (there's nothing keeping it running yet, since we picked no web dependency).

> 💬 **Say:** "That log output is proof the container started, scanned, and configured itself — all from one annotation. You just haven't given it anything to *do* yet. That's next."

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

#### 🛠️ Build-Along — Add it (one step, should be routine by now)

**Say:** "Create `Course` exactly as shown, in the same package as `Main`."

> 💬 **Say:** "Nothing to run yet — a bean sitting unused doesn't print anything. Next section is where it gets used."

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

**Before (Chapters 3–7):**
```java
context.getBean(Student.class).work();
```

**Now:** Spring Boot runs everything automatically.

> 🧠 **Say:** "In Spring Boot, you don't pull beans — Spring pushes execution."

#### 🛠️ Build-Along — Build `Runner`, see the shift from pull to push

**Say:** "Create `Runner` exactly as shown — `@Component`, `implements CommandLineRunner`, constructor injection for `Course`, and `course.study()` inside `run()`. Run `Main` again."

✅ **Expected output:**
```
Studying...
```

> 💬 **Say, and contrast directly:** "In Chapter 3, you had to write `context.getBean(Course.class)` yourself, in a `main` method, and call the method manually. Here, you never touched `Main` at all after the initial setup — you just described what should happen at startup, and Spring made it happen. That's the 'push' model."

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

#### 🛠️ Build-Along — Break the rule on purpose

> 📌 **Instructor Note:** Same pattern as the `@ComponentScan`-removal build-along in Chapter 4 — students should see the failure before the rule feels real.

**Say:** "Create a subpackage, e.g. `org.codex.other`, and move `Main` into it — leave `Course` and `Runner` in `org.codex`. Run `Main` from its new location."

✅ **Expected result:** either startup fails to find `Runner`/`Course` as beans, or `Runner`'s `course.study()` line never executes — because Spring only scans *downward* from wherever `Main` sits, and `org.codex` is no longer below `org.codex.other`.

> 💬 **Say, once they see it break:** "Move `Main` back to the root package and run it again to confirm it's fixed. This is the exact same underlying rule as `@ComponentScan` in Chapter 4 — Spring can only find what's inside the area it's told to scan. Spring Boot just picks that starting point for you automatically, based on where `Main` lives."

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

#### 🛠️ Build-Along — Solve it independently

> 📌 **Instructor Note:** Give the spec as-is. This should be routine at this point in the course — step in only where genuinely stuck.

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
- `@SpringBootApplication` = `@Configuration` + `@ComponentScan` + `@EnableAutoConfiguration`, all in one — literally the `AppConfig` class from Chapter 3, collapsed
- `SpringApplication.run(...)` starts the container, scans components, applies auto-configuration, and starts the server if needed
- `CommandLineRunner` is how you run logic right after the app starts — no manually pulling beans from a context; execution is pushed, not pulled
- The main class must sit in the root package for component scanning to find everything below it — the same underlying rule as `@ComponentScan`, just auto-located

> 🧠 "Spring Boot turns complex setup into simple startup."

---

**← Previous: [Chapter 7 — Spring Configuration Styles](./chapter-7-spring-configuration-styles.md)** | **Next: Chapter 9 →**

---

## 🔧 What Changed in This Revision

> 🆕 **Not for dictation — for your reference only.**

- This chapter's setup is legitimately new (a different bootstrapping tool), unlike Chapter 7's — so nothing was trimmed here. Instead, direct callbacks were added throughout tying `@SpringBootApplication` back to the manual `AppConfig` rig from Chapter 3, so the "this replaces five chapters of manual work" claim is felt, not just stated.
- Added a build-along for the empty first run, so students see real Spring startup log output before anything else happens — proof the container is real before it's asked to do anything.
- Added a deliberate project-structure break (move `Main` into a subpackage, watch scanning fail, move it back) — same "cause the error on purpose" pattern used successfully in Chapters 4 and 6, applied to a rule that's otherwise easy to forget because it's rarely violated by accident until a real project's package structure grows.
