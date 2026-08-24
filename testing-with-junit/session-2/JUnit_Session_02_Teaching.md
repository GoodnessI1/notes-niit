# JUnit — Session 2
## From a Test Method to a JUnit Test Class

### Session focus

**Core idea:** A JUnit test is not just `@Test`. Students should understand the basic structure of a JUnit test class, how JUnit identifies test methods, how multiple tests are organized, and the most useful JUnit 4 → JUnit 5 distinctions.

---

## 1. Start from what we already know

Last session:

```text
Production code
      ↓
Behaviour we want to verify
      ↓
Test method
      ↓
Assertion
      ↓
JUnit reports the result
```

Today we zoom out.

Instead of looking at one test method, we ask:

> **Where do tests live, and how does JUnit know which methods to execute as tests?**

---

## 2. A test class

```java
class CalculatorTest {

    @Test
    void shouldAddNumbers() {
        Calculator calculator = new Calculator();

        int result = calculator.add(2, 3);

        assertEquals(5, result);
    }

    @Test
    void shouldSubtractNumbers() {
        Calculator calculator = new Calculator();

        int result = calculator.subtract(5, 2);

        assertEquals(3, result);
    }
}
```

There are two levels here:

**Test class**

```java
CalculatorTest
```

It groups related tests.

**Test methods**

```java
shouldAddNumbers()
shouldSubtractNumbers()
```

Each method represents an individual test.

**Key distinction:**

> A **test class** is a container/grouping of tests. A **test method** is an individual test.

JUnit Jupiter recognizes test methods through annotations such as `@Test`. Test classes can contain test methods. citeturn0search1

---

## 3. Why have multiple test methods?

Suppose `Calculator` has:

```java
add()
subtract()
multiply()
divide()
```

We could put everything into:

```java
@Test
void testEverything() {
    // test add
    // test subtract
    // test multiply
    // test divide
}
```

That is poor test organization.

Instead:

```java
@Test
void shouldAddNumbers() { ... }

@Test
void shouldSubtractNumbers() { ... }

@Test
void shouldMultiplyNumbers() { ... }

@Test
void shouldDivideNumbers() { ... }
```

Each test communicates a more specific piece of behaviour.

### Principle

> **A test should have a clear reason to pass or fail.**

Focused tests generally make failures easier to understand.

---

## 4. What makes a method a JUnit test?

Compare:

```java
void addNumbers() {
    // test code
}
```

with:

```java
@Test
void addNumbers() {
    // test code
}
```

The second one is recognized as a JUnit test method because of:

```java
@Test
```

The method name itself does not make it a test.

This:

```java
void testAddNumbers()
```

does **not** become a JUnit test merely because its name begins with `test`.

JUnit Jupiter identifies test methods using test annotations; `@Test` is the basic one. citeturn0search1

---

## 5. Test method naming

Compare:

```java
@Test
void test1() {
    ...
}
```

with:

```java
@Test
void shouldReturnFiveWhenAddingTwoAndThree() {
    ...
}
```

Both can be valid tests.

But the second communicates intent.

A useful test name should help answer:

> **What behaviour is this test checking?**

Examples:

```java
shouldAddTwoNumbers()
returnsTrueForAdultAge()
shouldRejectInvalidPassword()
```

The exact naming convention matters less than clarity and consistency.

---

## 6. A test class is still ordinary Java

A JUnit test class is a Java class:

```java
class CalculatorTest {
    ...
}
```

It contains methods, fields, constructors, and ordinary Java statements.

JUnit adds testing-specific meaning through annotations and testing APIs.

So students should **not** create the mental model that JUnit is a separate programming language.

It is Java code using a testing framework.

---

## 7. JUnit 4 and JUnit 5: why are there different annotations?

Older material may contain:

```java
import org.junit.Test;
```

while JUnit Jupiter code commonly uses:

```java
import org.junit.jupiter.api.Test;
```

Both can appear as:

```java
@Test
```

The important distinction is the **import**.

```java
org.junit.Test
```

→ JUnit 4

```java
org.junit.jupiter.api.Test
```

→ JUnit Jupiter / JUnit 5

### Exam habit

> **When comparing JUnit examples, check the import, not just `@Test`.**

---

## 8. The useful 80/20 of JUnit 4 → JUnit 5

Do not spend time memorizing JUnit history.

Know these useful differences:

| JUnit 4 | JUnit 5 / Jupiter |
|---|---|
| `org.junit.Test` | `org.junit.jupiter.api.Test` |
| `@Before` | `@BeforeEach` |
| `@After` | `@AfterEach` |
| `@BeforeClass` | `@BeforeAll` |
| `@AfterClass` | `@AfterAll` |
| Test methods commonly written `public` | Test methods need not be `public` |

JUnit Jupiter's test methods do not have to be `public`; they must not be `private`. citeturn0search1turn0search2

Do **not** turn this table into a memorization exercise yet.

The important question is:

> **What problem are the lifecycle annotations solving?**

That becomes the next layer of the course.

---

## 9. Compare a JUnit 4 and JUnit 5 test

### JUnit 4 style

```java
import org.junit.Test;
import static org.junit.Assert.assertEquals;

public class CalculatorTest {

    @Test
    public void shouldAddNumbers() {
        Calculator calculator = new Calculator();

        int result = calculator.add(2, 3);

        assertEquals(5, result);
    }
}
```

### JUnit 5 / Jupiter style

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;

class CalculatorTest {

    @Test
    void shouldAddNumbers() {
        Calculator calculator = new Calculator();

        int result = calculator.add(2, 3);

        assertEquals(5, result);
    }
}
```

Notice:

```text
Same Java idea
      ↓
Different JUnit APIs/imports
      ↓
Some different conventions and annotations
```

Do not teach every difference here.

The objective is recognition and orientation.

---

## 10. Multiple tests and independence

Suppose:

```java
class CalculatorTest {

    @Test
    void testOne() {
        ...
    }

    @Test
    void testTwo() {
        ...
    }
}
```

A beginner may assume:

> "JUnit runs `testOne()` and then `testTwo()`, so `testTwo()` can depend on what `testOne()` did."

That is a dangerous assumption.

JUnit Jupiter's default test-instance lifecycle creates a new test-class instance for each test method, helping isolate tests from mutable instance state. citeturn0search1

The practical principle is:

> **A test should not depend on another test having run first.**

We will build the lifecycle explanation properly later.

---

## 11. What students should understand

### Test class

A Java class used to group related tests.

### Test method

A method that JUnit recognizes as a test because of a test annotation such as `@Test`.

### `@Test`

The basic annotation used to identify a JUnit test method.

### Import

The imported annotation/API helps identify which JUnit API is being used.

### Test independence

A test should be designed so its result does not depend on another test running first.

---

## 12. Practical work

### Exercise 1 — Build a test class

Given:

```java
public class Calculator {

    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }
}
```

Create:

```java
CalculatorTest
```

with:

```text
shouldAddNumbers
shouldSubtractNumbers
```

Each test should contain:

```text
Arrange
Act
Assert
```

---

### Exercise 2 — Spot the problem

Is this a good test organization?

```java
class CalculatorTest {

    @Test
    void testEverything() {
        // test add
        // test subtract
        // test multiply
        // test divide
    }
}
```

**Expected reasoning:**

It can execute, but it is poorly focused. Separate behaviours generally give clearer failures and clearer intent.

---

### Exercise 3 — Identify the JUnit API

A:

```java
import org.junit.Test;
```

B:

```java
import org.junit.jupiter.api.Test;
```

**Answer:**

A → JUnit 4

B → JUnit Jupiter / JUnit 5

---

### Exercise 4 — Fix the test

```java
import org.junit.jupiter.api.Test;

class CalculatorTest {

    void shouldAddNumbers() {
        Calculator calculator = new Calculator();

        int result = calculator.add(2, 3);

        assertEquals(5, result);
    }
}
```

What is missing?

**Answer:**

The method needs:

```java
@Test
```

so JUnit recognizes it as a test method.

---

## 13. One important warning

Do not teach students:

> "JUnit always runs tests in the order they appear."

That is not a safe mental model.

Tests should be independent of execution order.

If one test must run before another for the second test to work, the test design has a problem.

Later, when we discuss lifecycle and execution ordering, students will learn the mechanisms involved.

---

## 14. Session takeaway

Students should leave with this mental model:

```text
JUnit test class
      │
      ├── Test method
      │      ├── Arrange
      │      ├── Act
      │      └── Assert
      │
      ├── Test method
      │      ├── Arrange
      │      ├── Act
      │      └── Assert
      │
      └── Test method
```

And they should be able to distinguish:

```text
Test class
    ≠
Test method
    ≠
Assertion
    ≠
Production code
```

Also:

```text
org.junit.Test
        ≠
org.junit.jupiter.api.Test
```

---

## Teacher boundary

Do **not** spend the session explaining every difference between JUnit 4 and JUnit 5.

The purpose here is recognition and orientation.

The deeper JUnit 5 lifecycle model comes next.
