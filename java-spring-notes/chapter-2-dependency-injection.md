# Chapter 2: Dependency Injection (Deep Dive) & IoC in Practice

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them — they're notes on *how* to teach the concept, not the concept itself.

This chapter builds directly on Chapter 1. Where Chapter 1 introduced *what* Dependency Injection (DI) and Inversion of Control (IoC) are, this chapter goes deeper into the **types of DI**, how to implement it **manually in plain Java**, and how **Spring automates it**.

## 🎯 Lesson Objective

By the end of this lesson, students should understand:

- The three types of Dependency Injection (Constructor, Setter, Field)
- The difference between IoC and DI
- How to wire dependencies manually in plain Java
- How Spring automates dependency wiring using annotations
- Common mistakes beginners make when learning DI

## 🗺️ Lesson Roadmap

1. What is Dependency Injection?
2. Types of DI (Constructor, Setter, Field)
3. IoC vs DI
4. Manual DI in plain Java
5. How Spring does it
6. Why DI is powerful
7. Common mistakes
8. Quick exercise
9. Closing statement

---

## 1. What is Dependency Injection (DI)?

Dependency Injection is a technique where an object receives its dependencies from an external source instead of creating them itself.

> 🔁 **Key line to repeat in class:** "Don't create what you need — receive it."

---

## 2. Types of Dependency Injection

### 2.1 Constructor Injection (Most Important)

```java
class Car {
    Engine engine;

    Car(Engine engine) {
        this.engine = engine;
    }
}
```

**✅ Pros:**
- Mandatory dependencies — the object can't exist without them
- Safe — the object is always fully constructed
- Recommended approach in most cases

### 2.2 Setter Injection

```java
class Car {
    Engine engine;

    void setEngine(Engine engine) {
        this.engine = engine;
    }
}
```

**⚠️ Pros:**
- Good for optional dependencies

**❌ Cons:**
- Easy to forget to call the setter
- Object may end up in an incomplete state

### 2.3 Field Injection (Spring Style)

```java
class Car {
    Engine engine;
}
```

> 💬 **Say:** "Spring will inject this automatically — we'll see exactly how in section 5."

**⚠️ Note:**
- Easiest to write
- Not recommended for testing (hard to mock/inject manually without a framework)

---

## 3. IoC vs DI (Very Important)

| Concept | Meaning |
|---|---|
| **IoC** | Giving control of object creation away |
| **DI** | The technique used to *implement* that control transfer |

> 🎯 **One-line explanation:** DI is a way to achieve IoC.

---

## 4. Manual DI (Plain Java)

> 📌 *No Spring required here — this section proves DI works without any framework.*

```java
// Engine.java
public interface Engine {
    void start();
}

// ElectricEngine.java
public class ElectricEngine implements Engine {
    @Override
    public void start() {
        System.out.println("Electric engine starting silently...");
    }
}

// Car.java
public class Car {
    private final Engine engine;

    public Car(Engine engine) {
        this.engine = engine;
    }

    public void drive() {
        engine.start();
        System.out.println("Car is driving.");
    }
}

// MainApp.java
public class MainApp {
    public static void main(String[] args) {
        Engine engine = new ElectricEngine();
        Car car = new Car(engine);
        car.drive();
    }
}
```

> 💬 **Explain:** "This `main` method is acting like a mini Spring container — it creates the dependency and hands it to `Car`. That's manual DI."

---

## 5. How Spring Does It

```java
@Component
class Car {
    @Autowired
    Engine engine;
}
```

> ⚠️ **Conceptual preview only** — this code will **not** run as a standalone program yet. It needs a Spring Boot project with an `ApplicationContext` to actually create and wire the `Car` bean. We'll cover that project setup in a later chapter. For now, focus on the idea, not running it.

> 💬 **Say:** "Spring removes the need for manual wiring."

What's happening here:
- Spring creates the objects (beans)
- Spring injects the dependencies
- You don't write the wiring code yourself — Spring does what `MainApp` did manually in section 4

---

## 6. Why DI is Powerful

- Flexibility
- Easy testing
- Maintainability
- Scalability

> 🧠 **Real statement to drive home:** "DI allows us to change behavior without modifying existing code."

---

## 7. Common Mistakes

Students may:
- Still create objects inside the class instead of receiving them
- Confuse IoC and DI as the same thing
- Think DI only exists *because of* Spring

> 💬 **Correct them:** "DI exists even without Spring — Spring only automates it."

---

## 8. Quick Exercise

Given:

```java
class Phone {
    Battery battery = new Battery();
}
```

**Questions:**
1. Is this tight or loose coupling?
2. Convert it to use Dependency Injection.

<details>
<summary>💡 Click to reveal a suggested solution</summary>

This is **tight coupling** — `Phone` creates its own `Battery` and can't work with any other implementation.

Converted to Constructor Injection:

```java
class Phone {
    Battery battery;

    Phone(Battery battery) {
        this.battery = battery;
    }
}
```

Now `Phone` can receive any `Battery` implementation (e.g. `LithiumBattery`, `SolarBattery`) without changing its own code.

</details>

---

## 9. Closing Statement

> 💬 **Say:** "Dependency Injection is what makes loose coupling practical. It allows objects to remain flexible and independent."

## ✅ Key Takeaways

- DI means receiving dependencies, not creating them
- There are three types: Constructor (safest), Setter (optional deps), Field (easiest, hardest to test)
- IoC is the *what* (giving up control); DI is the *how* (the technique)
- DI works in plain Java — Spring just automates it
- Constructor Injection is the generally recommended default

---

**← Previous: [Chapter 1 — Getting Started with Spring & Design Patterns](./chapter-1-getting-started-with-spring.md)** | **Next: Chapter 3 →**
