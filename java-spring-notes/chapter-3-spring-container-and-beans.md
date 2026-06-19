# Chapter 3: Spring Container & Beans

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

In Chapter 2, we manually wired dependencies ourselves in `MainApp`. This chapter answers the natural follow-up question: if Spring takes that job away from us, **who is actually creating and managing the objects?** The answer is the **Spring Container**.

## 🎯 Lesson Objective

By the end of this lesson, students should understand:

1. What the Spring Container is
2. What a Bean is
3. The types of Spring Containers
4. How Spring creates and manages objects
5. The basic flow of how Spring works internally

## 🗺️ Lesson Roadmap

1. Recap
2. Introduce the Spring Container
3. What is a Bean?
4. What does the container do?
5. Types of Spring Containers
6. How Spring works (high-level flow)
7. Common confusions
8. Exercise
9. Closing statement

---

## 1. Recap

> 💬 **Ask:**
> - "What is Dependency Injection?"
> - "Who was creating objects before Spring?"
>
> **Expected answer:** We were creating them manually in `main`.
>
> "Now the question is — if we are no longer creating objects, then who is?"

---

## 2. Introduce the Spring Container

The Spring Container is the part of Spring responsible for creating, managing, and injecting objects (beans).

> 🧠 **Say:** "The Spring Container is like a factory and manager for all objects in your application."

---

## 3. What is a Bean?

A Bean is any object that is created and managed by the Spring Container.

```java
@Component
class Car {}
```

> ⚠️ **Conceptual preview:** `@Component` won't do anything by itself in a plain Java file — it needs a running Spring Boot application with component scanning enabled, which we'll set up in a later chapter. For now, focus on the concept.

> 💬 **Say:** "This `Car` is now a Bean because Spring manages it."

A Spring **component** is a class managed by the Spring Container, typically marked with annotations like `@Component`, `@Service`, or `@Repository`.

---

## 4. What Does the Container Do?

- Creates objects
- Injects dependencies
- Manages lifecycle
- Configures relationships

> 🧠 **Say:** "Everything we were doing manually, Spring now does automatically."

---

## 5. Types of Spring Containers

### 5.1 BeanFactory (Basic)

The simplest container. Loads beans only when needed (**lazy loading**).

### 5.2 ApplicationContext (Advanced)

The most commonly used container.

**Key features:**
- Loads beans immediately (**eager loading**)
- Supports advanced features
- Used in real-world applications

> 🎯 **Say:** "In practice, we mostly use `ApplicationContext`."

---

## 6. How Spring Works (High-Level Flow)

**Step 1: You define classes**

```java
@Component
class Engine {}
```

**Step 2: Spring scans your application**
Finds classes with annotations like `@Component`.

**Step 3: Spring creates objects (Beans)**

**Step 4: Spring injects dependencies**

```java
@Component
class Car {
    @Autowired
    Engine engine;
}
```

**Step 5: You use the object**

*(Both snippets above are conceptual previews — see the note in Section 3.)*

### 🔄 Flow Summary

| Who | Action |
|---|---|
| You | Define classes |
| Spring | Scans |
| Spring | Creates beans |
| Spring | Injects dependencies |
| You | Use beans |

---

## 7. Common Confusions

| ❌ Misconception | ✅ Correction |
|---|---|
| "Bean = any object" | Bean = an object managed by Spring |
| "Spring automatically knows everything" | Spring only manages what you configure or annotate |
| "`BeanFactory` and `ApplicationContext` are the same" | `ApplicationContext` is more powerful and far more commonly used |

---

## 8. Exercise

```java
class Laptop {}
```

👉 "Is this a Bean?"

<details>
<summary>💡 Click to reveal the answer</summary>

**No** — it's just a plain Java object. Spring isn't aware of it because it has no `@Component` (or similar) annotation, and nothing tells Spring to scan or manage it.

To make it a Bean:

```java
@Component
class Laptop {}
```

*(Conceptual preview — still requires Spring Boot setup to actually run, as noted in Section 3.)*

</details>

---

## 9. Closing Statement

> 🎯 **Say:** "The Spring Container is the brain of Spring — it creates, connects, and manages all objects so we don't have to."

## ✅ Key Takeaways

- The **Spring Container** creates, manages, and injects all beans in your app
- A **Bean** is just an object that Spring manages — typically marked with `@Component`, `@Service`, or `@Repository`
- **`BeanFactory`** is basic and lazy-loading; **`ApplicationContext`** is the advanced, eager-loading container used in almost all real applications
- The core flow: you define classes → Spring scans → Spring creates beans → Spring injects dependencies → you use the beans

---

**← Previous: [Chapter 2 — Dependency Injection (Deep Dive) & IoC in Practice](./chapter-2-dependency-injection.md)** | **Next: Chapter 4 →**
