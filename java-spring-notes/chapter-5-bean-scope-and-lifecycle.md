# Chapter 5: Bean Scope & Lifecycle

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

Chapter 4 showed how Spring creates and wires beans. This chapter answers two follow-up questions: **how many instances** of a bean does Spring actually create, and **how long do they live**?

## 🎯 Lesson Objective

Students should understand:

- What bean scope means and why it matters
- The difference between Singleton (default) and Prototype scopes
- A common pitfall when injecting a prototype bean into a singleton
- The phases of the Spring bean lifecycle
- How to hook into bean creation and destruction with `@PostConstruct` and `@PreDestroy`

## 🗺️ Lesson Roadmap

1. The Big Question
2. Bean Scope (core idea)
3. Singleton Scope (default)
4. Prototype Scope
5. Bean Lifecycle
6. Lifecycle Example
7. Running It (Main Class)
8. Mini Exercise
9. Closing Statement

---

## 1. The Big Question

> 💬 **Ask:** "If I request a bean 5 times… do I get 5 different objects?"

> 🧠 **Say:** "Spring controls not just creation, but how many instances exist."

---

## 2. Bean Scope (Core Idea)

> 🧠 **Say:** "Scope defines how many instances of a bean Spring creates."

---

## 3. Singleton Scope (Default)

👉 Only **ONE** instance exists in the entire application.

```java
@Component
public class Course {
}
```

**Test it** (using the `AppConfig` bootstrap from Chapter 4):

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

Course c1 = context.getBean(Course.class);
Course c2 = context.getBean(Course.class);
System.out.println(c1 == c2);
```

✅ **Output:** `true`

> 💬 **Say:** "Spring creates the object once and keeps reusing it."

> 🔥 **Analogy:** "Singleton is like a school principal — only one exists, everyone uses the same one."

### When to Use Singleton ✅ (Default)

Use Singleton when:
1. The object has no changing internal state per user
2. It's shared across the whole app
3. It represents a service or piece of logic

> 🧠 **Say:** "If the object represents behavior, not identity — make it singleton."

**Why Singleton?**
- **Memory efficient** — only one object ever exists
- **Faster** — no repeated object creation
- **Centralized logic** — everyone uses the same instance, and most objects don't actually need duplication

---

## 4. Prototype Scope

👉 A **new** object is created every time it's requested.

```java
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

@Component
@Scope("prototype")
public class Course {
}
```

> 📌 *(Same `Course` class as above — just swap the annotation to compare behavior.)*

**Test it again:**

```java
Course c1 = context.getBean(Course.class);
Course c2 = context.getBean(Course.class);
System.out.println(c1 == c2);
```

✅ **Output:** `false`

> 💬 **Say:** "Prototype means 'give me a fresh object every time.'"

### ⚠️ Very Important Confusion

What happens here?

```java
@Component
public class Student {

    @Autowired
    private Course course;
}
```

If `Course` is prototype-scoped…

- 🤯 **Students expect:** a new `Course` every time
- ❌ **Reality:** only **ONE** is ever injected

> 💬 **Say:** "Prototype behaves differently when injected — it's created only once, at injection time."

### When to Use Prototype 🔁

Use Prototype when:
- The object has state that changes per use
- Each user/request needs a fresh copy
- The object represents data or a session-like entity

---

## 5. Bean Lifecycle

> 🧠 **Say:** "When does Spring create and destroy objects?"

**Lifecycle steps:**
1. **Bean created** — Spring creates the object
2. **Dependencies injected** — `@Autowired` happens
3. **Init method runs** — custom initialization
4. **Bean used** — your application runs
5. **Destroy method runs** — before shutdown

---

## 6. Lifecycle Example

Using `@PostConstruct` and `@PreDestroy`:

```java
import jakarta.annotation.PostConstruct;
import jakarta.annotation.PreDestroy;
import org.springframework.stereotype.Component;

@Component
public class Course {

    public Course() {
        System.out.println("Constructor called");
    }

    @PostConstruct
    public void init() {
        System.out.println("Bean initialized");
    }

    @PreDestroy
    public void destroy() {
        System.out.println("Bean destroyed");
    }
}
```

---

## 7. Running It (Main Class)

> 📌 Closing the context matters here — without it, `@PreDestroy` never fires.

```java
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Main {
    public static void main(String[] args) {

        AnnotationConfigApplicationContext context =
                new AnnotationConfigApplicationContext(AppConfig.class);

        context.getBean(Course.class);

        context.close(); // VERY IMPORTANT
    }
}
```

✅ **Output:**
```
Constructor called
Bean initialized
Bean destroyed
```

> 🧠 **Teaching line:** "Spring manages the full lifecycle — from creation to destruction."
>
> "Spring creates prototype beans, but does not manage their destruction."

---

## 8. Mini Exercise

1. Create a `Laptop` class
2. Make it prototype-scoped
3. Inject it into `Student` (alongside the existing `Course` field)
4. Print object references to see what's actually fresh and what isn't

<details>
<summary>💡 Click to reveal a suggested solution</summary>

```java
@Component
@Scope("prototype")
public class Laptop {
}

@Component
public class Student {

    @Autowired
    private Course course;

    @Autowired
    private Laptop laptop;

    public Laptop getLaptop() {
        return laptop;
    }
}
```

```java
Laptop fresh1 = context.getBean(Laptop.class);
Laptop fresh2 = context.getBean(Laptop.class);
System.out.println(fresh1 == fresh2); // false — fetched directly, prototype works as expected

Student student = context.getBean(Student.class);
System.out.println(student.getLaptop() == fresh1); // false — Student got its own injected copy

Student studentAgain = context.getBean(Student.class);
System.out.println(student.getLaptop() == studentAgain.getLaptop()); // true — same Student singleton, same fixed Laptop forever
```

This shows the gotcha from Section 4 in action: fetching `Laptop` directly from the container gives a fresh instance every time, but once it's injected into a singleton like `Student`, that copy is locked in for the lifetime of the app.

</details>

---

## 9. Closing Statement

> 🧠 **Say:** "We choose scope based on whether the object should be shared or independent."

## ✅ Key Takeaways

- **Singleton** (default) — one shared instance, used for services and stateless logic
- **Prototype** — a fresh instance every time it's requested directly from the container
- Injecting a prototype bean into a singleton locks in just one instance — Spring resolves the dependency once, at injection time
- Spring manages the full lifecycle of beans it creates — `@PostConstruct` runs after creation, `@PreDestroy` runs before shutdown
- Spring does **not** manage the destruction of prototype beans — only singletons get that lifecycle hook

---

**← Previous: [Chapter 4 — Spring Annotations Deep Dive](./chapter-4-spring-annotations-deep-dive.md)** | **Next: Chapter 6 →**
