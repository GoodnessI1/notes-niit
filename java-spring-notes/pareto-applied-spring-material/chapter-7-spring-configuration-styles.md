# Chapter 7: Spring Configuration Styles

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. This file is your delivery script: you dictate/describe, students write notes and build the code live as you guide them.

> 🆕 **Revision note (not for dictation):** Sections 3–5 of the original chapter re-taught `@Configuration` and `@Bean` almost identically to Chapter 4 — that's now a fast recap instead of a full re-teach, freeing up time for what's actually new here: the three-configuration-styles framing, the method-interception behavior in Section 6 ("the magic"), and mixing styles. Same project/rig as always, reused from Chapter 3.

So far we've relied on `@Component` and component scanning without asking how Spring actually finds out what to create. This chapter steps back and covers all three ways Spring can be configured — and one behavior of `@Bean` methods that surprises almost everyone the first time they see it.

## 🎯 Lesson Objective

Students should understand — and have run code demonstrating:

- The 3 ways to configure Spring, and why modern Spring prefers annotations + Java config
- Why calling a `@Bean` method from inside another `@Bean` method doesn't create a duplicate object
- How to mix component scanning and manual `@Bean` registration in one project

## 🗺️ Lesson Roadmap

1. Start With This Question
2. The 3 Configuration Styles
3. Recap: `@Configuration` and `@Bean` (fast — already covered in Chapter 4)
4. Full Example — **build-along**
5. The Method-Interception "Magic" — **build-along**
6. Mixing Config Styles — **build-along**
7. When to Use `@Bean` vs `@Component`
8. Mini Exercise — **build-along, independent**
9. Closing Statement

---

## 1. Start With This Question

> 💬 **Ask:** "How does Spring even know what beans to create?"

> 🧠 **Say:** "Spring must be told what objects exist — configuration is how we tell it. You've been doing this with `@ComponentScan` since Chapter 3. Today we look at the full picture."

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

> 🧠 **Say:** "XML was powerful… but painful. You won't write this yourself in this course — it's here so you recognize it if you ever see it in an older codebase."
>
> 📌 **Instructor Note:** No build-along for XML — it's legacy and out of scope for hands-on practice. Mention it, move on.

### 2.2 Annotation-Based (What You've Been Using)

A way of telling Spring how to create and manage objects/dependencies using annotations.

```java
@Component
public class Course {
}
```

✅ **Pros:**
- Simple
- Clean
- Automatic scanning

⚠️ **Limitation:** "What if you don't own the class?" — e.g. a third-party library class, or external config you can't add annotations to.

> 💬 **Say:** "This is exactly the wall you'd hit — you can't put `@Component` on a class you didn't write. That's what the third style solves."

### 2.3 Java Configuration (`@Configuration`) ⭐

This is the modern, powerful approach — and the focus of the rest of this chapter.

---

## 3. Recap: `@Configuration` and `@Bean`

> 📌 **Instructor Note:** This is a recap, not new material — students built this exact rig in Chapter 3 and used `@Bean` directly in Chapter 4. Move quickly; the goal is just to refresh the vocabulary before Section 4's new content.

- **`@Configuration`** marks a class as a place where beans are defined manually.
- **`@Bean`**, placed on a method, tells Spring: "the object this method returns is a bean — manage it."

```java
@Configuration
public class AppConfig {

    @Bean
    public Course course() {
        return new Course();
    }
}
```

> 🧠 **Teaching line:** "`@Bean` turns a method into a bean factory. You've already done this in Chapter 4 — same idea, same syntax."

---

## 4. Full Example (Very Important)

👉 Without `@Component` anywhere this time — every bean is registered manually.

#### 🛠️ Build-Along — Wire two beans by hand, no `@Component` anywhere (chunked)

**Chunk 1 — say:** "Create `Course` and `Student` as plain classes — no annotations on either one. `Course` has a `study()` method; `Student` takes a `Course` via constructor and has a `work()` method that calls `course.study()`."

```java
// Course.java
public class Course {
    public void study() {
        System.out.println("Studying...");
    }
}
```

```java
// Student.java
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

**Chunk 2 — say:** "In `AppConfig`, add two `@Bean` methods: one returning a `Course`, and one returning a `Student` — built by calling `course()` from inside `student()`."

```java
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

**Chunk 3 — say:** "Fetch a `Student` bean from the context and call `work()`. Run it."

✅ **Output:**
```
Studying...
```

> 💬 **Say:** "Every bean here was registered manually, with no `@Component` in sight — and it still works exactly like the automatic version."

---

## 5. The Method-Interception "Magic" (This Blows Minds)

Look closely at this line from the build-along above:

```java
return new Student(course());
```

> ❓ **Question:** "Does calling `course()` here create a *new* `Course` every time?"
> ❌ **No.** Spring intercepts this call and returns the same bean.

> 🧠 **Teaching line:** "Spring controls even method calls inside `@Configuration`."

#### 🛠️ Build-Along — Prove the interception, don't just state it

> 📌 **Instructor Note:** This is the single most surprising fact in the chapter — worth proving directly rather than leaving as an assertion students take on faith.

**Say:** "Add a constructor to `Course` that prints `Course object created`. Add a second `@Bean` method to `AppConfig` — call it `anotherCourse()` — that also returns `course()`. Fetch `Student` from the context and run it. Count how many times `Course object created` prints."

```java
public class Course {
    public Course() {
        System.out.println("Course object created");
    }
    public void study() {
        System.out.println("Studying...");
    }
}
```

```java
@Bean
public Course anotherCourse() {
    return course();
}
```

✅ **Expected output:** `Course object created` prints **once**, not twice — even though `course()` is referenced by two different `@Bean` methods.

> 💬 **Say, once they see it:** "You called `course()` from two different places in the config class, and the constructor only ran once. That's the proof — Spring rewrites how `@Configuration` classes behave under the hood so that calling a `@Bean` method never bypasses the container. This is different from calling a regular method twice, which *would* run the constructor twice — try that comparison with a plain, non-`@Bean` method if anyone doubts it."

---

## 6. Mixing Config Styles (Real World)

```java
@Configuration
@ComponentScan("org.codex")
public class AppConfig {
}
```

👉 Now you get:
- Automatic scanning (`@Component`)
- Manual beans (`@Bean`)

#### 🛠️ Build-Along — Combine both styles in one project (chunked)

**Chunk 1 — say:** "Mark `Student` with `@Component` and give it `@Autowired` on the constructor, instead of registering it manually. Keep `Course` as a plain class with no annotation."

```java
@Component
public class Student {

    private final Course course;

    @Autowired
    public Student(Course course) {
        this.course = course;
    }

    public void work() {
        course.study();
    }
}
```

**Chunk 2 — say:** "In `AppConfig`, keep `@ComponentScan` (so `Student` gets picked up automatically), but keep the `@Bean` method for `Course` (pretend it's a class you don't own and can't annotate)."

```java
@Configuration
@ComponentScan("org.codex")
public class AppConfig {

    @Bean
    public Course course() {
        return new Course();
    }
}
```

**Chunk 3 — say:** "Run it — same output as before, but now half the wiring is automatic and half is manual."

✅ **Output:**
```
Studying...
```

> 💬 **Say:** "Spring doesn't care which style registered a bean — once it's in the container, it can be autowired into anything else, regardless of how it got there."

---

## 7. When to Use `@Bean` vs `@Component`

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

## 8. Mini Exercise

You need to wire in a `Library` class that you're told to treat as **third-party** — you can't add annotations to it. Meanwhile, `Student` is a class you own.

1. Create a `Library` class (no annotations allowed on it) with a `borrowBook()` method
2. Create a `Student` class, annotated with `@Component`, that depends on `Library` via constructor injection
3. Write an `AppConfig` that mixes both styles: `@ComponentScan` to pick up `Student` automatically, and a `@Bean` method to register `Library` manually
4. Run it and confirm Spring wires both together correctly

#### 🛠️ Build-Along — Solve it independently

> 📌 **Instructor Note:** Give the numbered spec as-is and let students work through it — this is a direct rerun of the exact shape of Section 6's build-along with new class names, so it should be routine. Step in only where genuinely needed.

<details>
<summary>💡 Click to reveal a suggested solution</summary>

```java
// Library.java — pretend this is a third-party class you can't modify
public class Library {
    public void borrowBook() {
        System.out.println("Borrowing a book...");
    }
}
```

```java
// Student.java — a class you own
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
@Configuration
@ComponentScan("org.codex")
public class AppConfig {

    @Bean
    public Library library() {
        return new Library();
    }
}
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
- Method calls between `@Bean` methods (e.g. `course()` inside `student()`) don't create new objects — Spring intercepts the call and returns the existing bean, provably (constructor runs once, no matter how many `@Bean` methods call it)

> 🧠 "`@Component` lets Spring create objects — `@Bean` lets you create them."

---

**← Previous: [Chapter 6 — Advanced Dependency Injection](./chapter-6-advanced-dependency-injection.md)** | **Next: Chapter 8 →**

---

## 🔧 What Changed in This Revision

> 🆕 **Not for dictation — for your reference only.**

- Compressed the original Sections 3–5 (`@Configuration`, `@Bean` explained from scratch) into a single fast recap — this content was already taught in Chapter 4 almost verbatim, so re-teaching it here was pure redundancy at odds with the Pareto goal.
- Turned the "Important Magic" section from an assertion into a proof: students now add a constructor print statement and *watch* it fire only once across two `@Bean` methods referencing the same call, instead of just being told Spring intercepts it.
- Formalized the Full Example and Mixing Config Styles sections as build-alongs, consistent with every other chapter.
- XML is explicitly called out as **not** a build-along target — it's legacy/recognition-only content, so time isn't spent having students hand-write it.
