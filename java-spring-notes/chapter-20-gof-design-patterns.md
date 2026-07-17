# Chapter 20: Introduction to GOF Design Patterns

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

This chapter shifts focus from Spring-specific tools to the *ideas* that underpin them. Spring didn't invent design patterns — it uses them heavily. Understanding even four of them will make a student a noticeably better developer.

## 🎯 Lesson Goal

Students should understand:

- What a design pattern is and why they exist
- The three GOF categories (Creational, Structural, Behavioral)
- Singleton Pattern
- Prototype Pattern
- Factory Pattern
- Strategy Pattern

> 📌 There are 23 GOF patterns in total. Don't try to teach all of them. These four are enough for now — and they're the ones students will encounter almost immediately in real Spring work.

## 🗺️ Lesson Roadmap

1. Start With the Problem
2. What Is a Design Pattern?
3. GOF Categories
4. Pattern 1: Singleton
5. Pattern 2: Prototype
6. Pattern 3: Factory
7. Pattern 4: Strategy
8. Design Patterns and Spring
9. What Students Must Remember
10. Mini Exercise
11. Closing Statement

---

## 1. Start With the Problem

> 💬 **Ask:** Imagine a junior developer says "I know Java, why do I need design patterns?"

Suppose every developer solves the same problem differently:

- Developer A → Solution A
- Developer B → Solution B
- Developer C → Solution C

The codebase becomes inconsistent and hard to maintain. Experienced developers eventually discover: "We've solved this problem hundreds of times before." So common solutions get named and documented — they become patterns.

---

## 2. What Is a Design Pattern?

**Definition:** A design pattern is a proven, reusable solution to a recurring software design problem.

Two things to notice:
- A pattern is **not code** — it's an idea
- A pattern is a *solution to a problem* — knowing the problem is more important than memorizing the solution

> 🧠 **Teaching line:** "A design pattern is a blueprint, not a finished building."

**Real-world analogy:** When building a staircase, architects already know how staircases should be built. Nobody reinvents stairs. Similarly, software developers don't reinvent solutions to common problems.

### GOF

GOF stands for **Gang of Four** — the four authors who wrote:

> 📖 *Design Patterns: Elements of Reusable Object-Oriented Software*

This book identified and named 23 patterns. It's why the patterns are called "GOF patterns."

---

## 3. GOF Categories

The 23 patterns are grouped into three categories:

| Category | Concern | Examples |
|---|---|---|
| **Creational** | How objects are created | Singleton, Factory, Prototype, Builder |
| **Structural** | How objects are organized | Adapter, Decorator, Facade, Proxy |
| **Behavioral** | How objects communicate | Strategy, Observer, Command, State |

> 🧠 **Teaching line:** "Creational creates, Structural organizes, Behavioral communicates."

> 📌 You don't need to know all 23 yet. You'll encounter them naturally as you build more complex systems. The four covered in this chapter will get you through most real-world Spring work.

---

## 4. Pattern 1: Singleton

### The Problem

```java
DatabaseConnection connection = new DatabaseConnection();
```

Suppose 100 requests each create their own `DatabaseConnection`. That's 100 open connections — expensive and unnecessary.

Sometimes, one shared instance for the entire application is exactly what's needed.

### Solution

**Singleton Pattern:** Ensures only one instance of a class exists.

```java
public class Logger {

    private static Logger instance = new Logger();

    private Logger() {} // ← private: prevents anyone from calling 'new Logger()' directly

    public static Logger getInstance() {
        return instance;
    }
}
```

**Usage:**
```java
Logger logger = Logger.getInstance();
```

> 📌 The constructor is `private` deliberately — it blocks every other class from creating a second instance with `new Logger()`. The only way in is through `getInstance()`, which always returns the same one.

**Complete runnable example:**
```java
public class Main {
    public static void main(String[] args) {
        Logger a = Logger.getInstance();
        Logger b = Logger.getInstance();

        System.out.println(a == b); // true — same object
    }
}
```

> 🧠 **Teaching line:** "Singleton guarantees a single shared object."

### Spring Connection

```java
@Component
public class StudentService {
}
```

By default, every Spring bean is **Singleton-scoped**. No matter how many times you call `context.getBean(StudentService.class)`, you get the same object back.

> 🧠 **Teaching line:** "Spring beans are Singleton by default."

---

## 5. Pattern 2: Prototype

### The Problem

Sometimes sharing is bad. Imagine a `ShoppingCart` — each user needs their own, completely independent cart.

### Solution

**Prototype Pattern:** Create a fresh object every time one is requested.

```java
public class ShoppingCart implements Cloneable {

    private List<String> items = new ArrayList<>();

    public void addItem(String item) {
        items.add(item);
    }

    public List<String> getItems() {
        return items;
    }

    @Override
    protected ShoppingCart clone() throws CloneNotSupportedException {
        return (ShoppingCart) super.clone();
    }
}
```

**Usage:**
```java
ShoppingCart cartA = new ShoppingCart();
cartA.addItem("Book");

ShoppingCart cartB = cartA.clone();
cartB.addItem("Pen");

System.out.println(cartA.getItems()); // [Book]
System.out.println(cartB.getItems()); // [Book, Pen] — independent copy
```

### Spring Connection

```java
@Component
@Scope("prototype")
public class ShoppingCart {
}
```

> 🧠 **Teaching line:** "Prototype creates a fresh object on demand."

### Singleton vs Prototype

| | Singleton | Prototype |
|---|---|---|
| Instances | One shared | New every time |
| Independence | Shared state | Independent state |
| Spring default? | ✅ Yes | ❌ Must specify |

---

## 6. Pattern 3: Factory

This is one of the most important patterns.

### The Problem

```java
CardPayment payment = new CardPayment();
```

What if tomorrow `TransferPayment` or `CryptoPayment` is needed? Every place that creates a payment object must change. The code fills up with `new`, `new`, `new` — scattered everywhere.

### Solution

**Factory Pattern:** Centralize object creation behind a single method.

```java
public interface Payment {
    void pay();
}

public class CardPayment implements Payment {
    public void pay() {
        System.out.println("Paying by card");
    }
}

public class TransferPayment implements Payment {
    public void pay() {
        System.out.println("Paying by transfer");
    }
}

public class PaymentFactory {

    public Payment create(String type) {
        if (type.equals("CARD")) return new CardPayment();
        if (type.equals("TRANSFER")) return new TransferPayment();

        throw new IllegalArgumentException("Unknown payment type: " + type);
    }
}
```

> 📌 The factory throws `IllegalArgumentException` for unknown types — never return `null`. A `null` return silently causes a `NullPointerException` somewhere later, which is much harder to debug than a clear error at creation time.

**Usage:**
```java
PaymentFactory factory = new PaymentFactory();
Payment payment = factory.create("CARD");
payment.pay(); // Paying by card
```

> 🧠 **Teaching line:** "Factories centralize object creation."

### Spring Connection

The Spring `ApplicationContext` itself is essentially a giant factory. When you call `context.getBean(...)`, Spring creates or retrieves the object for you — you never call `new` yourself.

---

## 7. Pattern 4: Strategy

Spend the most time here. Students usually understand it immediately — and it's the pattern they'll use most in real Spring work.

### The Problem

```java
if (type.equals("CARD")) { ... }
if (type.equals("TRANSFER")) { ... }
if (type.equals("PAYPAL")) { ... }
```

Every new payment method adds another `if`. This class keeps growing and is impossible to test properly.

### Solution

**Strategy Pattern:** Extract each behavior into its own class behind a common interface. Let the client work with the interface, not the implementation.

```java
public interface PaymentStrategy {
    void pay();
}

public class CardPayment implements PaymentStrategy {
    public void pay() {
        System.out.println("Paying by card");
    }
}

public class TransferPayment implements PaymentStrategy {
    public void pay() {
        System.out.println("Paying by transfer");
    }
}

public class PaypalPayment implements PaymentStrategy {
    public void pay() {
        System.out.println("Paying via PayPal");
    }
}
```

**Client — no `if` statements:**
```java
public class PaymentProcessor {

    private final PaymentStrategy strategy;

    public PaymentProcessor(PaymentStrategy strategy) {
        this.strategy = strategy;
    }

    public void processPayment() {
        strategy.pay();
    }
}
```

**Usage:**
```java
PaymentProcessor processor = new PaymentProcessor(new CardPayment());
processor.processPayment(); // Paying by card

processor = new PaymentProcessor(new PaypalPayment());
processor.processPayment(); // Paying via PayPal
```

To add a new payment method, you create a new class — you don't touch `PaymentProcessor` at all.

> 🧠 **Teaching line:** "Strategy allows behavior to change without changing the client."

### Spring Connection

This is where it all clicks together with what students already know.

```java
public interface NotificationService {
    void send(String message);
}

@Component
public class EmailNotificationService implements NotificationService {
    public void send(String message) {
        System.out.println("Email: " + message);
    }
}

@Component
public class SmsNotificationService implements NotificationService {
    public void send(String message) {
        System.out.println("SMS: " + message);
    }
}
```

```java
@Component
public class AlertSystem {

    private final NotificationService notificationService;

    public AlertSystem(@Qualifier("smsNotificationService") NotificationService notificationService) {
        this.notificationService = notificationService;
    }

    public void alert(String message) {
        notificationService.send(message);
    }
}
```

> 📌 This is exactly `@Qualifier` from Chapter 6 — but now you can see *why* it works that way. Spring is injecting a specific Strategy. Switching from SMS to Email is a one-word change in the `@Qualifier` string, not a rewrite of `AlertSystem`.

---

## 8. Design Patterns and Spring

| Spring Feature | Pattern |
|---|---|
| Bean scope (default) | Singleton |
| `@Scope("prototype")` | Prototype |
| `ApplicationContext` | Factory |
| Dependency Injection | Strategy-like behavior |
| AOP / Proxies | Proxy Pattern |

> 🧠 **Teaching line:** "Spring did not invent design patterns; Spring uses them heavily."

---

## 9. What Students Must Remember

Not the code. Not the definitions. **Remember the problems.**

| Problem | Pattern |
|---|---|
| Need one shared object | Singleton |
| Need fresh, independent objects | Prototype |
| Object creation becoming scattered | Factory |
| Too many `if`/`else` behaviors | Strategy |

---

## 10. Mini Exercise

Which pattern is being used in each example?

**Example A:**
```java
@Component
public class StudentService {
}
```

**Example B:**
```java
@Component
@Scope("prototype")
public class Cart {
}
```

**Example C:**
```java
context.getBean(StudentService.class);
```

**Example D:**
```java
public interface SortStrategy {
    void sort(int[] data);
}
```

<details>
<summary>💡 Click to reveal the answers</summary>

**A → Singleton** — default Spring bean scope; one shared instance exists.

**B → Prototype** — `@Scope("prototype")` means a new `Cart` is created every time one is requested.

**C → Factory** — `getBean()` is Spring's factory method; you ask for an object and Spring creates or retrieves it.

**D → Strategy** — defining a `SortStrategy` interface means any sorting algorithm (`BubbleSort`, `QuickSort`, etc.) can be swapped in without changing the code that calls `sort()`.

</details>

---

## ✅ Key Takeaways

- A design pattern is a proven solution to a recurring problem — it's an idea, not code
- **Creational** patterns control how objects are created; **Structural** how they're organized; **Behavioral** how they communicate
- **Singleton** — one shared instance; the Spring default
- **Prototype** — a fresh instance every time; `@Scope("prototype")` in Spring
- **Factory** — centralizes object creation; `ApplicationContext.getBean()` is Spring's factory
- **Strategy** — replaces `if`/`else` chains with interchangeable implementations behind an interface; the same thinking that makes `@Qualifier` injection work
- Spring didn't invent these patterns — it's built on top of them

---

**← Previous: Chapter 19** | **Next: Chapter 21 →**
