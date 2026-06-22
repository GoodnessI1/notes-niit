# Chapter 6: Advanced Dependency Injection

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

Chapter 4 covered injecting a single dependency. This chapter covers two follow-up problems: what happens when Spring finds *more than one* matching bean, and which injection style (field, setter, or constructor) is actually the professional standard — and why.

## 🎯 Lesson Goal

- Why constructor injection is preferred
- How to resolve multiple beans (`@Qualifier`, `@Primary`)
- Why field injection is discouraged

## 🗺️ Lesson Roadmap

1. The Problem (Multiple Beans)
2. Solution 1: `@Qualifier`
3. Solution 2: `@Primary`
4. Field Injection (What You've Been Using)
5. Constructor Injection (Best Practice)
6. Combining Concepts
7. Mini Exercise
8. Closing Statement

---

## 1. The Problem (Multiple Beans)

> 💬 **Ask:** "What if Spring finds TWO beans for the same type?"

**Example setup:**

```java
@Component
public class JavaCourse implements Course {
    public void study() {
        System.out.println("Studying Java");
    }
}

@Component
public class PythonCourse implements Course {
    public void study() {
        System.out.println("Studying Python");
    }
}

@Component
public class Student {

    @Autowired
    private Course course;

    public void work() {
        course.study();
    }
}
```

❌ **Error:** `NoUniqueBeanDefinitionException`

> 🧠 **Teaching line:** "Spring is confused — it found multiple options and doesn't know which one to inject."

---

## 2. Solution 1: `@Qualifier`

✅ Tells Spring exactly which bean to use.

```java
@Component
public class Student {

    @Autowired
    @Qualifier("javaCourse") // matches the default bean name for JavaCourse
    private Course course;

    public void work() {
        course.study();
    }
}
```

> 📌 By default, Spring names a bean after its class with the first letter lowercased — so `JavaCourse` becomes `javaCourse`. That's the string `@Qualifier` is matching against.

> 🧠 **Teaching line:** "`@Qualifier` removes ambiguity by naming the exact bean."

---

## 3. Solution 2: `@Primary`

✅ Sets a default bean to use when no qualifier is specified.

```java
@Component
@Primary
public class JavaCourse implements Course {
    // study() method omitted here for brevity — same implementation as Section 1
}
```

👉 Now Spring will choose `JavaCourse` automatically.

> 🧠 **Teaching line:** "`@Primary` says — 'if no one specifies, use me by default.'"

**When to use what:**
- Use `@Primary` when there's one clear default choice
- Use `@Qualifier` when a specific bean must be selected explicitly

---

## 4. Field Injection (What You've Been Using)

```java
@Autowired
private Course course;
```

❌ **Problems:**
1. **Hard to test** — you can't easily pass in a mock
2. **Hidden dependency** — not visible from outside the class
3. **Can cause null issues** if the object is created outside of Spring (e.g. with `new`)

> 🧠 **Teaching line:** "Field injection works, but it hides important details."

> ❓ **"Isn't a constructor dependency also hidden — it's still inside the class?"**
> Not in the same way. With field injection, the only way to know `Student` needs a `Course` is to open the source file and look for `@Autowired` fields — nothing in the constructor or public API says so. With constructor injection (next section), the dependency is a constructor *parameter* — visible the moment you try to create a `Student`, without reading a single line of the class body. "Hidden" here means hidden from the class's public contract, not literally hidden inside the file.

---

## 5. Constructor Injection (Best Practice) ⭐

✅ The professional way.

```java
@Component
public class Student {

    private Course course;

    @Autowired
    public Student(Course course) {
        this.course = course;
    }

    public void work() {
        course.study();
    }
}
```

**Even better** (Spring auto-detects):

```java
@Component
public class Student {

    private final Course course;

    public Student(Course course) {
        this.course = course;
    }

    public void work() {
        course.study();
    }
}
```

Since Spring 4.3+, if there's only one constructor, `@Autowired` is optional.

> 🧠 **Teaching line:** "If a class needs something to exist — it should be in the constructor."

**Why constructor injection is better:**

1. ✅ **Forces dependency** — the object literally cannot exist without it
   > ❓ **"How does it actually force it?"** This isn't Spring magic — it's plain Java. If `Student`'s only constructor takes a `Course`, then `new Student()` won't even compile:
   > ```java
   > Student student = new Student(); // ❌ Compile error — no matching constructor
   > ```
   > Removing the no-args constructor makes the dependency mandatory at the language level, before Spring even gets involved.
2. ✅ **Easier testing** — `Student s = new Student(new MockCourse());`
3. ✅ **Immutability** — `private final Course course;`
4. ✅ **Cleaner design** — dependencies are visible immediately, right in the constructor signature

> 🧠 **Teaching line:** "Constructor injection makes your design honest."

**Common student confusion:**

> ❓ "Do we still use `@Autowired`?"
> 👉 Optional for constructors (Spring 4.3+) — required for fields and setters.

---

## 6. Combining Concepts

```java
@Component
public class Student {

    private final Course course;

    public Student(@Qualifier("pythonCourse") Course course) {
        this.course = course;
    }

    public void work() {
        course.study();
    }
}
```

> 🧠 **Teaching line:** "Constructor injection + qualifier = precise and clean."

### Classroom Demo

> 💬 **Ask:** "Which of these classes is safer?"

**Option A (Field Injection):**
```java
@Component
public class Student {
    @Autowired
    Course course;
}
```

**Option B (Constructor Injection):**
```java
@Component
public class Student {
    private final Course course;

    public Student(Course course) {
        this.course = course;
    }
}
```

> 👉 Let them think… ✅ **Answer: B**

---

## 7. Mini Exercise

1. Create `MathCourse` and `PhysicsCourse`
2. Inject one into `Student` using `@Primary`
3. Then switch to `@Qualifier` to select the other

<details>
<summary>💡 Click to reveal a suggested solution</summary>

```java
@Component
@Primary
public class MathCourse implements Course {
    public void study() {
        System.out.println("Studying Math");
    }
}

@Component
public class PhysicsCourse implements Course {
    public void study() {
        System.out.println("Studying Physics");
    }
}

@Component
public class Student {

    private final Course course;

    public Student(Course course) {
        this.course = course;
    }

    public void work() {
        course.study();
    }
}
```

With `@Primary` on `MathCourse`, `work()` prints `Studying Math` with no extra configuration needed.

To switch to Physics instead, add a qualifier to the constructor parameter:

```java
public Student(@Qualifier("physicsCourse") Course course) {
    this.course = course;
}
```

Now `work()` prints `Studying Physics` — the qualifier overrides the default `@Primary` choice.

</details>

---

## ✅ Key Takeaways

- When Spring finds multiple beans of the same type, it throws `NoUniqueBeanDefinitionException`
- `@Qualifier` names the exact bean to use; `@Primary` sets a default when none is specified
- Field injection is easy to write but hides dependencies and is hard to test
- Constructor injection is the recommended default — dependencies are visible, immutable, and enforced by the compiler itself
- `@Autowired` is optional on a single constructor (Spring 4.3+), but still required for field and setter injection

> 🧠 "Constructor injection makes dependencies clear, safe, and testable."

---

**← Previous: [Chapter 5 — Bean Scope & Lifecycle](./chapter-5-bean-scope-and-lifecycle.md)** | **Next: Chapter 7 →**
