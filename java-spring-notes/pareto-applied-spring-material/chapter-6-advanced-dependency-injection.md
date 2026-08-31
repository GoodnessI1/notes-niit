# Chapter 6: Advanced Dependency Injection

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. This file is your delivery script: you dictate/describe, students write notes and build the code live as you guide them.

> 🆕 **Revision note (not for dictation):** Same project, same rig from Chapter 3 — no new setup. Scaffolding continues to fade: build-alongs here are chunked in larger pieces than Chapter 5, and the Mini Exercise is given as a spec for students to solve mostly independently, matching where they should be six chapters in.

Chapter 4 covered injecting a single dependency. This chapter covers two follow-up problems: what happens when Spring finds *more than one* matching bean, and which injection style (field, setter, or constructor) is actually the professional standard — and why.

## 🎯 Lesson Goal

Students should understand — and have run code demonstrating:

- Why constructor injection is preferred
- How to resolve multiple beans (`@Qualifier`, `@Primary`)
- Why field injection is discouraged

## 🗺️ Lesson Roadmap

1. The Problem (Multiple Beans) — **build-along**
2. Solution 1: `@Qualifier` — **build-along**
3. Solution 2: `@Primary` — **build-along**
4. Field Injection (What You've Been Using)
5. Constructor Injection (Best Practice) — **build-along**
6. Combining Concepts
7. Mini Exercise — **build-along, mostly independent**
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

#### 🛠️ Build-Along — Cause the error on purpose (chunked)

> 📌 **Instructor Note:** Same project as always. Give this as one instruction; students should be comfortable enough to type all three classes without step-by-step hand-holding.

**Say:** "In your existing project, create an interface `Course` with a `study()` method. Create `JavaCourse` and `PythonCourse`, both `@Component`, both implementing `Course`, each printing a different subject when `study()` is called. Then create (or update) `Student` with an `@Autowired` field of type `Course` and a `work()` method that calls `course.study()`. Run `MainApp`, fetching a `Student` bean and calling `work()`."

✅ **Expected result:** the app fails to start, with `NoUniqueBeanDefinitionException` in the stack trace.

> 💬 **Say, once they see it:** "Read the exception message itself — Spring is telling you exactly what's wrong: it found two beans of type `Course` and has no way to choose. This is a real error you will see again in your own projects."

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

#### 🛠️ Build-Along — Fix the error with `@Qualifier`

**Say:** "Add `@Qualifier(\"javaCourse\")` above the `@Autowired` field in `Student`. Run it again."

✅ **Expected output:**
```
Studying Java
```

**Then say:** "Now change the qualifier string to `\"pythonCourse\"` and run it once more, without touching anything else."

✅ **Expected output:**
```
Studying Python
```

> 💬 **Say:** "One string changed which implementation gets used — nothing else in the class changed."

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

#### 🛠️ Build-Along — Compare `@Primary` against `@Qualifier`

**Say:** "Remove `@Qualifier` from `Student` entirely. Add `@Primary` above the `JavaCourse` class. Run it — predict the output first."

✅ **Expected output:**
```
Studying Java
```

> 💬 **Say:** "No qualifier needed this time — Spring picked `JavaCourse` automatically because it's marked as the default. If you add `@Qualifier(\"pythonCourse\")` back to `Student` now, it overrides `@Primary` — try it and confirm."

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

#### 🛠️ Build-Along — Feel the testability difference directly

> 📌 **Instructor Note:** This is the strongest argument for constructor injection, but it's abstract until students try it themselves. Give this as one combined instruction.

**Say:** "Convert `Student` to constructor injection, exactly as shown above, using `@Qualifier` in the constructor parameter to keep picking `JavaCourse`. Then, in `MainApp`, *without going through the Spring container at all*, write `Student testStudent = new Student(new PythonCourse());` and call `testStudent.work()`."

✅ **Expected output:**
```
Studying Python
```

> 💬 **Say, once it runs:** "You just built a fully working `Student` with a completely different `Course` — no Spring, no container, no annotations doing anything at that moment. That's what 'easier testing' actually means: constructor injection lets you hand-build the object with exactly what you want, any time you like. Try doing that with field injection — you'll find there's no clean way in."

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

#### 🛠️ Build-Along — Solve it mostly independently

> 📌 **Instructor Note:** By now, give the numbered spec above and let students work through it with minimal intervention — this should feel routine after five build-alongs of this exact shape (multiple implementations of an interface, resolved via `@Primary` or `@Qualifier`). Circulate and help only where genuinely stuck; don't dictate steps this time.

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
- Constructor injection lets you build an object entirely outside the Spring container with `new`, handing it whatever dependency you want — this is exactly what makes it easy to test

> 🧠 "Constructor injection makes dependencies clear, safe, and testable."

---

**← Previous: [Chapter 5 — Bean Scope & Lifecycle](./chapter-5-bean-scope-and-lifecycle.md)** | **Next: Chapter 7 →**

---

## 🔧 What Changed in This Revision

> 🆕 **Not for dictation — for your reference only.**

- Added a build-along that deliberately triggers `NoUniqueBeanDefinitionException`, then fixes it two different ways (`@Qualifier`, then `@Primary`) — students read a real stack trace and connect it to the concept, rather than being told the error exists.
- Added a build-along that has students construct a `Student` entirely outside the Spring container (`new Student(new PythonCourse())`) to make "easier testing" concrete instead of asserted — this is the strongest argument for constructor injection in the whole batch, and previously it was only stated in prose.
- Continued the scaffolding fade: the Mini Exercise is now explicitly framed as mostly independent work, with instructor intervention only as needed — appropriate six chapters into the fade you described.
