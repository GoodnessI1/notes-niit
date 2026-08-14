# JUnit Retrieval — Session 1
## Testing Fundamentals, Unit Testing & JUnit

**Purpose:** Repeated retrieval, discrimination, misconception correction, and exam preparation.

> **How to use this:** Ask the question first and require the student to answer before revealing the answer immediately underneath. The answers are included directly with each question so you can use this as a teaching/review document without switching to a separate answer key.

---

# Round 1 — Core Retrieval

### 1. What problem is software testing trying to solve?

**Answer:**  
Software testing seeks evidence about whether software behaves as expected under specified conditions. It gives us a repeatable way to check software behaviour and detect problems.

---

### 2. What is a unit test?

**Answer:**  
A unit test checks a relatively small, isolated unit of software behaviour. In Java, this will often involve testing a method or small class/component.

---

### 3. What is JUnit's role?

**Answer:**  
JUnit is a Java testing framework that provides mechanisms for defining, executing, and evaluating tests.

**Key distinction:** JUnit is not the application being tested. It provides the testing infrastructure.

---

### 4. What is the difference between production code and test code?

**Answer:**  
Production code implements the application's behaviour. Test code expresses repeatable checks of the expected behaviour of that production code.

---

### 5. What does `@Test` indicate?

**Answer:**  
`@Test` tells JUnit that the annotated method represents a test method.

---

### 6. What is the purpose of an assertion?

**Answer:**  
An assertion checks whether an expected condition or value matches what was actually observed when the code executed.

---

### 7. What is an expected value?

**Answer:**  
The expected value is what the test says should happen.

Example:

```java
assertEquals(5, result);
```

Here, `5` is the expected value.

---

### 8. What is an actual value?

**Answer:**  
The actual value is what the code actually produced.

Example:

```java
int result = calculator.add(2, 3);
assertEquals(5, result);
```

Here, `result` represents the actual value.

---

### 9. What does Arrange → Act → Assert mean?

**Answer:**

- **Arrange:** Prepare the objects, inputs, and conditions required by the test.
- **Act:** Execute the behaviour being tested.
- **Assert:** Check whether the observed result satisfies the expectation.

Example:

```java
// Arrange
Calculator calculator = new Calculator();

// Act
int result = calculator.add(2, 3);

// Assert
assertEquals(5, result);
```

---

### 10. Does a passing test prove that the entire application has no bugs?

**Answer:**  
No.

A passing test only establishes that the tested behaviour satisfied the specified expectation under the particular condition that was tested.

Other inputs, paths, behaviours, or defects may remain untested.

---

# Round 2 — Distinction Training

### 11. Running vs Testing

A developer writes:

```java
System.out.println(calculator.add(2, 3));
```

The console displays:

```text
5
```

Has the developer performed a test?

**Answer:**  
Not necessarily.

The developer has executed the code and observed its output. A test goes further by explicitly expressing expected behaviour and evaluating the observed result against that expectation.

**Distinction:**

> Running asks: **"What happens?"**

> Testing asks: **"Does what happened match what we expect?"**

---

### 12. Expected vs Actual

Given:

```java
int result = calculator.add(2, 3);

assertEquals(5, result);
```

Identify:

- Expected:
- Actual:

**Answer:**

- **Expected:** `5`
- **Actual:** `result`, whose runtime value is produced by `calculator.add(2, 3)`.

---

### 13. Test vs Assertion

Are these two statements equivalent?

> "The assertion is the test."

**Answer:**  
No.

A **test** is the broader test case that establishes a situation, performs an action, and evaluates behaviour.

An **assertion** is a mechanism inside the test that checks whether an expected condition or result is satisfied.

Think:

```text
Test
 ├── Arrange
 ├── Act
 └── Assert
       └── assertion
```

---

### 14. JUnit vs Unit Testing

Which statement is more accurate?

A. JUnit and unit testing are the same thing.

B. Unit testing is a testing practice/approach, while JUnit is a Java testing framework used to implement and execute tests.

C. JUnit is the application being tested.

D. Unit testing is a JUnit annotation.

**Answer: B**

Unit testing is a testing approach/practice.

JUnit is a Java testing framework used to implement and execute tests.

---

### 15. Passing Test

A student says:

> "My test passed, so my method is correct."

What is wrong with this statement?

**Answer:**  
The conclusion is too broad.

The test only demonstrates that the method satisfied the expectation for the particular condition/input tested. Other cases may still fail, and the test itself could even contain an incorrect expectation.

---

# Round 3 — Code Interpretation

### 16. Identify Arrange, Act, and Assert

```java
@Test
void shouldAddNumbers() {
    Calculator calculator = new Calculator();

    int result = calculator.add(4, 6);

    assertEquals(10, result);
}
```

**Answer:**

**Arrange:**

```java
Calculator calculator = new Calculator();
```

**Act:**

```java
int result = calculator.add(4, 6);
```

**Assert:**

```java
assertEquals(10, result);
```

---

### 17. What happens if the actual result is `9`?

Suppose:

```java
int result = calculator.add(4, 6);
```

produces `9`.

What should happen when this executes?

```java
assertEquals(10, result);
```

**Answer:**  
The assertion fails because:

```text
Expected = 10
Actual   = 9
```

The test therefore reports a failure.

---

### 18. What if the test contains the wrong expectation?

Suppose the production code actually returns `9`, but the test says:

```java
assertEquals(9, result);
```

and the test passes.

What does this demonstrate?

**Answer:**  
It demonstrates that a passing test does not automatically mean the software behaviour is correct.

The **test itself must express the correct expectation**. A test can pass while checking the wrong thing.

---

### 19. Which part represents the behaviour being tested?

```java
int result = calculator.add(4, 6);
```

**Answer:**  
The method invocation:

```java
calculator.add(4, 6)
```

is the behaviour being exercised.

---

### 20. Which part represents the expectation?

```java
assertEquals(10, result);
```

**Answer:**  
The assertion expresses the expectation that the actual result should equal `10`.

---

# Round 4 — "What Happens If?"

### 21. What would you expect if `@Test` were removed from a test method?

**Answer:**  
JUnit would no longer identify that method as a test method merely because it exists in the test class.

The annotation provides the information JUnit needs to recognize the method as a test.

---

### 22. What happens when an assertion's expected value does not match the actual value?

**Answer:**  
The assertion fails, and JUnit reports the test as failed.

---

### 23. What happens if production code changes but the test remains unchanged?

**Answer:**  
The existing test can provide feedback about whether the previously expected behaviour has been affected by the change.

This is one reason automated tests are valuable: they can be rerun after changes.

---

### 24. If you only test `add(2, 3) → 5`, can you conclude that `add()` is correct for every possible pair of integers?

**Answer:**  
No.

One test covers only one condition/input combination. Other inputs and behaviours may expose defects.

This is the beginning of **test design**: deciding which cases provide useful evidence.

---

# Round 5 — Misconception Detector

For each statement, decide **TRUE / FALSE / PARTIALLY TRUE**, then compare your reasoning with the answer.

### 25. "JUnit tests the whole application automatically."

**Answer: FALSE**

JUnit provides testing infrastructure. It does not automatically prove that an entire application is correct.

The developer still has to decide what behaviour to test and write appropriate tests.

---

### 26. "An assertion tells JUnit what result we expect."

**Answer: TRUE, with a qualification.**

An assertion expresses an expected condition or value and checks it against what actually occurred.

For example:

```java
assertEquals(5, result);
```

expresses that `result` is expected to equal `5`.

---

### 27. "If a test passes, the software has no defects."

**Answer: FALSE**

A passing test only establishes that the tested expectation passed under the tested conditions.

It does not establish that all possible behaviours are correct.

---

### 28. "Running a program and testing a program are exactly the same."

**Answer: FALSE**

Running executes the software and lets us observe what happens.

Testing explicitly evaluates observed behaviour against expected behaviour.

---

### 29. "A unit test should focus on a relatively small unit of behaviour."

**Answer: TRUE**

That is the central idea behind unit testing.

The exact boundary of a "unit" can depend on context, but the important concept is focused testing of a relatively small piece of behaviour.

---

### 30. "The test code and production code have exactly the same purpose."

**Answer: FALSE**

Production code implements application behaviour.

Test code checks whether that behaviour satisfies specified expectations.

---

# Round 6 — Exam-Style Discrimination

### 31. Which statement BEST describes a unit test?

A. A test that guarantees an entire application works.

B. A test focused on a relatively small unit of software behaviour.

C. A test that only checks the user interface.

D. A test that replaces production code.

**Answer: B**

---

### 32. Which component primarily provides the framework for defining and executing Java tests?

A. The production class

B. JUnit

C. The assertion result

D. The Java compiler

**Answer: B**

---

### 33. Consider:

```java
int result = calculator.multiply(4, 5);
assertEquals(20, result);
```

Which statement is correct?

A. `20` is the actual result.

B. `result` is the expected value.

C. `20` is the expected value and `result` represents the actual value.

D. `multiply()` is the assertion.

**Answer: C**

---

### 34. Which statement is MOST accurate about a passing test?

A. It proves the entire program is correct.

B. It proves the tested behaviour satisfied the specified expectation under the tested condition.

C. It proves JUnit has found every possible defect.

D. It proves no future code change can introduce a defect.

**Answer: B**

---

### 35. Which sequence best represents the common structure of a simple test?

A. Assert → Arrange → Act

B. Act → Assert → Arrange

C. Arrange → Act → Assert

D. Test → Compile → Design

**Answer: C**

---

# Round 7 — Transfer

### 36. Design a test

Given:

```java
public class DiscountCalculator {

    public double calculate(double price, double discount) {
        return price - (price * discount);
    }
}
```

Requirement:

> A price of 100 with a discount of 0.10 should produce 90.

Answer:

1. What is the unit/behaviour being tested?
2. What should you arrange?
3. What is the action?
4. What is the expected result?
5. Which assertion would express the expectation?

**Answer:**

1. **Behaviour:** `calculate()` calculating the discounted price.
2. **Arrange:** Create a `DiscountCalculator` and prepare `100` and `0.10`.
3. **Act:**

```java
double result = calculator.calculate(100, 0.10);
```

4. **Expected result:** `90`.
5. **Assertion:** An equality assertion comparing the expected result with the actual result.

For example:

```java
assertEquals(90, result);
```

For production-quality floating-point tests, an appropriate delta may be needed; the important Session 1 concept is the expected-vs-actual relationship.

---

### 37. Change the requirement

Now:

> A price of 200 with a discount of 0.25 should produce 150.

What changes in the test?

What stays the same?

**Answer:**

**Changes:**

- Input price: `200`
- Discount: `0.25`
- Expected result: `150`

**Stays the same:**

- The class being tested.
- The method being called.
- The basic Arrange → Act → Assert structure.
- The general assertion strategy.

---

### 38. Why might repeated tests eventually suggest a more systematic testing technique?

**Answer:**  
If many tests have the same structure but differ only in their input values and expected results, the test code becomes repetitive.

That problem eventually motivates a systematic way of supplying multiple data sets to the same test structure.

Later in the course, this leads naturally to **parameterized testing**.

---

# Round 8 — Teach It Back

### 39. Explain JUnit to a Java developer who has never used it.

Your explanation must include:

1. Why testing exists.
2. What a unit test is.
3. What JUnit does.
4. What an assertion does.
5. Expected vs actual.
6. Why a passing test is not proof of a bug-free system.

**Answer / Model explanation:**

Software testing gives us repeatable evidence about whether software behaves as expected. A unit test focuses on a relatively small unit of software behaviour. JUnit is a Java testing framework that helps us define and execute those tests. An assertion checks whether an expected condition or result matches what actually occurred. The expected value is what should happen, while the actual value is what the code produced. A passing test only proves that the tested behaviour satisfied the tested expectation under the tested conditions; it does not prove that the whole system is free of defects.

---

# Round 9 — Five High-Value Comparisons

### 40. Testing vs Running

Complete:

> Running code tells me __________.

> Testing code asks whether __________.

**Answer:**

> Running code tells me **what happened when the code executed**.

> Testing code asks whether **what happened matches the expected behaviour**.

---

### 41. Production Code vs Test Code

Complete:

> Production code primarily provides __________.

> Test code primarily provides __________.

**Answer:**

> Production code primarily provides **application behaviour**.

> Test code primarily provides **repeatable checks of expected behaviour**.

---

### 42. JUnit vs the Code Under Test

Complete:

> The application code contains __________.

> JUnit provides __________.

**Answer:**

> The application code contains **the behaviour being tested**.

> JUnit provides **testing infrastructure for defining and executing tests and evaluating their results**.

---

### 43. Expected vs Actual

Complete:

> Expected = __________.

> Actual = __________.

**Answer:**

> Expected = **what should happen**.

> Actual = **what actually happened**.

---

### 44. Test vs Assertion

Complete:

> A test is __________.

> An assertion is __________.

**Answer:**

> A test is **a test case that establishes conditions, exercises behaviour, and evaluates the result**.

> An assertion is **a mechanism used to check whether an expected condition/result is satisfied**.

---

# Round 10 — Delayed Retrieval

Use these questions again several days or a week later.

### 45. Explain why this statement is incomplete:

> "JUnit is used to test Java."

**Answer:**  
It is directionally correct but incomplete.

A stronger explanation is:

> JUnit is a Java testing framework that provides mechanisms for defining, executing, and evaluating tests. The developer still has to decide what behaviour should be tested and write appropriate tests.

---

### 46. A test passes for `add(2, 3) → 5`. Give two reasons why "the method works" may be too broad.

**Answer:**

Any two of:

- Only one input combination was tested.
- Other behaviours may not have been tested.
- Other paths or boundary conditions may fail.
- The test itself could contain an incorrect expectation.
- Other defects may exist outside the behaviour covered by the test.

---

### 47. You are given a method and asked: "What should this test prove?" What should you think about before writing JUnit code?

**Answer:**  
Start with the **requirement and expected behaviour**.

Ask:

> What should happen for this condition/input?

Only after identifying the behaviour should you decide how to express it using JUnit.

---

### 48. Why is the question "What behaviour am I trying to verify?" more useful than "Which annotation should I use?"

**Answer:**  
Because JUnit features are tools for solving testing problems.

If the student starts with syntax, they may memorize annotations without understanding when or why to use them.

Starting with the behaviour creates the correct reasoning sequence:

```text
Requirement
    ↓
Expected behaviour
    ↓
Test design
    ↓
JUnit mechanism
    ↓
Code
```

---

# Final Challenge — No Notes

### 49. You have this method:

```java
public boolean isAdult(int age) {
    return age >= 18;
}
```

The requirement is:

> A person is considered an adult when their age is 18 or greater.

What are three useful questions you would ask before writing the tests?

**Answer:**

A strong answer should identify questions such as:

1. What should happen for an age below 18?
2. What should happen at exactly 18?
3. What should happen for an age above 18?

This is already moving toward **equivalence partitions and boundary analysis**, which will become major topics later.

---

### 50. Explain this entire Session 1 in one minute.

**Answer / minimum content expected:**

A strong explanation should connect:

```text
Software has behaviour
        ↓
We need evidence that behaviour is correct
        ↓
Unit testing focuses on relatively small units of behaviour
        ↓
JUnit provides Java testing infrastructure
        ↓
A test establishes conditions and exercises behaviour
        ↓
Assertions compare expected and actual behaviour
        ↓
Arrange → Act → Assert provides a useful test structure
        ↓
A passing test only proves the tested behaviour passed
        ↓
Therefore, good testing requires deciding which behaviours and cases to test
```

If a student can explain this chain without notes, the Session 1 mental model is taking hold.

---

# Teacher Scoring Guide

Do not judge retrieval only by the number of correct answers.

Track these six dimensions:

| Skill | Strong evidence | Weak evidence |
|---|---|---|
| **Recall** | States the concept without notes | Needs prompting |
| **Distinction** | Explains why two similar concepts differ | Gives interchangeable definitions |
| **Code interpretation** | Explains what each part of a test is doing | Reads syntax mechanically |
| **Prediction** | Predicts pass/fail before execution | Must run the code to know |
| **Transfer** | Applies the concept to unfamiliar code | Copies the example |
| **Explanation** | Can teach the concept in plain language | Recites a definition |

### Important

A student who gets an MCQ correct by guessing is **not equivalent** to a student who can explain why the correct answer is right and why the alternatives are wrong.

The latter is the level we are training toward.

---

# Teacher Follow-Up Prompts

When a student gives an answer, use:

- **"Why?"**
- **"What makes that different from the other option?"**
- **"What would happen if we changed this?"**
- **"What evidence supports your answer?"**
- **"Can you explain it without JUnit terminology?"**
- **"Give me an example."**
- **"What's the nearest concept you could confuse this with?"**
- **"How would this appear in code?"**

These prompts turn retrieval into **diagnostic learning**, rather than simple answer checking.
