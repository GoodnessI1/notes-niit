# JUnit — Session 6
## Mockito: Why Mocking Exists

### Where we are

Students can now write JUnit tests, use assertions, lifecycle methods, exception assertions, parameterized tests, and think about meaningful test cases.

Chapter 5 now begins the syllabus section on integrating JUnit 5 with external frameworks. The first framework listed is Mockito. fileciteturn5file0L30-L32

The key rule is:

> **Do not teach Mockito as a collection of annotations. Teach the problem it solves first.**

---

# 1. The problem Mockito solves

Consider:

```java
class OrderService {
    private PaymentService paymentService;

    public boolean placeOrder(Order order) {
        if (paymentService.charge(order.getAmount())) {
            // save order
            return true;
        }
        return false;
    }
}
```

We want to test `OrderService`.

But `OrderService` depends on `PaymentService`.

The question becomes:

> When testing `OrderService`, do we really want to use the real `PaymentService`?

Often, no.

We want to test:

```text
OrderService
```

without making the test depend on the real behaviour of:

```text
PaymentService
```

This is where mocking becomes useful.

---

# 2. Dependency

A dependency is an object another object needs to perform its work.

Here:

```text
OrderService
      ↓
PaymentService
```

`OrderService` depends on `PaymentService`.

This is not a Mockito concept.

It is an ordinary object-oriented design concept.

Mockito becomes relevant because we want to control that dependency during a test.

---

# 3. What is a mock?

A mock is a test double whose behaviour can be configured and whose interactions can be inspected.

Instead of using the real dependency:

```text
OrderService → real PaymentService
```

we can use:

```text
OrderService → mock PaymentService
```

The mock allows the test to control what the dependency returns.

For example:

```text
paymentService.charge(...)
             ↓
          return true
```

or:

```text
paymentService.charge(...)
             ↓
          return false
```

---

# 4. Why control the dependency?

Suppose the payment service contacts a real payment provider.

That creates problems for a unit test:

- external systems
- network calls
- unpredictable responses
- slower execution
- difficult setup
- possible side effects

A unit test of `OrderService` should primarily give us evidence about `OrderService`.

Mocking lets us isolate the unit from the dependency.

---

# 5. Mocking is not "fake testing"

A common misconception:

> "If I mock the dependency, I am not really testing the code."

Incorrect.

We are testing the unit under controlled conditions.

For example:

```text
Given payment succeeds
        ↓
OrderService should save/complete the order
```

and:

```text
Given payment fails
        ↓
OrderService should not complete the order
```

The mock supplies the controlled payment behaviour.

The JUnit assertions verify the behaviour of `OrderService`.

---

# 6. Stubbing

Stubbing means specifying what a mock should return when a particular method is called.

Conceptually:

```text
When paymentService.charge(...)
is called
        ↓
Return true
```

With Mockito:

```java
when(paymentService.charge(100))
    .thenReturn(true);
```

This is the basic stubbing pattern:

```text
when(...)
   ↓
thenReturn(...)
```

Read it as:

> When this interaction occurs, return this value.

---

# 7. Verification

Stubbing asks:

> **What should the dependency return?**

Verification asks:

> **Did the unit interact with the dependency as expected?**

For example:

```java
verify(paymentService).charge(100);
```

This checks that the interaction occurred.

So remember:

```text
STUBBING
→ controls dependency behaviour

VERIFICATION
→ checks dependency interaction
```

This distinction is extremely important.

---

# 8. Stubbing vs verification

Suppose:

```java
when(paymentService.charge(100))
    .thenReturn(true);
```

This does **not** primarily mean:

> "Prove that `charge(100)` was called."

It means:

> "If `charge(100)` is called, make the mock return `true`."

By contrast:

```java
verify(paymentService).charge(100);
```

asks whether that interaction occurred.

```text
when(...).thenReturn(...)
        ↓
       STUB

verify(...)
        ↓
     VERIFY
```

---

# 9. A complete conceptual test

```java
@Test
void shouldPlaceOrderWhenPaymentSucceeds() {

    when(paymentService.charge(100))
        .thenReturn(true);

    boolean result = orderService.placeOrder(order);

    assertTrue(result);

    verify(paymentService).charge(100);
}
```

Think about the four stages:

```text
1. Arrange dependency behaviour
        ↓
2. Execute the unit
        ↓
3. Assert returned behaviour
        ↓
4. Verify important interaction
```

Mockito does not replace JUnit.

They have different roles.

```text
JUnit
→ test execution + assertions

Mockito
→ mock/stub/verify dependencies
```

---

# 10. Mockito and JUnit work together

JUnit remains the test framework.

Mockito is an external framework used to create and control test doubles.

Conceptually:

```text
JUnit
  +
Mockito
  ↓
Test a unit while controlling its dependencies
```

Chapter 5 specifically covers Mockito and its JUnit 5 extension. fileciteturn4file2L275-L279

---

# 11. The core mental model

When students see Mockito, they should ask:

### Question 1
What is the unit I am testing?

### Question 2
What dependencies does it use?

### Question 3
Do I want the real dependency in this test?

### Question 4
If not, what behaviour should the mock provide?

### Question 5
What interaction should I verify?

That is much more useful than memorizing annotations.

---

# 12. What we will deliberately postpone

The syllabus contains more Mockito detail, including the JUnit 5 extension.

For this first Mockito session, do not overload students with:

- every Mockito annotation
- advanced argument matchers
- spies
- captors
- deep stubs
- advanced verification modes
- extension internals

First make this distinction automatic:

```text
Mock
→ controlled test double

Stub
→ predetermined response

Verify
→ check interaction
```

---

# Session outcome

Students should be able to explain:

> **Why might a unit test replace a real dependency with a mock?**

They should also be able to distinguish:

```text
JUnit vs Mockito
real dependency vs mock
stubbing vs verification
returned behaviour vs interaction
```

And write a simple test where:

```text
mock dependency
      ↓
stub response
      ↓
call unit
      ↓
assert result
      ↓
verify interaction
```
