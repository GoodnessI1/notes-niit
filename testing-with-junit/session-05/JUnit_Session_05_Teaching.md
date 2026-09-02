# JUnit — Session 5
## Choosing Test Cases: Exceptions, Boundaries, and Parameterized Thinking

## Why this session comes next
We have learned how to write tests, use lifecycle methods, and run the same test structure with different data.

The next question is:

> **Which data should we choose?**

A parameterized test is only useful if its inputs represent meaningful behaviours.

## 1. One method can have different behaviours

```java
boolean isValidAge(int age) {
    return age >= 18;
}
```

```text
17 → false
18 → true
19 → true
```

`18` is special because behaviour changes there.

```text
17 | 18 | 19
false | true | true
      ↑
   boundary
```

A good test asks:

> Which values are most likely to reveal whether the behaviour is correct?

## 2. Normal cases vs boundary cases

A normal case is comfortably inside an expected range.

```text
25 → true
```

A boundary case is at, immediately below, or immediately above a point where behaviour changes.

```text
17 → false
18 → true
19 → true
```

The boundary is often more informative than many random values.

## 3. Exceptions are also behaviour

```java
int divide(int a, int b) {
    return a / b;
}
```

For `b = 0`, the expected behaviour is an exception.

```java
@Test
void shouldThrowExceptionWhenDividingByZero() {
    assertThrows(
        ArithmeticException.class,
        () -> calculator.divide(10, 0)
    );
}
```

The key distinction:

```text
A failing test
≠
A test that correctly verifies expected exceptional behaviour
```

If the expected exception occurs, `assertThrows` can pass.

## 4. The lambda is the code being tested

```java
assertThrows(
    ArithmeticException.class,
    () -> calculator.divide(10, 0)
);
```

```text
Expected exception type
          +
Operation expected to throw
          ↓
assertThrows checks them
```

## 5. Parameterized tests + meaningful cases

```java
@ParameterizedTest
@CsvSource({
    "17, false",
    "18, true",
    "19, true"
})
void shouldValidateAge(int age, boolean expected) {
    assertEquals(expected, isValidAge(age));
}
```

The important thinking happened before the annotation:

```text
What are the meaningful cases?
        ↓
Below boundary
At boundary
Above boundary
        ↓
Represent them as test data
```

## 6. Do not confuse these

### Test case
A particular situation used to verify behaviour.

```text
Input: 17
Expected: false
```

### Test method
Java code defining the test logic.

### Parameterized invocation
One execution using one argument set.

```text
One parameterized test method
            ↓
      multiple invocations
            ↓
      multiple test cases
```

## 7. Practice

Given:

```java
boolean canWithdraw(int balance, int amount) {
    return amount > 0 && amount <= balance;
}
```

Identify useful cases before writing code:

- normal successful withdrawal
- amount equals balance
- amount greater than balance
- amount is zero
- amount is negative

Then ask:

- Which cases can be parameterized?
- What result is expected?
- Is an exception expected?

## 8. The workflow

```text
1. What behaviour am I testing?
2. What inputs can produce different behaviour?
3. Where does behaviour change?
4. What should happen for invalid input?
5. Then choose ordinary or parameterized tests.
```

## Session takeaway

```text
Parameterized testing:
How can I run similar test logic with different data?

Test design:
Which data should I choose?
```

The first is a JUnit technique.

The second is testing thinking.
