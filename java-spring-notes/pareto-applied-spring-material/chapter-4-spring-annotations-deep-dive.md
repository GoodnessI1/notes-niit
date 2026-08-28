# Chapter 4: Spring Annotations Deep Dive

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. This file is your delivery script: you dictate/describe, students write notes and build the code live as you guide them.

> 🆕 **Revision note (not for dictation):** The old "Quick Setup" section has been removed — students already built the `AppConfig` + `MainApp` rig in Chapter 3, and reuse it here without rebuilding it. Also, scaffolding starts lightly chunking now (2 small steps per instruction instead of 1) — students should be getting fluent with the vocabulary at this point.

In Chapter 3, we learned that the Spring Container creates and manages beans — and you built a real, running container yourself. This chapter goes one level deeper: *how exactly* do we tell the container which classes to manage, and how dependencies get wired? That comes down to four annotations: `@Component`, `@Autowired`, `@Configuration`, and `@Bean`.

## 🎯 Lesson Goal

Students should understand — and have run code demonstrating:

- How Spring automatically creates and manages beans
- How we inject dependencies using annotations
- The difference between manual configuration and annotation-based configuration

> 📌 **Instructor Note:** Everything in this chapter runs in the **same project** students set up in Chapter 3 — same `AppConfig`, same Maven dependency, same `MainApp` pattern. If any student's rig from last chapter is broken or missing, fix that first before starting this chapter; nothing here works without it.

## 🗺️ Lesson Roadmap

1. The Big Shift
2. `@Component` — create this object for me
3. `@Autowired` — give me the dependency
4. `@Configuration` — this class defines beans
5. `@Bean` — create this bean manually — **build-along**
6. Putting it all together — **build-along**
7. The core idea
8. Very common student errors — **build-along (see the errors happen)**

---

## 1. The Big Shift (Very Important)

Before annotations, we did this manually:

```java
Student student = new Student(new Course());
```

> 👉 **Say:** "We stop creating objects ourselves… and let Spring handle it — you already proved this works in Chapter 3 with `Car` and `Engine`."
>
> "Annotations are how we talk to Spring and tell it what to manage."

---

## 2. `@Component` — "Create This Object for Me"

Tells Spring: "This class is a bean. Manage it."

```java
import org.springframework.stereotype.Component;

@Component
public class Course {
    public void study() {
        System.out.println("Studying...");
    }
}
```

**What happens internally:**
- Spring scans the class
- Sees `@Component`
- Creates an object (bean)
- Stores it in the container

> 💬 **Say:** "`@Component` is like registering your class inside Spring automatically — this is exactly what you did with `Car` and `Laptop` last chapter."

> ⚠️ **Common confusion:** Don't think `@Component` *runs* the class — it only *registers* it as a bean.

---

## 3. `@Autowired` — "Give Me the Dependency"

**What it does:** Find this object for me and inject it.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class Student {

    @Autowired
    private Course course;

    public void startStudying() {
        course.study();
    }
}
```

**What happens:**
1. Spring creates `Course`
2. Spring creates `Student`
3. Spring sees `@Autowired`
4. Spring injects `Course` into `Student`

> 🧠 **Teaching line:** "`@Autowired` removes the need for `new`. Spring connects objects for you."

> ⚠️ **Common confusion:**
> - "Where did the object come from?" → From the Spring Container
> - "Do I still use `new`?" → ❌ No. Spring handles it.

#### 🛠️ Build-Along — `Course` + `Student`, chunked (2 steps at a time)

> 📌 **Instructor Note:** This is the same pattern as `Car`/`Engine` from Chapter 3 — you can now give two instructions at once and expect students to keep up, since they've done this exact shape of task before.

**Chunk 1 — say:** "In your Chapter 3 project, create `Course` marked `@Component` with a `study()` method that prints `Studying...`. Then create `Student`, also `@Component`, with an `@Autowired` field of type `Course`, and a `startStudying()` method that calls `course.study()`."

```java
@Component
public class Course {
    public void study() {
        System.out.println("Studying...");
    }
}

@Component
public class Student {

    @Autowired
    private Course course;

    public void startStudying() {
        course.study();
    }
}
```

**Chunk 2 — say:** "In `MainApp`, fetch a `Student` bean from the context instead of `Car`, and call `startStudying()`. Run it."

✅ **Expected output:**
```
Studying...
```

---

## 4. `@Configuration` — "This Class Defines Beans"

Marks a class as a configuration class where beans are defined manually — the contrast to `@Component`, where beans are registered automatically.

```java
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {
}
```

> 💬 **Say:** "`@Configuration` is like a factory class for beans. You already have one of these — it's the `AppConfig` you built in Chapter 3."

**Why we use it:**
- When we want manual control
- When we can't use `@Component` (e.g., classes from external libraries we don't own)

---

## 5. `@Bean` — "Create This Bean Manually"

Tells Spring: "Use this method to create a bean." The `@Bean` method is called by Spring, and the result is stored in the container.

```java
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

**What happens:**
1. Spring calls the method
2. Spring stores the returned object as a bean

> 💬 **Say:** "`@Bean` is manual bean creation, but still managed by Spring."

> ⚠️ **Common confusion:**

| Student Thought | Reality |
|---|---|
| `@Bean` runs every time it's used | ❌ Runs once — singleton by default |
| It's the same as `new` | ❌ Spring still manages the lifecycle |

#### 🛠️ Build-Along — Swap `@Component` for `@Bean` on the same class (chunked)

**Chunk 1 — say:** "Remove `@Component` from `Course`. In your existing `AppConfig`, add a `@Bean`-annotated method called `course()` that returns `new Course()`."

```java
@Configuration
@ComponentScan
public class AppConfig {

    @Bean
    public Course course() {
        return new Course();
    }
}
```

**Chunk 2 — say:** "Run `MainApp` again — same code as before, no changes needed there. Confirm the output is identical."

✅ **Expected output:**
```
Studying...
```

> 💬 **Say, once confirmed:** "Same result, two different ways of registering the same bean. `@Component` is automatic; `@Bean` is manual. Neither is 'more correct' — you'll learn when each is appropriate as the course goes on. Switch `Course` back to `@Component` before continuing."

---

## 6. Putting It All Together (Very Important)

```java
@Component
public class Course {
    public void study() {
        System.out.println("Studying...");
    }
}

@Component
public class Student {

    @Autowired
    private Course course;

    public void start() {
        course.study();
    }
}
```

Run it with your existing `AppConfig` + `MainApp` pair, and you should see `Studying...` printed — the same result you already produced in the build-along above.

**What Spring does:**
1. Scans classes
2. Finds `@Component`
3. Creates beans
4. Sees `@Autowired`
5. Injects dependencies

> 💬 **Say:** "Spring scans, creates, and connects objects automatically — you've now proven this three different ways: with `Car`/`Engine`, `Laptop`, and `Course`/`Student`."

---

## 7. The Core Idea

> 🔥 **Say:** "Annotations replace manual object creation and wiring."

---

## 8. Very Common Student Errors

**❌ Error 1: Forgetting Component Scan**

Spring won't see `@Component` classes without scanning enabled.

#### 🛠️ Build-Along — Make it fail, on purpose

**Say:** "Temporarily delete `@ComponentScan` from `AppConfig`. Run `MainApp`."

✅ **Expected result:** `NoSuchBeanDefinitionException` — Spring never scanned the project, so `Student` was never registered.

> 💬 **Say:** "Put `@ComponentScan` back and run it again to confirm it's fixed. This is the exact same failure you saw with `Laptop` in Chapter 3 — same cause, same fix."

**❌ Error 2: NullPointerException**

```java
Student student = new Student(); // ❌ WRONG
```

**Why?** Spring didn't create it, so Spring can't inject anything into it — `course` stays `null`.

> 🧠 **Teaching line:** "If Spring didn't create it, Spring can't inject it."

## ✅ Key Takeaways

- `@Component` registers a class as a bean — it doesn't run anything by itself
- `@Autowired` tells Spring to find and inject a dependency, replacing manual `new`
- `@Configuration` + `@Bean` is the manual alternative to `@Component`, useful for classes you don't own
- `@Bean` methods run once — the result is cached as a singleton
- Without Spring creating an object, Spring can't inject anything into it (`new` bypasses the container entirely)
- Forgetting `@ComponentScan` produces the same "Spring doesn't know this exists" failure as forgetting `@Component` — both mean the container never registered the class

---

**← Previous: [Chapter 3 — Spring Container & Beans](./chapter-3-spring-container-and-beans.md)** | **Next: Chapter 5 →**

---

## 🔧 What Changed in This Revision

> 🆕 **Not for dictation — for your reference only.**

- Removed the "Quick Setup" section entirely — it duplicated the rig now built in Chapter 3, Build-Along 1. This chapter now opens by reusing that existing project directly.
- Added build-alongs for `@Bean` vs `@Component` (same bean, two registration methods, same output — makes the distinction concrete instead of just described) and for the "forgot `@ComponentScan`" error (students see the exact exception, then fix it, rather than just reading about it).
- Instructions are now lightly chunked (2 steps per instruction) rather than single-step-at-a-time, consistent with the scaffolding fade — this is the first chapter where that shift happens.
