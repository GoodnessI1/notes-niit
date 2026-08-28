# JUnit — Chapter 4
## Simplifying Testing with Advanced JUnit Features

### Pareto focus

Chapter 4 of the syllabus covers dependency injection, dynamic tests, test interfaces, test templates, parameterized tests, argument sources/conversion, custom names, and Java 9 compatibility. fileciteturn2file2L940-L949

For teaching, we will **not give all of these equal weight**.

The highest-value idea is:

> **When many tests have the same structure but different data, parameterized testing lets us express that pattern without writing a separate test method for every data set.**

That is the main practical capability students should leave with.

---

# 1. The problem: repetitive tests

Suppose we want to test a calculator:

```java
@Test
void shouldAdd2And3() {
    assertEquals(5, calculator.add(2, 3));
}

@Test
void shouldAdd5And4() {
    assertEquals(9, calculator.add(5, 4));
}

@Test
void shouldAdd10And7() {
    assertEquals(17, calculator.add(10, 7));
}
```

Look carefully.

The **test logic is almost identical**.

Only the data changes:

```text
2, 3 → 5
5, 4 → 9
10, 7 → 17
```

This is the problem parameterized tests address.

---

# 2. Parameterized testing

Instead of writing several almost-identical test methods, we can write one parameterized test and supply multiple sets of arguments.

Conceptually:

```text
One test structure
       +
Multiple data sets
       ↓
Multiple test invocations
```

This is the central idea.

---

# 3. `@ParameterizedTest`

A parameterized test is marked with:

```java
@ParameterizedTest
```

rather than simply:

```java
@Test
```

Example:

```java
@ParameterizedTest
@ValueSource(ints = {2, 5, 10})
void shouldBePositive(int number) {
    assertTrue(number > 0);
}
```

The same test method is invoked using each supplied value.

Conceptually:

```text
shouldBePositive(2)
shouldBePositive(5)
shouldBePositive(10)
```

So one test definition can represent multiple test cases.

---

# 4. `@ValueSource`

`@ValueSource` is useful when one parameter receives a sequence of simple values.

Example:

```java
@ParameterizedTest
@ValueSource(ints = {1, 2, 3, 4, 5})
void shouldBePositive(int number) {
    assertTrue(number > 0);
}
```

The test receives:

```text
1
2
3
4
5
```

### Important distinction

`@ValueSource` supplies **one argument per invocation**.

It is not the general solution for supplying several unrelated parameters.

---

# 5. Multiple parameters: `@CsvSource`

Suppose our test needs:

```text
input + expected result
```

For example:

```text
2 + 3 → 5
5 + 4 → 9
10 + 7 → 17
```

`@CsvSource` is a natural fit:

```java
@ParameterizedTest
@CsvSource({
    "2, 3, 5",
    "5, 4, 9",
    "10, 7, 17"
})
void shouldAddNumbers(int a, int b, int expected) {
    assertEquals(expected, calculator.add(a, b));
}
```

Each CSV row supplies one invocation:

```text
a   b   expected
2   3   5
5   4   9
10  7   17
```

This is one of the most useful parameterized-test patterns.

---

# 6. `@CsvFileSource`

If the test data is stored in a CSV file rather than directly in the annotation, JUnit provides:

```java
@CsvFileSource(...)
```

Conceptually:

```text
CSV file
   ↓
Arguments
   ↓
Parameterized test
```

Use this when the amount of data makes putting everything directly inside the annotation inconvenient.

For the core lesson, students mainly need to understand:

> `@CsvSource` → CSV data written in the test  
> `@CsvFileSource` → CSV data supplied from a file

---

# 7. `@EnumSource`

Sometimes the input is an enum.

Example:

```java
enum Status {
    NEW,
    PROCESSING,
    COMPLETE
}
```

A parameterized test can receive enum values through:

```java
@ParameterizedTest
@EnumSource(Status.class)
void shouldHandleEveryStatus(Status status) {
    // ...
}
```

The key idea:

> `@EnumSource` supplies enum constants as arguments.

Students do not need to memorize every filtering option initially.

---

# 8. `@MethodSource`

Sometimes the test data is more complicated than a few literals.

For example:

```java
static Stream<Arguments> additionCases() {
    return Stream.of(
        Arguments.of(2, 3, 5),
        Arguments.of(5, 4, 9),
        Arguments.of(10, 7, 17)
    );
}
```

The parameterized test can use it:

```java
@ParameterizedTest
@MethodSource("additionCases")
void shouldAddNumbers(int a, int b, int expected) {
    assertEquals(expected, calculator.add(a, b));
}
```

The important distinction:

> `@MethodSource` obtains arguments from a method.

This is useful when the test data needs Java code to construct it.

---

# 9. `@ArgumentsSource`

JUnit also supports custom argument providers through:

```java
@ArgumentsSource
```

The idea is:

```text
Custom provider
      ↓
Arguments
      ↓
Parameterized test
```

This is more flexible, but also more complex.

### Pareto decision

Students should **recognize what `@ArgumentsSource` does**, but we do not need to spend the same classroom time here as we spend on `@ValueSource`, `@CsvSource`, and `@MethodSource`.

---

# 10. Comparing the major argument sources

| Source | Best mental model |
|---|---|
| `@ValueSource` | Simple individual values |
| `@EnumSource` | Enum constants |
| `@CsvSource` | Multiple values per row |
| `@CsvFileSource` | CSV data from a file |
| `@MethodSource` | Arguments produced by a method |
| `@ArgumentsSource` | Custom argument provider |

This table is worth understanding because the exam can test **which source fits a particular situation**.

---

# 11. `@ValueSource` vs `@CsvSource`

This is a particularly useful distinction.

### `@ValueSource`

```java
@ValueSource(ints = {1, 2, 3})
```

Think:

```text
one simple value
one invocation
```

### `@CsvSource`

```java
@CsvSource({
    "1, 2, 3",
    "4, 5, 9"
})
```

Think:

```text
multiple values
one row = one invocation
```

So:

```text
ValueSource → simple single argument
CsvSource   → several arguments/data columns
```

---

# 12. Parameterized tests still follow normal testing principles

A parameterized test is still a test.

It should still have a meaningful structure:

```text
Arrange
   ↓
Act
   ↓
Assert
```

For example:

```java
@ParameterizedTest
@CsvSource({
    "2, 3, 5",
    "5, 4, 9"
})
void shouldAddNumbers(int a, int b, int expected) {

    int result = calculator.add(a, b);

    assertEquals(expected, result);
}
```

The data varies.

The testing logic remains stable.

---

# 13. Why this matters

Without parameterization:

```text
Test A
Test B
Test C
Test D
Test E
```

Each may repeat the same logic.

With parameterization:

```text
One test definition
       ↓
A
B
C
D
E
```

This reduces duplication and makes the relationship between **test logic and test data** explicit.

---

# 14. Parameterized test ≠ one execution

A common misconception:

> "There is only one test method, so it runs only once."

Not necessarily.

A parameterized test is invoked with each supplied argument set.

For example:

```java
@ValueSource(ints = {1, 2, 3})
```

means the test is invoked for:

```text
1
2
3
```

So the distinction is:

```text
One test definition
       ≠
One test invocation
```

This is an important conceptual distinction.

---

# 15. Argument conversion

Suppose JUnit receives source data as strings but the test parameter expects another type.

JUnit can perform argument conversion in supported situations.

Example:

```java
@ParameterizedTest
@ValueSource(strings = {"1", "2", "3"})
void shouldAcceptNumbers(int number) {
    assertTrue(number > 0);
}
```

The source values are strings, while the parameter is an `int`.

JUnit supports implicit conversion for supported types.

There is also explicit conversion when the desired conversion needs to be specified.

### Pareto boundary

Students should understand:

> **Argument sources provide data; argument conversion makes supplied data usable as the parameter type when conversion is supported.**

Do not spend large amounts of time memorizing the entire conversion table.

---

# 16. Custom names

Parameterized tests can produce multiple invocations, so it can be useful to make their displayed names clearer.

JUnit supports custom display-name patterns for parameterized invocations.

The important idea:

> **Custom names affect how test invocations are displayed; they do not change the underlying test logic.**

This is useful, but lower priority than understanding parameterized test data itself.

---

# 17. Dynamic tests

A dynamic test is different from an ordinary statically declared `@Test` method.

The important idea is:

> A dynamic test is generated programmatically at runtime.

Instead of declaring every test as a normal test method, code can generate test cases dynamically.

Conceptually:

```text
Test factory
     ↓
Generate test cases
     ↓
JUnit executes generated tests
```

This is useful when the set of tests is determined programmatically.

### Pareto boundary

Students should know the distinction:

```text
Parameterized test
→ one test structure + supplied arguments

Dynamic test
→ tests generated programmatically at runtime
```

That distinction is more important than memorizing the complete dynamic-test API at this stage.

---

# 18. Test templates

A test template is another advanced mechanism for running a test structure according to a particular invocation context.

For the 80/20 approach, students mainly need recognition:

```text
Parameterized test
→ supplied data drives invocations

Test template
→ invocation context drives repeated execution
```

The complete extension machinery behind test templates is not a high-return beginner topic.

---

# 19. Dependency injection

Chapter 4 also introduces parameter resolution/dependency injection concepts such as:

- `TestInfo`
- `RepetitionInfo`
- `TestReporter`

The important idea is that JUnit can provide certain objects to test or lifecycle methods through its parameter-resolution mechanism.

For example:

```java
@Test
void testSomething(TestInfo testInfo) {
    // use information about the current test
}
```

The parameter is not something the student manually constructs.

JUnit supplies it.

### Core distinction

```text
Normal method parameter
→ caller supplies it

JUnit-resolved parameter
→ JUnit supplies it through its parameter-resolution mechanism
```

This is useful to recognize, but it is not the primary practical skill of this chapter.

---

# 20. Test interfaces

JUnit allows testing-related behavior to be placed in interfaces and inherited by implementing test classes.

This can support reusable testing conventions.

Conceptually:

```text
Test interface
      ↓
Implementing test class
      ↓
Inherited test behavior
```

This is an advanced reuse mechanism.

For our 80/20 treatment:

> Understand what problem it addresses; do not invest heavily in implementation details.

---

# 21. What belongs in the student's core mental model?

At the end of this chapter, students should be able to distinguish:

```text
Ordinary test
    ↓
One declared test structure

Parameterized test
    ↓
One test structure + multiple supplied argument sets

Dynamic test
    ↓
Test cases generated programmatically

Test template
    ↓
Test structure executed according to invocation context
```

And for argument sources:

```text
@ValueSource
@EnumSource
@CsvSource
@CsvFileSource
@MethodSource
@ArgumentsSource
```

Students should be able to identify **which one solves which data-supply problem**.

---

# 22. The 80/20 priority

### Deep understanding + practical coding

- Parameterized tests
- `@ParameterizedTest`
- `@ValueSource`
- `@CsvSource`
- `@MethodSource`
- Choosing the appropriate argument source
- Understanding one test definition vs multiple invocations

### Understand + recognize

- `@EnumSource`
- `@CsvFileSource`
- `@ArgumentsSource`
- Argument conversion
- Dynamic tests
- Test templates
- JUnit parameter resolution / dependency injection

### Awareness only unless the exam requires more

- Detailed `TestInfoParameterResolver`
- `RepetitionInfoParameterResolver`
- `TestReporterParameterResolver`
- Advanced custom argument conversion
- Java 9 compatibility details
- Deep extension implementation

The syllabus explicitly lists these topics, but the 80/20 teaching approach does not require equal classroom investment in all of them. fileciteturn2file2L940-L968

---

# 23. Session takeaway

The central problem is:

> **How do we avoid writing many nearly identical tests when only the input data changes?**

The answer:

> **Parameterized testing.**

Remember:

```text
@ParameterizedTest
        +
Argument source
        ↓
Multiple invocations
```

And distinguish:

```text
@ValueSource
→ simple values

@CsvSource
→ multiple values per row

@MethodSource
→ values produced by a method

@EnumSource
→ enum constants

@CsvFileSource
→ CSV file

@ArgumentsSource
→ custom argument provider
```

The deeper advanced features exist, but parameterized testing is the major practical payoff of this chapter.
