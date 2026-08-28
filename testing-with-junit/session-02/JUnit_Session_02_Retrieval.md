# JUnit Retrieval — Session 2
## Test Classes, Test Methods & JUnit 4/5 Distinctions

> Ask first. Reveal the answer after the student commits.

### 1. What is the difference between a test class and a test method?

**Answer:** A test class groups related tests. A test method represents an individual test.

### 2. What makes a method a JUnit test method?

**Answer:** A JUnit test annotation such as `@Test` identifies it as a test method.

### 3. Does naming a method `testAddNumbers()` automatically make it a JUnit test?

**Answer:** No. The method must be recognized by JUnit through an appropriate test annotation.

### 4. Which import indicates the JUnit 4 `@Test` annotation?

A. `org.junit.jupiter.api.Test`  
B. `org.junit.Test`

**Answer:** B.

### 5. Which import indicates the JUnit Jupiter `@Test` annotation?

A. `org.junit.jupiter.api.Test`  
B. `org.junit.Test`

**Answer:** A.

### 6. Why is looking at the import useful when identifying the JUnit version?

**Answer:** Because `@Test` appears in both examples. The imported type tells you which JUnit API the annotation belongs to.

### 7. Why should related behaviours generally be separated into focused test methods?

**Answer:** Focused tests make intent clearer and failures easier to interpret.

### 8. Can one test safely depend on another test having executed first?

**Answer:** No. Tests should be independent of one another and should not rely on execution order.

### 9. Compare:

```java
import org.junit.Test;
```

and:

```java
import org.junit.jupiter.api.Test;
```

**Answer:** The first is JUnit 4's `@Test`; the second is the JUnit Jupiter API used by JUnit 5.

### 10. What is the purpose of a test class?

**Answer:** To organize/group related tests in a Java class.

### 11. In JUnit Jupiter, are test methods required to be `public`?

**Answer:** No. They do not have to be `public`, but they must not be `private`. citeturn0search1turn0search2

### 12. What is the default JUnit Jupiter test-instance lifecycle?

**Answer:** A new test-class instance is created for each test method by default, helping isolate tests from mutable instance state. citeturn0search1

---

# Distinction Drill

### 13. Test class or test method?

```java
class UserServiceTest {
    ...
}
```

**Answer:** Test class.

### 14. Test class or test method?

```java
@Test
void shouldCreateUser() {
    ...
}
```

**Answer:** Test method.

### 15. Correct this statement:

> "`@Test` is the test."

**Answer:** `@Test` is the annotation that identifies a method as a test method. The test is the method and its test logic.

### 16. What is the difference between the purpose of a test name and `@Test`?

**Answer:** `@Test` tells JUnit to recognize the method as a test. The method name communicates the intended behaviour to the human reader.

---

# Code Diagnosis

### 17. What is wrong?

```java
class CalculatorTest {

    void shouldAddNumbers() {
        Calculator calculator = new Calculator();

        int result = calculator.add(2, 3);

        assertEquals(5, result);
    }
}
```

**Answer:** The method lacks `@Test`, so JUnit will not recognize it as a normal test method.

### 18. What is wrong with this assumption?

> "Because both examples contain `@Test`, they must use exactly the same JUnit API."

**Answer:** The import matters. `org.junit.Test` and `org.junit.jupiter.api.Test` belong to different JUnit APIs/generations.

### 19. A test passes only if another test has already run. What does this suggest?

**Answer:** The tests are improperly coupled. A well-designed test should not depend on another test's execution or side effects.

---

# Exam-Style Questions

### 20. Which statement is most accurate?

A. A test class and a test method are the same thing.  
B. A test class groups tests, while a test method represents an individual test.  
C. `@Test` is a test class.  
D. An assertion is a test class.

**Answer:** B.

### 21. Which pair represents JUnit 4 and JUnit 5/Jupiter respectively?

A. `org.junit.jupiter.api.Test` / `org.junit.Test`  
B. `org.junit.Test` / `org.junit.jupiter.api.Test`

**Answer:** B.

### 22. Which statement is safest?

A. JUnit tests always execute in source-code order.  
B. A test should depend on the previous test's state.  
C. Tests should be designed to be independent of execution order.  
D. Test methods must always be `public`.

**Answer:** C.

### 23. A student says:

> "`@Test` is the test."

What is the best correction?

**Answer:** `@Test` is the annotation that tells JUnit to recognize the method as a test. The test is the test method and its testing logic.

### 24. A student sees:

```java
import org.junit.jupiter.api.Test;
```

What should they recognize?

**Answer:** This is the JUnit Jupiter API used by JUnit 5.

---

# Delayed Retrieval

### 25. Draw the structure of a JUnit test class.

**Answer:**

```text
Test class
   │
   ├── Test method
   │      ├── Arrange
   │      ├── Act
   │      └── Assert
   │
   ├── Test method
   │
   └── Test method
```

### 26. Explain the difference between:

```text
Test class
Test method
@Test
Assertion
```

**Answer:**

- **Test class:** groups related tests.
- **Test method:** an individual test.
- **`@Test`:** identifies the method as a test for JUnit.
- **Assertion:** checks whether an expected condition/result is satisfied.

### 27. What should you immediately check when you see `@Test` and need to identify the JUnit API?

**Answer:** Check the import:

```java
org.junit.Test
```

versus:

```java
org.junit.jupiter.api.Test
```

### 28. Why should tests generally be independent?

**Answer:** A test should provide reliable information about the behaviour it checks. If its result depends on another test's execution or state, the test suite becomes fragile and failures become harder to interpret.
