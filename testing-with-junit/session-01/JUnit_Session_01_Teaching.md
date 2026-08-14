# JUnit Teaching Material --- Session 1

## From Software Testing to Your First JUnit Test

### Teacher's Control Panel

**Session:** 1 of 18\
**Duration:** 2 hours\
**Primary theme:** Why testing exists, what a unit test is, what JUnit
does, and writing a first useful JUnit 5 test.

### Session outcome

By the end of this session, students should be able to:

1.  Explain the problem software testing is intended to solve.
2.  Distinguish testing from simply running an application.
3.  Explain what a unit test is and why unit tests are useful.
4.  Explain JUnit's role without confusing JUnit with the code being
    tested.
5.  Write and run a basic JUnit 5 test independently.
6.  Read a simple test and identify the **Arrange → Act → Assert**
    structure.
7.  Given a simple Java method and a stated requirement, decide what
    behaviour should be tested.

### Must master

-   Why we test.
-   What a unit test is.
-   The relationship between production code, a test, and JUnit.
-   The basic structure of a JUnit 5 test.
-   Assertions as statements of expected behaviour.
-   Running a test and interpreting pass/fail.

### Must distinguish

-   Testing vs running/debugging.
-   Production code vs test code.
-   A test case vs the JUnit framework.
-   Actual result vs expected result.
-   A passing test vs proof that software has no bugs.

### Light exposure

Students do not need deep architecture or JUnit internals in this
session. The syllabus contains broader JUnit 5 architecture and
execution infrastructure; those will be earned later when students have
a reason to understand them.

------------------------------------------------------------------------

# 1. Start with the problem, not JUnit

## Opening question

Imagine we have this method:

``` java
public int add(int a, int b) {
    return a + b;
}
```

How do we know it works?

A student may say:

> "Run the application and try it."

Good. Now ask:

-   What if we have 100 methods?
-   What if the application is large?
-   What if someone changes `add()` next month?
-   How do we repeatedly check the behaviour?
-   How do we know what the correct result should be?
-   How do we know whether a change broke something that used to work?

This is the problem testing addresses.

## Core idea

Software testing is about obtaining evidence about software behaviour.

A test gives us a repeatable way to ask:

> **"Given this situation, does the software behave as expected?"**

That question is more important than any JUnit annotation.

------------------------------------------------------------------------

# 2. Running software is not the same as testing it

Suppose a developer runs:

``` java
System.out.println(add(2, 3));
```

and sees:

``` text
5
```

Something happened, but we have not yet expressed a test expectation.

A test is stronger when we explicitly state:

``` text
Given 2 and 3,
when add() is called,
the expected result is 5.
```

That gives us:

-   an input/context,
-   an action,
-   an expected behaviour,
-   and a way to determine whether the expectation was satisfied.

### Important distinction

**Running code** asks:

> "What happens?"

**Testing** asks:

> "Does what happened match the behaviour we expect?"

These activities can overlap, but they are not the same thing.

------------------------------------------------------------------------

# 3. What is a unit?

Before JUnit, understand the word **unit**.

A unit is a relatively small, isolated piece of software that we can
test as a unit of behaviour.

In beginner Java work, a unit will often be represented by a method or a
small class/component, depending on what we are testing.

Do not turn this into a memorization exercise.

The useful question is:

> **What piece of behaviour am I trying to verify independently?**

For example:

``` java
public boolean isAdult(int age) {
    return age >= 18;
}
```

A useful unit-level question is:

> "Does `isAdult()` correctly determine whether an age is at least 18?"

------------------------------------------------------------------------

# 4. Why unit testing matters

Suppose this method works today:

``` java
public boolean isAdult(int age) {
    return age >= 18;
}
```

A developer later changes it:

``` java
public boolean isAdult(int age) {
    return age > 18;
}
```

The code still compiles.

There may be no obvious runtime error.

But the behaviour has changed.

For `18`:

``` text
Expected: true
Actual:   false
```

A useful test can expose this immediately.

This leads to an important principle:

> **Tests provide repeatable checks of expected behaviour.**

They also provide feedback when code changes.

------------------------------------------------------------------------

# 5. What JUnit is doing for us

Now introduce JUnit.

Do not begin with:

> "`@Test` is an annotation..."

Begin with:

> "We have a testing problem. We need a systematic way to define and
> execute tests and determine whether their expectations hold."

JUnit is a testing framework for Java that provides the mechanisms
needed to write and execute tests.

Think of the relationship as:

``` text
Your production code
        ↓
   has behaviour
        ↓
You write tests describing
expected behaviour
        ↓
      JUnit
        ↓
executes the tests and
reports the results
```

### Critical distinction

JUnit is **not** the application being tested.

JUnit is the framework/tooling used to define and execute tests.

Your code contains the behaviour.

Your test expresses what behaviour you expect.

JUnit provides the testing infrastructure.

------------------------------------------------------------------------

# 6. Our first test

Start with ordinary Java production code.

``` java
public class Calculator {

    public int add(int a, int b) {
        return a + b;
    }
}
```

Now ask:

> What behaviour should we verify?

One obvious requirement is:

> Adding 2 and 3 should produce 5.

A JUnit 5 test can express that:

``` java
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;

class CalculatorTest {

    @Test
    void shouldAddTwoNumbers() {
        Calculator calculator = new Calculator();

        int result = calculator.add(2, 3);

        assertEquals(5, result);
    }
}
```

Do not rush past this code.

We want students to understand what each part is doing.

------------------------------------------------------------------------

# 7. Read the test as a story

Instead of memorizing syntax, read it as:

### Arrange

``` java
Calculator calculator = new Calculator();
```

We prepare what the test needs.

### Act

``` java
int result = calculator.add(2, 3);
```

We perform the behaviour we want to test.

### Assert

``` java
assertEquals(5, result);
```

We state what we expect.

So:

``` text
ARRANGE → ACT → ASSERT
```

This is a useful mental model, not a JUnit-specific requirement.

------------------------------------------------------------------------

# 8. What does `@Test` mean?

``` java
@Test
void shouldAddTwoNumbers() {
    ...
}
```

At this stage, the useful understanding is:

> `@Test` tells JUnit that this method represents a test method.

It is not necessary to teach the entire annotation system here.

The student should be able to answer:

**Question:** Which part tells JUnit this method is a test?

**Answer:** `@Test`.

But immediately follow it with a more useful question:

> "If I remove `@Test`, what do you expect will happen?"

This starts building cause-and-effect understanding rather than
annotation memorization.

------------------------------------------------------------------------

# 9. What is an assertion?

The assertion is one of the most important concepts students will learn.

``` java
assertEquals(5, result);
```

It communicates:

> "I expect the actual result to equal 5."

Conceptually:

``` text
Expected value ─────┐
                    ├── comparison ──→ pass/fail
Actual value ───────┘
```

If:

``` text
expected = 5
actual   = 5
```

the assertion passes.

If:

``` text
expected = 5
actual   = 4
```

the assertion fails.

### Important distinction

A failed assertion does not mean:

> "JUnit is broken."

It means:

> "The observed behaviour did not satisfy the expectation expressed by
> this assertion."

That may indicate a defect in the production code, an incorrect test
expectation, or another problem in the test setup.

------------------------------------------------------------------------

# 10. The first important misconception

A passing test does **not** prove:

> "The software is bug-free."

It proves something narrower:

> **The software satisfied the behaviour checked by that particular test
> under that particular condition.**

For example, if we only test:

``` java
add(2, 3)
```

we have not established that every possible input to `add()` is correct.

This is the beginning of test design.

Later we will ask:

> "Which cases should we test?"

For now, students only need to understand the limitation.

------------------------------------------------------------------------

# 11. Test behaviour, not implementation trivia

Consider:

``` java
public int add(int a, int b) {
    return a + b;
}
```

A useful test focuses on:

> "Given these inputs, is the returned result correct?"

The test should not primarily exist to prove that the method contains a
particular line of code.

The important target is **observable behaviour**.

This idea will become increasingly important as the course progresses.

------------------------------------------------------------------------

# 12. Guided practical

## Exercise 1 --- First test

Create:

``` java
Calculator
```

with:

``` java
int add(int a, int b)
```

Then create:

``` java
CalculatorTest
```

Write a JUnit 5 test that verifies:

``` text
2 + 3 = 5
```

### Teacher rule

Do not display the complete solution first.

Give students the problem.

If they ask what to do, use the hint ladder:

1.  "What behaviour are you trying to verify?"
2.  "What class contains that behaviour?"
3.  "What input should you provide?"
4.  "What result do you expect?"
5.  "Which JUnit mechanism compares an expected value with an actual
    value?"
6.  Only then provide stronger assistance.

------------------------------------------------------------------------

# 13. Guided practical --- variation

Now change the requirement:

> Verify that `10 + 5` produces `15`.

Do not let students simply copy the previous test without thinking.

Ask:

-   What changes?
-   What stays the same?
-   Why?
-   Is this a new testing technique or the same technique with different
    data?

The answer should lead them toward:

> Same test structure, different input and expected result.

This prepares the ground for parameterized testing later.

------------------------------------------------------------------------

# 14. Independent challenge

Give students:

``` java
public class TemperatureConverter {

    public double celsiusToFahrenheit(double celsius) {
        return (celsius * 9 / 5) + 32;
    }
}
```

Requirement:

> When the input is `0°C`, the result should be `32°F`.

Students must independently:

1.  Create the test class.
2.  Identify the method to test.
3.  Choose the input.
4.  Determine the expected result.
5.  Write the test.
6.  Run it.
7.  Explain why the test passes.

### Do not help immediately.

Ask:

> "What are you trying to prove?"

Then:

> "What would count as success?"

Then:

> "Where is the actual result?"

Then:

> "What should we compare it with?"

------------------------------------------------------------------------

# 15. Thinking exercise --- distinguish these

Ask students to classify each statement.

### A

> "I ran the program and it printed 32."

### B

> "I wrote a test that expects `celsiusToFahrenheit(0)` to return 32."

### C

> "JUnit executed the test and reported that the assertion passed."

These are three different things:

-   **A:** manually observing program behaviour.
-   **B:** expressing an expected behaviour as a test.
-   **C:** the testing framework executing the test and reporting the
    result.

This distinction should become part of the student's mental model.

------------------------------------------------------------------------

# 16. Mini diagnostic

Ask each student individually:

### Question 1

What problem does JUnit help solve?

### Question 2

What is the difference between the code under test and the test code?

### Question 3

What does `@Test` communicate?

### Question 4

What is the purpose of an assertion?

### Question 5

What is the difference between expected and actual values?

### Question 6

Does one passing test prove that a method is completely correct? Why?

### Question 7

Explain this test without looking at your notes:

``` java
@Test
void shouldMultiply() {
    Calculator calculator = new Calculator();

    int result = calculator.multiply(4, 5);

    assertEquals(20, result);
}
```

------------------------------------------------------------------------

# 17. Exam lens

Students should already begin developing the habit:

> **Read the wording carefully.**

Potential distinctions:

### JUnit vs unit testing

Unit testing is a testing approach/practice.

JUnit is a Java testing framework/tool used to implement and execute
tests.

### Test vs assertion

A test is the broader test case/method.

An assertion checks whether an expected condition is satisfied.

### Expected vs actual

Expected = what the test says should happen.

Actual = what the code actually produced.

### Running vs testing

Running code can reveal behaviour.

Testing explicitly evaluates observed behaviour against an expectation.

### Passing test vs bug-free software

A passing test demonstrates that the tested behaviour satisfied that
test's expectation under that condition. It does not prove the entire
system is defect-free.

------------------------------------------------------------------------

# 18. Exit test

Use this without notes.

### 1. Which statement best describes the purpose of a unit test?

A. To make Java compile faster\
B. To verify a small unit of software behaviour\
C. To replace the application\
D. To guarantee that the entire application contains no defects

**Answer:** B

------------------------------------------------------------------------

### 2. In this test, what is the actual value?

``` java
int result = calculator.add(2, 3);
assertEquals(5, result);
```

A. `2`\
B. `3`\
C. `5`\
D. `result`

**Answer:** D

------------------------------------------------------------------------

### 3. What does `@Test` primarily indicate?

A. The method contains production logic\
B. The method should be treated as a JUnit test method\
C. The method must return a value\
D. The method is executed before every test

**Answer:** B

------------------------------------------------------------------------

### 4. If the expected value is `10` and the actual value is `8`, what should happen to `assertEquals(10, actual)`?

A. Pass\
B. Fail\
C. Compile only\
D. Skip automatically

**Answer:** B

------------------------------------------------------------------------

### 5. Which statement is most accurate?

A. One passing test proves the method is correct for every possible
input.\
B. JUnit generates the production code being tested.\
C. A test expresses expected behaviour and JUnit provides mechanisms to
execute and evaluate it.\
D. Assertions and tests are exactly the same thing.

**Answer:** C

------------------------------------------------------------------------

# 19. Homework

## Part A --- Implementation

Create a class:

``` java
StringUtils
```

with:

``` java
boolean isEmpty(String value)
```

Define a reasonable behaviour for the method and write tests for at
least **three different cases**.

Do not copy the calculator example.

The purpose is transfer.

## Part B --- Explanation

Write 3--5 sentences answering:

> Why is writing a test more useful than simply running the method and
> looking at its output?

## Part C --- Prediction

Before running your tests, predict which should pass and which should
fail.

Then run them.

Record:

``` text
Prediction:
Actual result:
What I learned:
```

------------------------------------------------------------------------

# 20. Retrieval targets for future sessions

Bring these concepts back repeatedly:

-   Why software testing exists.
-   Testing vs running code.
-   Unit testing.
-   JUnit's role.
-   Production code vs test code.
-   `@Test`.
-   Assertions.
-   Expected vs actual.
-   Arrange → Act → Assert.
-   What a passing test actually tells us.
-   What a passing test does NOT tell us.

Do not teach these once and retire them.

They are foundational vocabulary for the entire course.

------------------------------------------------------------------------

# 21. Teacher preparation notes

Before teaching, personally implement every practical example.

Then deliberately break at least one:

``` java
public int add(int a, int b) {
    return a - b;
}
```

Run the test.

Observe the failure.

Then ask yourself:

-   What exactly does JUnit report?
-   Can I explain the failure without looking at notes?
-   Can I explain expected vs actual?
-   Can I explain why the test failed rather than merely saying "the
    answer is wrong"?

Then write one additional test yourself that students will not see
beforehand.

The goal is not to prepare a polished performance.

The goal is to be prepared to **diagnose student thinking**.

------------------------------------------------------------------------

# 22. Session principle

The most important sentence students should leave with is:

> **A test is a repeatable way of expressing and checking expected
> software behaviour.**

JUnit is the mechanism that helps us put that idea into practice in
Java.

Everything else in this course will progressively make that basic idea
more powerful.

------------------------------------------------------------------------

## Source boundary

The syllabus supplied for this course establishes that Chapter 1 covers
software quality, QA, verification/validation, defects, testing, testing
levels/methods, unit testing, and JUnit 3/4 material, while later
chapters move into JUnit 5. fileciteturn1file0L17-L27

This Session 1 document is therefore a **Pareto teaching synthesis**,
not a transcription of the PACTS book. The JUnit-specific terminology
and current JUnit 5 behaviour were checked against the official JUnit
User Guide. citeturn0search0turn0search2
