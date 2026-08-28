# 📘 Chapter 1 – Getting Started with Spring & Design Patterns

> **Instructor Version** — Instructor-only cues are marked with 📌 blockquotes throughout. This file is your delivery script: you dictate/describe, students write notes and build the code live as you guide them.

> 🆕 **Revision note (not for dictation):** This chapter now includes explicit **build-along** blocks. At this stage of the course, scaffolding should be maximally explicit — spell out each line, don't chunk steps together yet. That fades starting around Chapter 4 as students get fluent with the vocabulary.

---

## 🗺️ Lesson Roadmap

Write these on the board in this order as you teach:

1. Code example → tight coupling
2. Definition of tight coupling + problems
3. **Build-along:** run the tight-coupling code, see it work but feel rigid
4. Loose coupling code example
5. **Build-along:** convert it live, run it again
6. Key principle: _"Don't create what you need — receive it."_
7. Inversion of Control (IoC) definition
8. Programming to an interface
9. **Build-along:** add a second Engine type, swap it in with zero changes to `Car`
10. Spring definition
11. What Spring does (Beans, DI, Lifecycle)

---

## 🎯 Lesson Objectives

By the end of this lesson, students should understand — and have personally typed and run code that demonstrates:

- The problem Spring solves
- What tight coupling is and why it's bad
- What Dependency Injection (DI) is
- What Inversion of Control (IoC) means
- What it means to program to an interface
- What Spring does at a high level

---

## 1. Hook

> 📌 **Instructor Note:** Open with this question to get students thinking before you introduce any concept.

Start by writing this on the board:

```java
class Car {
    Engine engine = new Engine();
}
```

Then ask the class:

**"What is the problem with this code?"**

Let students respond before moving on.

---

## 2. The Problem — Tight Coupling

**Tight coupling** is when one class is strongly dependent on another specific class.

In the example above:

- `Car` directly creates an `Engine` inside itself
- `Car` cannot work without that exact `Engine` class
- If `Engine` changes → `Car` must also change

### Why This is a Problem

1. **Hard to change** — modifying one class forces changes in others
2. **Hard to test** — you can't swap in a fake/mock object during testing
3. **Low flexibility** — the code is rigid and brittle
4. **Poor maintenance** — problems in one class ripple across the codebase

> 📌 **Instructor Note:** Use this line to drive the point home:
> _"If I change `Engine` to `ElectricEngine`, I must go into the `Car` class and modify it. In a large system, that's risky — and it happens everywhere."_

---

### 🛠️ Build-Along 1 — Feel the rigidity (explicit, step by step)

> 📌 **Instructor Note:** This is the first hands-on moment of the course. Go slow. Dictate every line, wait for every student to have it typed before moving on. The goal isn't the code — it's the first "I built something and ran it" moment.

Have students create three files and build this exactly, one file at a time:

**Step 1 — say:** "Create a class called `Engine`. Give it one method, `start`, that prints `Engine starting...`"

```java
class Engine {
    void start() {
        System.out.println("Engine starting...");
    }
}
```

**Step 2 — say:** "Now create `Car`. Give it a field of type `Engine`, and create the `Engine` right there in the field declaration, using `new`."

```java
class Car {
    Engine engine = new Engine();

    void drive() {
        engine.start();
        System.out.println("Car is driving.");
    }
}
```

**Step 3 — say:** "Now create a class with a `main` method — call it `MainApp`. Inside `main`, create a `Car` and call `drive()` on it."

```java
public class MainApp {
    public static void main(String[] args) {
        Car car = new Car();
        car.drive();
    }
}
```

**Step 4 — say:** "Run it. You should see two lines print."

✅ **Expected output:**
```
Engine starting...
Car is driving.
```

> 💬 **Say, once it runs:** "It works — but `Car` is stuck with exactly this `Engine`, forever, unless you go edit `Car` itself. Let's fix that."

---

## 3. The Solution — Dependency Injection

> 📌 **Instructor Note:** Ask students before showing the solution:
> _"What if `Car` doesn't create the `Engine` itself?"_

Instead of `Car` creating its own `Engine`, we **pass it in**:

```java
class Car {
    Engine engine;

    Car(Engine engine) {
        this.engine = engine;
    }
}
```

### Key Principle

> **"Don't create what you need — receive it."**

What changed:

- `Engine` is now given to `Car` from the outside
- `Car` is flexible — it works with any `Engine` implementation
- We can swap the implementation without touching `Car`

This is **Dependency Injection (DI)** — providing a class with its dependencies rather than letting it create them itself.

---

### 🛠️ Build-Along 2 — Convert it live (explicit, step by step)

**Step 1 — say:** "Go back to your `Car` class. Delete `= new Engine()`. Instead, add a constructor that takes an `Engine` and assigns it to the field."

```java
class Car {
    Engine engine;

    Car(Engine engine) {
        this.engine = engine;
    }

    void drive() {
        engine.start();
        System.out.println("Car is driving.");
    }
}
```

**Step 2 — say:** "Now go to `MainApp`. You now have to create the `Engine` yourself, and hand it to `Car` when you build it."

```java
public class MainApp {
    public static void main(String[] args) {
        Engine engine = new Engine();
        Car car = new Car(engine);
        car.drive();
    }
}
```

**Step 3 — say:** "Run it again."

✅ **Expected output:** same two lines as before — **but ask the class:** "The output didn't change. So what actually changed?"

> 🧠 **Answer to draw out of them:** Nothing changed in *behavior* — everything changed in *who's in control*. `Car` no longer decides what `Engine` it gets. `MainApp` does. That's the whole shift.

---

## 4. Programming to an Interface

Taking DI one step further — instead of depending on a concrete class, we depend on an **interface or parent class**.

```java
interface Engine {
    void start();
}

class PetrolEngine implements Engine {
    public void start() { System.out.println("Petrol engine starting..."); }
}

class ElectricEngine implements Engine {
    public void start() { System.out.println("Electric engine starting..."); }
}

class Car {
    Engine engine;

    Car(Engine engine) {
        this.engine = engine;
    }
}
```

Now `Car` doesn't care *which* engine it gets — only that it *has* one.

> **"Program to a general type, not a specific implementation. This lets you change behavior without modifying existing code."**

This is the foundation Spring builds everything on.

---

### 🛠️ Build-Along 3 — Swap engines with zero changes to `Car` (explicit, step by step)

**Step 1 — say:** "Turn `Engine` from a class into an `interface`. Give it one method, `start`, no body."

```java
interface Engine {
    void start();
}
```

**Step 2 — say:** "Rename your old `Engine` class to `PetrolEngine`, and make it `implements Engine`."

```java
class PetrolEngine implements Engine {
    public void start() {
        System.out.println("Petrol engine starting...");
    }
}
```

**Step 3 — say:** "Now create a brand new class, `ElectricEngine`, that also implements `Engine`."

```java
class ElectricEngine implements Engine {
    public void start() {
        System.out.println("Electric engine starting...");
    }
}
```

**Step 4 — say:** "Go to `MainApp`. Change one line — swap `new PetrolEngine()` for `new ElectricEngine()`. Do NOT touch `Car`."

```java
public class MainApp {
    public static void main(String[] args) {
        Engine engine = new ElectricEngine();
        Car car = new Car(engine);
        car.drive();
    }
}
```

**Step 5 — say:** "Run it."

✅ **Expected output:**
```
Electric engine starting...
Car is driving.
```

> 💬 **Say, and let it land:** "You just changed the *entire behavior* of your program by editing one line, in one file — and you never opened `Car.java`. That's the payoff of everything we just built."

---

## 5. Inversion of Control (IoC)

What we did in the examples above has a name:

> **Inversion of Control (IoC)** means transferring the responsibility of object creation away from your own class to an external system.

Previously, `Car` was in control — it created its own `Engine`.  
After DI, something *outside* `Car` is responsible for creating and providing the `Engine`.

We have **inverted** who is in control of object creation.

> 📌 **Instructor Note:** Point back at `MainApp` and say: _"Right now, YOU are the 'external system.' You are the container. In a few chapters, Spring takes that job away from you too."_

---

## 6. Introducing Spring

> 📌 **Instructor Note:** Bridge from IoC to Spring with this line:
> _"We've been doing this manually. Spring is the tool that handles it automatically."_

**Spring** is a framework that manages objects and their dependencies in a Java application.

Think of it this way:

> 🏭 **"Spring is like a factory + manager for your objects."**

---

## 7. What Spring Does

Spring handles four things automatically:

| # | What Spring Does | Plain English |
|---|-----------------|---------------|
| 1 | Creates objects (**Beans**) | Spring builds your objects so you don't have to |
| 2 | Connects them (**Dependency Injection**) | Spring wires objects together automatically |
| 3 | Manages their **lifecycle** | Spring controls when objects are created and destroyed |
| 4 | Configures **application structure** | Spring organises how your app is assembled |

---

## 8. The Spring Container

Spring uses a **container** to manage all of this. The container holds your objects and wires them together.

There are two main types:

| Container | Description |
|-----------|-------------|
| `BeanFactory` | The basic container — lightweight, loads beans lazily |
| `ApplicationContext` | The full-featured container — extends `BeanFactory`, used in most real applications |

> 📌 **Instructor Note:** For now, tell students to think of both as "the thing that manages our objects." They'll work with `ApplicationContext` in practice.

---

## 9. Bean Lifecycle

Every object Spring manages is called a **Bean**. Spring controls the full life of each bean:

| Stage | What Happens |
|-------|-------------|
| **Created** | Spring instantiates the object (calls the constructor) |
| **Initialized** | Spring injects dependencies and runs any setup logic |
| **Destroyed** | Spring cleans up the object when it's no longer needed |

> 📌 **Instructor Note:** Summarise with: _"Spring controls the entire life of your objects — from birth to death."_

---

## 10. Design Patterns in Spring

Spring is built on well-known design patterns. Students will encounter these throughout the course:

| Pattern | Where Spring Uses It |
|---------|---------------------|
| **Factory Pattern** | Spring container creates objects for you |
| **Dependency Injection** | Spring wires dependencies between beans |
| **Template Pattern** | `JdbcTemplate`, `RestTemplate`, etc. |
| **Proxy Pattern (AOP)** | Spring wraps beans to add cross-cutting concerns |

> 📌 **Instructor Note:** Don't go deep on any of these yet. Just mention them so students know these aren't arbitrary Spring features — they're patterns they'll recognise as they grow.

---

## ❓ Class Discussion Questions

Use these to keep students engaged throughout the lesson:

1. "Why is tight coupling bad?"
2. "What happens to `Car` if we change `Engine`?"
3. "Who should be responsible for creating objects?"
4. "What does the Spring container manage?"
5. "In Build-Along 3, who was the 'external system' that created the `Engine`?"

---

## 📋 Lesson Summary

| Concept | One-line Definition |
|---------|-------------------|
| Tight Coupling | A class that depends directly on another specific class |
| Dependency Injection | Providing a class its dependencies from the outside |
| Programming to an Interface | Depending on a general type, not a specific implementation |
| Inversion of Control | Shifting object creation responsibility to an external system |
| Spring | A framework that implements IoC and DI automatically |
| Bean | Any object managed by the Spring container |
| Spring Container | The system Spring uses to create, wire, and manage beans |

---

## 🔧 What Changed in This Revision

> 🆕 **Not for dictation — for your reference only.**

- Added three fully runnable **Build-Along** blocks (tight coupling → DI → programming to an interface), each step-by-step and explicit, matching the earliest, most-scaffolded stage of your fading-instruction method.
- Every code example in this chapter can now actually be typed and run by students, with real console output — closing the "three chapters of theory before anything runs" gap.
- Added a discussion question tying Build-Along 3 back to the IoC definition, to check the concept actually landed.
