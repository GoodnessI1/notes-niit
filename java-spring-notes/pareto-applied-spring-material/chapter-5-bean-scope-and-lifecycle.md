# Chapter 5: Bean Scope & Lifecycle

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. This file is your delivery script: you dictate/describe, students write notes and build the code live as you guide them.

> 🆕 **Revision note (not for dictation):** This chapter's code was already runnable — it just needs to point at the right rig. All references to "the `AppConfig` bootstrap" now point back to **Chapter 3**, where that rig was actually built (not Chapter 4, which no longer re-teaches it). Scaffolding continues to fade here — instructions are chunked further than Chapter 4.

Chapter 4 showed how Spring creates and wires beans. This chapter answers two follow-up questions: **how many instances** of a bean does Spring actually create, and **how long do they live**?

## 🎯 Lesson Objective

Students should understand — and have run code demonstrating:

- What bean scope means and why it matters
- The difference between Singleton (default) and Prototype scopes
- A common pitfall when injecting a prototype bean into a singleton
- The phases of the Spring bean lifecycle
- How to hook into bean creation and destruction with `@PostConstruct` and `@PreDestroy`

## 🗺️ Lesson Roadmap

1. The Big Question
2. Bean Scope (core idea)
3. Singleton Scope (default) — **build-along**
4. Prototype Scope — **build-along**
5. Bean Lifecycle
6. Lifecycle Example — **build-along**
7. Running It (Main Class)
8. Mini Exercise — **build-along**
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

#### 🛠️ Build-Along — Prove singleton scope for real (chunked)

> 📌 **Instructor Note:** Uses the exact same `AppConfig` project from Chapter 3 — no new setup. Two steps per instruction now; students should keep pace comfortably.

**Chunk 1 — say:** "In your existing project, create a `Course` class marked `@Component` — no fields needed. In `MainApp`, fetch `Course` from the context twice, into two variables `c1` and `c2`."

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MainApp {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        Course c1 = context.getBean(Course.class);
        Course c2 = context.getBean(Course.class);
        System.out.println(c1 == c2);
    }
}
```

**Chunk 2 — say:** "Run it and note the output."

✅ **Output:** `true`

> 💬 **Say:** "Spring creates the object once and keeps reusing it. `c1` and `c2` are literally the same object in memory."

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

#### 🛠️ Build-Along — Prove prototype scope, then break the intuition (chunked)

**Chunk 1 — say:** "Add `@Scope(\"prototype\")` above `@Component` on your `Course` class. Run the exact same `MainApp` from the Singleton build-along, no changes needed there."

✅ **Output:** `false`

> 💬 **Say:** "Same code, one annotation changed the entire behavior. Prototype means 'give me a fresh object every time.'"

**Chunk 2 — say:** "Now create `Student`, `@Component`, with an `@Autowired` field of type `Course`. Add a getter `getCourse()`. In `MainApp`, fetch two `Student` beans and compare `student1.getCourse() == student2.getCourse()`."

```java
@Component
public class Student {

    @Autowired
    private Course course;

    public Course getCourse() {
        return course;
    }
}
```

```java
Student student1 = context.getBean(Student.class);
Student student2 = context.getBean(Student.class);
System.out.println(student1.getCourse() == student2.getCourse());
```

**Chunk 3 — say:** "Predict the output before you run it — then run it and check."

✅ **Output:** `true` — **most students will predict `false`. This is the point.**

> 💬 **Say, once they see it:** "`Course` is prototype-scoped, but `student1` and `student2` share the exact same `Course` instance. Why? Because `Student` itself is a singleton — Spring only wires its dependencies once, at the moment `Student` is created. Prototype only gives you a fresh object when you ask the *container* directly — not when it's injected into something that only gets built once."

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

> 💬 **Say:** "You just watched this happen. Prototype behaves differently when injected — it's created only once, at injection time."

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

#### 🛠️ Build-Along — Watch the full lifecycle print in order (chunked)

**Chunk 1 — say:** "Switch `Course` back to default (singleton) scope — remove `@Scope(\"prototype\")`. Add the constructor, `@PostConstruct` method, and `@PreDestroy` method shown above, each with its own print statement."

**Chunk 2 — say:** "Update `MainApp`: fetch a `Course` bean, then call `context.close()` at the end — this matters, don't skip it."

```java
public class Main {
    public static void main(String[] args) {

        AnnotationConfigApplicationContext context =
                new AnnotationConfigApplicationContext(AppConfig.class);

        context.getBean(Course.class);

        context.close(); // VERY IMPORTANT
    }
}
```

**Chunk 3 — say:** "Run it and read the three lines of output in order."

✅ **Output:**
```
Constructor called
Bean initialized
Bean destroyed
```

> 💬 **Say:** "Comment out `context.close()` and run it again — watch 'Bean destroyed' disappear. That line is what triggers `@PreDestroy`. Without it, Spring never gets the signal to clean up."

> 🧠 **Teaching line:** "Spring manages the full lifecycle — from creation to destruction."
>
> "Spring creates prototype beans, but does not manage their destruction."

---

## 7. Running It (Main Class)

> 📌 Closing the context matters here — without it, `@PreDestroy` never fires. Students should have already confirmed this themselves in the build-along above.

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

---

## 8. Mini Exercise

1. Create a `Laptop` class
2. Make it prototype-scoped
3. Inject it into `Student` (alongside the existing `Course` field)
4. Print object references to see what's actually fresh and what isn't

#### 🛠️ Build-Along — Do the full exercise (chunked, students drive this one)

> 📌 **Instructor Note:** By this point, give the instructions as the exercise states them (numbered 1–4 above) and let students work it out largely on their own — step in only where they get stuck. This is closer to independent application than earlier build-alongs.

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
- Forgetting `context.close()` silently skips `@PreDestroy` — this is easy to miss and worth testing deliberately

---

**← Previous: [Chapter 4 — Spring Annotations Deep Dive](./chapter-4-spring-annotations-deep-dive.md)** | **Next: Chapter 6 →**

---

## 🔧 What Changed in This Revision

> 🆕 **Not for dictation — for your reference only.**

- Updated all references to "the `AppConfig` bootstrap" to point to Chapter 3 (where the rig actually now lives), not Chapter 4.
- Formalized the existing runnable examples as explicit Build-Alongs, matching the format used since Chapter 1 — the code itself needed little change since it was already functional, but it wasn't previously framed as something every student builds and runs.
- Added a "predict before you run it" moment in the Prototype build-along (Chunk 3) — most students will wrongly predict `false`, which makes the actual result land harder as a genuine surprise rather than confirmation of something they were just told.
- Added a deliberate "remove `context.close()` and watch `@PreDestroy` disappear" step, since this is a common silent bug students will hit later without realizing why.
- Continued the scaffolding fade: instructions are chunked further than Chapter 4, and the Mini Exercise is framed as mostly independent work rather than a fully dictated build-along.
