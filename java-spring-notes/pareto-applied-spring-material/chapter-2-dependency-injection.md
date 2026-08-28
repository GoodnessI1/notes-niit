# Chapter 2: Dependency Injection (Deep Dive) & IoC in Practice

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. This file is your delivery script: you dictate/describe, students write notes and build the code live as you guide them.

> 🆕 **Revision note (not for dictation):** Build-Along blocks added throughout. Still early in the course — keep most steps explicit, but you can start lightly chunking 2 small steps together where noted, if Chapter 1's build-alongs went smoothly for the cohort.

This chapter builds directly on Chapter 1. Where Chapter 1 introduced *what* Dependency Injection (DI) and Inversion of Control (IoC) are, this chapter goes deeper into the **types of DI**, how to implement it **manually in plain Java**, and how **Spring automates it**.

## 🎯 Lesson Objective

By the end of this lesson, students should understand — and have typed and run code demonstrating:

- The three types of Dependency Injection (Constructor, Setter, Field)
- The difference between IoC and DI
- How to wire dependencies manually in plain Java
- Why field injection can't really be demonstrated without a framework (this is the setup for "why Spring" in Chapter 3)
- Common mistakes beginners make when learning DI

## 🗺️ Lesson Roadmap

1. What is Dependency Injection?
2. Types of DI (Constructor, Setter, Field) — **build-along each one**
3. IoC vs DI
4. Manual DI in plain Java — **build-along**
5. How Spring does it (preview only — full build-along in Chapter 3)
6. Why DI is powerful
7. Common mistakes
8. Quick exercise — **build-along**
9. Closing statement

---

## 1. What is Dependency Injection (DI)?

Dependency Injection is a technique where an object receives its dependencies from an external source instead of creating them itself.

> 🔁 **Key line to repeat in class:** "Don't create what you need — receive it."

---

## 2. Types of Dependency Injection

> 📌 **Instructor Note:** Students already built constructor injection by hand in Chapter 1 (Build-Along 2), so section 2.1 below should feel like a fast, familiar recap — don't over-explain it. Sections 2.2 and 2.3 are genuinely new; give those the full explicit treatment.

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

> 💬 **Say:** "You already built this exact thing last chapter. This is the same pattern — now we're giving it a name."

---

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

#### 🛠️ Build-Along — See the setter pitfall for real (explicit, step by step)

**Step 1 — say:** "Change your `Car` class. Remove the constructor. Add a setter method called `setEngine` instead."

```java
class Car {
    Engine engine;

    void setEngine(Engine engine) {
        this.engine = engine;
    }

    void drive() {
        engine.start();
        System.out.println("Car is driving.");
    }
}
```

**Step 2 — say:** "In `MainApp`, create the `Car` — but this time, deliberately do NOT call `setEngine`. Call `drive()` directly."

```java
public class MainApp {
    public static void main(String[] args) {
        Car car = new Car();
        car.drive(); // no engine was ever set
    }
}
```

**Step 3 — say:** "Run it."

✅ **Expected output:** a `NullPointerException` on the `engine.start()` line.

> 💬 **Say, once they see the crash:** "This is the exact problem setter injection has — nothing forces you to call the setter. The object *looks* fine when it's created, but it's actually broken until you remember that extra step."

**Step 4 — say:** "Now fix it — call `car.setEngine(new PetrolEngine())` before `drive()`, and run it again to confirm it works."

---

### 2.3 Field Injection (Spring Style)

```java
class Car {
    Engine engine;
}
```

> 💬 **Say:** "Spring will inject this automatically — we'll see exactly how in Chapter 3."

**⚠️ Note:**
- Easiest to write
- Not recommended for testing (hard to mock/inject manually without a framework)

> 📌 **Instructor Note — say this honestly, don't skip it:** "Try this yourself: with just plain Java, no Spring, there's no clean way to get a value into `engine` from outside the class — you'd have to either add a setter (which defeats the point) or use reflection, which is way more advanced than we need right now. That's not a gap in your understanding — it's a real gap in plain Java. **This is exactly the gap Spring exists to fill**, and it's why field injection only really works once a framework like Spring is doing the wiring for you. We'll see that live next chapter."

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

#### 🛠️ Build-Along — Build a mini app with an interface + two implementations (explicit, step by step)

> 📌 **Instructor Note:** This mirrors Build-Along 3 from Chapter 1 almost exactly, but with a new domain (`Engine`/vehicles → generic interface pattern) reinforced through the `Engine` interface built fresh. If the cohort is moving fast, you can chunk Steps 1–2 together here.

**Step 1 — say:** "Create the interface, if you don't already have it from Chapter 1."

```java
// Engine.java
public interface Engine {
    void start();
}
```

**Step 2 — say:** "Create `ElectricEngine`, implementing `Engine`."

```java
// ElectricEngine.java
public class ElectricEngine implements Engine {
    @Override
    public void start() {
        System.out.println("Electric engine starting silently...");
    }
}
```

**Step 3 — say:** "Create `Car` with constructor injection, plus a `drive()` method."

```java
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
```

**Step 4 — say:** "In `MainApp`, create the engine, create the car, call `drive()`."

```java
// MainApp.java
public class MainApp {
    public static void main(String[] args) {
        Engine engine = new ElectricEngine();
        Car car = new Car(engine);
        car.drive();
    }
}
```

**Step 5 — say:** "Run it and confirm the output."

✅ **Expected output:**
```
Electric engine starting silently...
Car is driving.
```

> 💬 **Explain:** "This `main` method is acting like a mini Spring container — it creates the dependency and hands it to `Car`. That's manual DI. Next chapter, we replace `MainApp` with actual Spring."

---

## 5. How Spring Does It

```java
@Component
class Car {
    @Autowired
    Engine engine;
}
```

> ⚠️ **Preview only — do not have students type this yet.** This code needs a real Spring project (dependency, `@Configuration` class, `ApplicationContext`) to run, which we build together in **Chapter 3, Build-Along 1**. Dictate this as "here's what it will look like" and move on — don't let students sit with broken/non-running code in their editor.

> 💬 **Say:** "Spring removes the need for manual wiring. Next class, you'll build the actual container that makes this run — for real, on your machine."

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

#### 🛠️ Build-Along — Do the exercise live, don't just talk through it

> 📌 **Instructor Note:** Don't just discuss the answer — have every student actually type both versions and run them, same as Build-Along 2 in Chapter 1. This is a direct repetition of that exact skill in a new domain (`Phone`/`Battery`), which is the point — it should feel easy and fast if Chapter 1 landed.

**Step 1 — say:** "Create `Battery` with a `charge()` method that prints something, and `Phone` exactly as shown above, with a `call()` method that uses `battery`."

```java
class Battery {
    void charge() {
        System.out.println("Battery charging...");
    }
}

class Phone {
    Battery battery = new Battery();

    void call() {
        battery.charge();
        System.out.println("Phone is calling.");
    }
}
```

**Step 2 — say:** "Run it from a `main` method first, confirm it works, tightly coupled as-is."

**Step 3 — say:** "Now convert `Phone` to constructor injection, same pattern as `Car`. Update `main` to create the `Battery` and pass it in."

<details>
<summary>💡 Click to reveal the converted solution</summary>

```java
class Phone {
    Battery battery;

    Phone(Battery battery) {
        this.battery = battery;
    }

    void call() {
        battery.charge();
        System.out.println("Phone is calling.");
    }
}

public class MainApp {
    public static void main(String[] args) {
        Battery battery = new Battery();
        Phone phone = new Phone(battery);
        phone.call();
    }
}
```

This is **tight coupling → loose coupling**, the same conversion as Chapter 1, done independently this time.

</details>

**Step 4 — say:** "Run the converted version. Same output, different control — same lesson as last chapter."

---

## 9. Closing Statement

> 💬 **Say:** "Dependency Injection is what makes loose coupling practical. It allows objects to remain flexible and independent. Next class, we stop being the container ourselves — Spring takes over."

## ✅ Key Takeaways

- DI means receiving dependencies, not creating them
- There are three types: Constructor (safest), Setter (optional deps, but easy to forget and crash), Field (easiest, hardest to test, and literally can't be demonstrated without a framework)
- IoC is the *what* (giving up control); DI is the *how* (the technique)
- DI works in plain Java — Spring just automates it
- Constructor Injection is the generally recommended default

---

**← Previous: [Chapter 1 — Getting Started with Spring & Design Patterns](./chapter-1-getting-started-with-spring.md)** | **Next: Chapter 3 →**

---

## 🔧 What Changed in This Revision

> 🆕 **Not for dictation — for your reference only.**

- Added a build-along for Setter Injection that lets students *watch it actually crash* (`NullPointerException`) rather than just being told it's risky — this is a much stronger memory hook than the prose warning alone.
- Turned the honest limitation of Field Injection in plain Java into a teaching moment and explicit bridge to "why Spring," instead of a throwaway note.
- Marked the Manual DI section as a formal Build-Along, consistent with Chapter 1's format, and flagged it as a place you can start lightly chunking steps if the cohort is ready.
- Converted the Quick Exercise into a full Build-Along instead of a discussion-and-reveal, since it's the first fully independent repetition of the Chapter 1 skill — that repetition is where the concept actually locks in.
- Section 5 ("How Spring Does It") is explicitly marked preview-only with a clear signpost to where it becomes real (Chapter 3, Build-Along 1) — no code left dangling as "won't run" without a next step attached.
