# Chapter 21: Aspect-Oriented Programming (AOP)

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

So far, everything added to a method had to be written *inside* that method. AOP is a technique that lets you attach behavior to methods *from outside* — without modifying the method at all. It sounds abstract at first, but Spring already uses it constantly under the hood.

## 🎯 Lesson Goal

Students should understand:

- What AOP is and why it exists
- Cross-cutting concerns
- The core terminology: Aspect, Join Point, Pointcut, Advice
- `@Before`, `@After`, and `@Around`
- How Spring implements AOP using proxies

## 🧰 Quick Setup

> 📌 AOP requires `spring-boot-starter-aop`. Add it to `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

Without this, `@Aspect` annotations are silently ignored — no error, just no AOP.

## 🗺️ Lesson Roadmap

1. Start With the Problem
2. What Is AOP?
3. Cross-Cutting Concerns
4. The Analogy (Read Before the Definitions)
5. Core AOP Terminology
6. Before Advice
7. After Advice
8. Around Advice
9. Complete Runnable Example
10. How Spring Implements AOP (Proxies)
11. Real Spring Features Built on AOP
12. Mini Exercise
13. Closing Statement

---

## 1. Start With the Problem

```java
@Service
public class StudentService {

    public Student createStudent() {
        System.out.println("Method Started");
        Student student = new Student();
        System.out.println("Method Finished");
        return student;
    }

    public void deleteStudent() {
        System.out.println("Method Started");
        // logic
        System.out.println("Method Finished");
    }

    public void updateStudent() {
        System.out.println("Method Started");
        // logic
        System.out.println("Method Finished");
    }
}
```

Every method carries the same logging lines. Now add security checks and performance tracking on top of that — the business logic drowns in bookkeeping.

> 🧠 **Teaching line:** "Business code should focus on business problems, not logging and bookkeeping."

---

## 2. What Is AOP?

**Definition:** AOP (Aspect-Oriented Programming) is a technique that allows us to add behavior to existing code *without modifying the code itself*.

Instead of `createStudent()` containing logging — we *attach* logging from outside.

> 🧠 **Teaching line:** "AOP lets us inject behavior around a method."

---

## 3. Cross-Cutting Concerns

The repeated code above — logging, security checks, transaction management, performance monitoring, auditing — is called a **cross-cutting concern**.

Why "cross-cutting"? Because it cuts across many classes:

```
StudentService  →  Logging
BookService     →  Logging
UserService     →  Logging
```

The concern is the same everywhere. AOP handles it once, centrally.

> 🧠 **Teaching line:** "Cross-cutting concerns appear everywhere in an application."

---

## 4. The Analogy (Read Before the Definitions)

> 💬 **Start here before showing any terminology.** The definitions land much better once students have this image.

Imagine a door. Someone enters.

You want:
- **Before entering** → check their ID
- **After entering** → log the entry

The door doesn't know about the ID check or the log. Those are added *around* it, from outside.

That's AOP.

---

## 5. Core AOP Terminology

### Join Point

A point during program execution where AOP can intervene — typically a method call.

Examples: `createStudent()`, `deleteStudent()`

> 🧠 **Teaching line:** "A join point is a place where extra behavior can be attached."

### Pointcut

A rule that selects which join points to target.

Example: "all methods in `StudentService`"

> 🧠 **Teaching line:** "Pointcuts choose where the aspect should run."

### Advice

The actual code that runs at a selected join point.

Example: `System.out.println("Started");`

> 🧠 **Teaching line:** "Advice is the action performed by an aspect."

### Aspect

A class that contains AOP logic — a container for advice.

```java
@Aspect
@Component
public class LoggingAspect {
    // advice goes here
}
```

---

## 6. Before Advice

Runs **before** the method executes.

```java
@Before("execution(* org.codex.service.*.*(..))")
public void beforeMethod() {
    System.out.println("Method Started");
}
```

```
Before Advice runs
      ↓
Method runs
```

### Reading the Pointcut Expression

`execution(* org.codex.service.*.*(..))` looks intimidating — here's what each part means:

| Part | Meaning |
|---|---|
| `execution(...)` | Match a method execution |
| `*` (first) | Any return type |
| `org.codex.service` | This package |
| `*` (second) | Any class inside that package |
| `*` (third) | Any method on that class |
| `(..)` | Any number and type of parameters |

In plain English: "run this advice on every method, in every class, in the `org.codex.service` package."

---

## 7. After Advice

Runs **after** the method executes — whether it succeeded or threw an exception.

```java
@After("execution(* org.codex.service.*.*(..))")
public void afterMethod() {
    System.out.println("Method Finished");
}
```

```
Method runs
      ↓
After Advice runs
```

---

## 8. Around Advice

The most powerful. Wraps the method — runs code before **and** after.

```java
@Around("execution(* org.codex.service.*.*(..))")
public Object measureTime(ProceedingJoinPoint joinPoint) throws Throwable {

    long start = System.currentTimeMillis();

    Object result = joinPoint.proceed(); // ← THIS runs the actual method

    long end = System.currentTimeMillis();

    System.out.println("Execution time: " + (end - start) + "ms");

    return result;
}
```

```
Around Advice (before)
        ↓
  Method runs  ← joinPoint.proceed()
        ↓
Around Advice (after)
```

> ⚠️ **Critical:** `joinPoint.proceed()` is what actually calls the real method. If you forget to call it, the method **never runs** — Spring just skips it silently. Always call `proceed()` and return its result, or you'll have very confusing bugs.

> 🧠 **Teaching line:** "Around advice wraps the method like a sandwich."

---

## 9. Complete Runnable Example

Here's a full, working `LoggingAspect` that logs every method in `org.codex.service`:

```java
package org.codex.aspect;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.After;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* org.codex.service.*.*(..))")
    public void beforeMethod() {
        System.out.println("[LOG] Method starting...");
    }

    @After("execution(* org.codex.service.*.*(..))")
    public void afterMethod() {
        System.out.println("[LOG] Method finished.");
    }

    @Around("execution(* org.codex.service.*.*(..))")
    public Object measureTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();

        Object result = joinPoint.proceed();

        long end = System.currentTimeMillis();
        System.out.println("[LOG] Execution time: " + (end - start) + "ms");

        return result;
    }
}
```

Place this in `src/main/java/org/codex/aspect/`. No other changes needed — Spring detects `@Aspect` and activates it automatically because `spring-boot-starter-aop` is on the classpath.

Call any method in `StudentService` and the log lines will appear automatically without `StudentService` knowing anything about it.

---

## 10. How Spring Implements AOP (Proxies)

> 🧠 **This is the secret.**

Instead of:

```
Client → StudentService
```

Spring secretly creates a **Proxy object**:

```
Client → Proxy → StudentService
```

The proxy intercepts the call, runs the advice, then delegates to the real `StudentService`. The client never knows the proxy exists.

> 🧠 **Teaching line:** "The proxy is Spring's undercover agent."

> 💬 Students remember this one. 😄

---

## 11. Real Spring Features Built on AOP

When students see this list, AOP stops being abstract.

| Spring Annotation | Uses AOP |
|---|---|
| `@Transactional` | Wraps method in a database transaction |
| `@PreAuthorize` | Checks security before method runs |
| `@Cacheable` | Skips method if result is already cached |

> 🧠 **Teaching line:** "Many Spring annotations work because of AOP."

Every time you've used `@Transactional`, you were using AOP without knowing it.

---

## 12. Mini Exercise

1. Create a `LoggingAspect` in `org.codex.aspect`
2. Add a `@Before` advice that prints the method name before it runs
3. Apply it to all methods in `org.codex.service`
4. Call `StudentService.getStudents()` and confirm the log prints without touching `StudentService`

> 📌 Hint: `joinPoint.getSignature().getName()` gives you the method name inside advice.

<details>
<summary>💡 Click to reveal a suggested solution</summary>

```java
package org.codex.aspect;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* org.codex.service.*.*(..))")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("[LOG] About to run: " + joinPoint.getSignature().getName());
    }
}
```

Calling `studentService.getStudents()` now prints:

```
[LOG] About to run: getStudents
```

...automatically, with zero changes to `StudentService`.

</details>

---

## ✅ Key Takeaways

- AOP adds behavior to methods from *outside* the method — the method itself is never modified
- Repeated concerns like logging, security, and performance tracking are called **cross-cutting concerns**
- **Join Point** = where AOP can intervene (a method); **Pointcut** = the rule selecting which join points; **Advice** = the code that runs; **Aspect** = the class holding the advice
- `@Before` runs before the method; `@After` runs after; `@Around` wraps both sides
- In `@Around`, always call `joinPoint.proceed()` and return its result — forgetting it silently skips the real method
- Spring implements AOP using **proxy objects** that intercept calls transparently
- `@Transactional`, `@PreAuthorize`, and `@Cacheable` are all powered by AOP

---

**← Previous: [Chapter 20 — GOF Design Patterns](./chapter-20-gof-design-patterns.md)**
