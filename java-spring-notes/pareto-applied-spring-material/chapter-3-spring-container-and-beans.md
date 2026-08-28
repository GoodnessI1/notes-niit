# Chapter 3: Spring Container & Beans

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. This file is your delivery script: you dictate/describe, students write notes and build the code live as you guide them.

> 🆕 **Revision note (not for dictation):** This is the most important structural change in the batch. The minimal runnable Spring setup — previously introduced in Chapter 4 — now lives here. Every `@Component` / container example in this chapter is fully build-along and runs for real. Nothing in this chapter is a "conceptual preview" anymore.

In Chapter 2, we manually wired dependencies ourselves in `MainApp`. This chapter answers the natural follow-up question: if Spring takes that job away from us, **who is actually creating and managing the objects?** The answer is the **Spring Container** — and today, for the first time, you'll have Spring actually do it, live, on your machine.

## 🎯 Lesson Objective

By the end of this lesson, students should understand — and have run real Spring code demonstrating:

1. What the Spring Container is
2. What a Bean is
3. The types of Spring Containers
4. How Spring creates and manages objects
5. The basic flow of how Spring works internally

## 🗺️ Lesson Roadmap

1. Recap
2. Introduce the Spring Container
3. **Build-along:** set up a real, running Spring container (the "test rig")
4. What is a Bean? — **build-along**
5. What does the container do?
6. Types of Spring Containers
7. How Spring works (high-level flow) — **build-along**
8. Common confusions
9. Exercise — **build-along**
10. Closing statement

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

## 3. 🧰 Build-Along — Set Up a Real Spring Container (explicit, step by step)

> 📌 **Instructor Note:** This is the payoff moment for the last two chapters. Go slow, and don't skip explaining *why* each piece exists — students have been waiting since Chapter 1 to see Spring actually run something. Once this rig exists, it is reused for the rest of this chapter and every chapter after — you never rebuild it from scratch again.

**Step 1 — say:** "Add the Spring dependency to your project." (Maven `pom.xml`)

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>6.1.13</version>
</dependency>
```

> 💬 **Say while they add it:** "This one dependency is what turns plain Java into Spring. Everything from here on depends on this being in place."

**Step 2 — say:** "Create a new class called `AppConfig`. Mark it `@Configuration` and `@ComponentScan`."

```java
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan
public class AppConfig {
}
```

> 💬 **Say:** "`@Configuration` tells Spring 'this class configures the app.' `@ComponentScan` tells Spring 'go look through my project for classes I've marked as beans.' This class is the map Spring uses to find everything."

**Step 3 — say:** "Take your `Car` class from Chapter 1 or 2. Add `@Component` above the class."

```java
import org.springframework.stereotype.Component;

@Component
public class Car {
    public void drive() {
        System.out.println("Car is driving.");
    }
}
```

> 💬 **Say:** "This is the same `Car` you've been building for two chapters — the only thing new is this one annotation. `@Component` is you telling Spring: 'manage this one.'"

**Step 4 — say:** "Create `MainApp`. This time, instead of creating `Car` yourself with `new`, ask the Spring container for it."

```java
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MainApp {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context =
                new AnnotationConfigApplicationContext(AppConfig.class);

        Car car = context.getBean(Car.class);
        car.drive();

        context.close();
    }
}
```

**Step 5 — say:** "Run it."

✅ **Expected output:**
```
Car is driving.
```

> 💬 **Say, and pause here — this matters:** "Look at what you didn't do. You never wrote `new Car()`. You asked the container for a `Car`, and it handed you one, fully built. That line — `context.getBean(Car.class)` — is Spring doing the exact job `MainApp` was doing manually in Chapter 2. This container is now your test rig. Every example for the rest of this chapter plugs into it — same `AppConfig`, same `MainApp` pattern, just a different bean."

---

## 4. What is a Bean?

A Bean is any object that is created and managed by the Spring Container.

```java
@Component
class Car {}
```

> 💬 **Say:** "This `Car` is now a Bean because Spring manages it — you just proved that in the build-along above."

A Spring **component** is a class managed by the Spring Container, typically marked with annotations like `@Component`, `@Service`, or `@Repository`.

#### 🛠️ Build-Along — Add a second bean and inject it

**Step 1 — say:** "Create an `Engine` class from Chapter 1, mark it `@Component` too."

```java
@Component
public class Engine {
    public void start() {
        System.out.println("Engine starting...");
    }
}
```

**Step 2 — say:** "In `Car`, add an `@Autowired` field for `Engine`, and call `engine.start()` inside `drive()`."

```java
@Component
public class Car {

    @Autowired
    private Engine engine;

    public void drive() {
        engine.start();
        System.out.println("Car is driving.");
    }
}
```

**Step 3 — say:** "Run the same `MainApp` from the build-along above — no changes needed there."

✅ **Expected output:**
```
Engine starting...
Car is driving.
```

> 💬 **Say:** "You didn't create the `Engine` anywhere. You didn't wire it to `Car` anywhere. Spring scanned both classes, saw `@Component` on each, saw `@Autowired`, and connected them itself. This is everything from Chapters 1 and 2 — now automatic."

---

## 5. What Does the Container Do?

- Creates objects
- Injects dependencies
- Manages lifecycle
- Configures relationships

> 🧠 **Say:** "Everything we were doing manually, Spring now does automatically — and you just watched it happen."

---

## 6. Types of Spring Containers

### 6.1 BeanFactory (Basic)

The simplest container. Loads beans only when needed (**lazy loading**).

### 6.2 ApplicationContext (Advanced)

The most commonly used container — this is what `AnnotationConfigApplicationContext` in your build-along actually is.

**Key features:**
- Loads beans immediately (**eager loading**)
- Supports advanced features
- Used in real-world applications

> 🎯 **Say:** "In practice, we mostly use `ApplicationContext` — you've been using one all lesson."

---

## 7. How Spring Works (High-Level Flow)

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

> 💬 **Say:** "This isn't a diagram of something abstract — this is exactly what you just built and ran above. Steps 1 through 5 are `Engine`, `Car`, and `MainApp`, in order."

### 🔄 Flow Summary

| Who | Action |
|---|---|
| You | Define classes |
| Spring | Scans |
| Spring | Creates beans |
| Spring | Injects dependencies |
| You | Use beans |

---

## 8. Common Confusions

| ❌ Misconception | ✅ Correction |
|---|---|
| "Bean = any object" | Bean = an object managed by Spring |
| "Spring automatically knows everything" | Spring only manages what you configure or annotate |
| "`BeanFactory` and `ApplicationContext` are the same" | `ApplicationContext` is more powerful and far more commonly used |

---

## 9. Exercise

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

</details>

#### 🛠️ Build-Along — Prove it, don't just discuss it

**Step 1 — say:** "Create `Laptop` with no annotation at all. Add a `use()` method that prints something."

```java
class Laptop {
    void use() {
        System.out.println("Laptop in use.");
    }
}
```

**Step 2 — say:** "In `MainApp`, try to fetch it from the container: `context.getBean(Laptop.class)`. Run it."

✅ **Expected result:** an exception — Spring throws `NoSuchBeanDefinitionException` because it never scanned `Laptop` in as a bean.

> 💬 **Say, once they see the error:** "That error is the answer to the exercise question, proven instead of just stated. Spring genuinely does not know this class exists."

**Step 3 — say:** "Now add `@Component` above `Laptop`, and run the exact same `MainApp` code again — no other changes."

✅ **Expected output:**
```
Laptop in use.
```

> 💬 **Say:** "One annotation was the entire difference between 'Spring has no idea this exists' and 'Spring fully manages it.'"

---

## 10. Closing Statement

> 🎯 **Say:** "The Spring Container is the brain of Spring — it creates, connects, and manages all objects so we don't have to. And now you've built one yourself."

## ✅ Key Takeaways

- The **Spring Container** creates, manages, and injects all beans in your app
- A **Bean** is just an object that Spring manages — typically marked with `@Component`, `@Service`, or `@Repository`
- **`BeanFactory`** is basic and lazy-loading; **`ApplicationContext`** is the advanced, eager-loading container used in almost all real applications
- The core flow: you define classes → Spring scans → Spring creates beans → Spring injects dependencies → you use the beans
- The `AppConfig` + `MainApp` rig built in this chapter is reused going forward — you don't rebuild it each time, you just add beans to it

---

**← Previous: [Chapter 2 — Dependency Injection (Deep Dive) & IoC in Practice](./chapter-2-dependency-injection.md)** | **Next: Chapter 4 →**

---

## 🔧 What Changed in This Revision

> 🆕 **Not for dictation — for your reference only.**

- Pulled the runnable Spring setup (Maven dependency + `AppConfig` + `MainApp` with `AnnotationConfigApplicationContext`) forward from Chapter 4 into this chapter, as Build-Along 1. This was the single highest-leverage fix identified in the batch review — it closes the three-chapter gap where students heard about Spring but never ran it.
- Every `@Component`/container example that was previously flagged "conceptual preview, won't run yet" is now a real build-along with real output.
- Added a build-along for the Laptop exercise that lets students *see the exception* when a class isn't a bean, then *see it work* after adding `@Component` — proof instead of just an explained answer.
- **Heads-up for Chapter 4:** since the rig now lives here, Chapter 4's "Quick Setup" section will be redundant when we get to it — it should be trimmed down to a one-line callback ("using the rig from Chapter 3...") instead of re-teaching the same setup. Flagging this now so it's not a surprise when we revise Chapter 4.
