# Chapter 4: Spring Annotations Deep Dive

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

In Chapter 3, we learned that the Spring Container creates and manages beans — but not yet *how* we tell it which classes to manage. This chapter covers the annotations that do exactly that: `@Component`, `@Autowired`, `@Configuration`, and `@Bean`.

## 🎯 Lesson Goal

Students should understand:

- How Spring automatically creates and manages beans
- How we inject dependencies using annotations
- The difference between manual configuration and annotation-based configuration

## 🧰 Quick Setup: Running the Examples in This Chapter

Every example below needs a Spring **container** to actually run — a plain `Course` or `Student` class with an annotation on it does nothing by itself. Here's the smallest possible setup to make that container exist, using plain Spring (not Spring Boot, since we haven't covered full project setup yet).

**1. Add the dependency** (Maven `pom.xml`):

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>6.1.13</version>
</dependency>
```

**2. Create a config class that scans for components:**

```java
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan
public class AppConfig {
}
```

**3. Create the container and pull a bean out of it:**

```java
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MainApp {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context =
                new AnnotationConfigApplicationContext(AppConfig.class);

        Student student = context.getBean(Student.class);
        student.startStudying();

        context.close();
    }
}
```

> 💬 **Say:** "This `AppConfig` + `MainApp` pair is your test rig for the rest of this chapter — every `@Component` example below will actually run once it's scanned by this setup."

We'll revisit this setup properly (with full Spring Boot project structure) in a later chapter. For now, this is enough to prove every annotation in this chapter actually works.

## 🗺️ Lesson Roadmap

1. The Big Shift
2. `@Component` — create this object for me
3. `@Autowired` — give me the dependency
4. `@Configuration` — this class defines beans
5. `@Bean` — create this bean manually
6. Putting it all together
7. The core idea
8. Very common student errors

---

## 1. The Big Shift (Very Important)

Before annotations, we did this manually:

```java
Student student = new Student(new Course());
```

> 👉 **Say:** "We stop creating objects ourselves… and let Spring handle it."
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

> 💬 **Say:** "`@Component` is like registering your class inside Spring automatically."

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

---

## 4. `@Configuration` — "This Class Defines Beans"

Marks a class as a configuration class where beans are defined manually — the contrast to `@Component`, where beans are registered automatically.

```java
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {
}
```

**Why we use it:**
- When we want manual control
- When we can't use `@Component` (e.g., classes from external libraries we don't own)

> 💬 **Say:** "`@Configuration` is like a factory class for beans."

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

**Try it** — same `MainApp` pattern as the Quick Setup section, just fetching the bean differently:

```java
AnnotationConfigApplicationContext context =
        new AnnotationConfigApplicationContext(AppConfig.class);

Course course = context.getBean(Course.class);
course.study();

context.close();
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

Run it with the same `AppConfig` + `MainApp` pair from the **Quick Setup** section (swap `student.startStudying()` for `student.start()`), and you should see `Studying...` printed.

**What Spring does:**
1. Scans classes
2. Finds `@Component`
3. Creates beans
4. Sees `@Autowired`
5. Injects dependencies

> 💬 **Say:** "Spring scans, creates, and connects objects automatically."

---

## 7. The Core Idea

> 🔥 **Say:** "Annotations replace manual object creation and wiring."

---

## 8. Very Common Student Errors

**❌ Error 1: Forgetting Component Scan**

Spring won't see `@Component` classes without scanning enabled.

👉 **Fix:**

```java
@ComponentScan("com.example")
```

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

---

**← Previous: [Chapter 3 — Spring Container & Beans](./chapter-3-spring-container-and-beans.md)** | **Next: Chapter 5 →**
