# JUnit Retrieval — Session 6
## MCQ Exam Drill: Mockito Fundamentals

### 1. What is the primary reason to use a mock in a unit test?

A. To eliminate all assertions  
B. To replace/control a dependency so the unit can be tested under controlled conditions  
C. To make production code execute faster  
D. To replace JUnit

**Answer: B**

**Explanation:** A mock can isolate the unit from the real behaviour of a dependency.

### 2. If `OrderService` uses `PaymentService`, what is `PaymentService` from the perspective of `OrderService`?

A. An assertion  
B. A dependency  
C. A test runner  
D. A test case

**Answer: B**

**Explanation:** A dependency is an object another object needs to perform its work.

### 3. Which statement best describes a mock?

A. The real production dependency  
B. A test double whose behaviour can be controlled and whose interactions can be inspected  
C. A replacement for every assertion  
D. A production database

**Answer: B**

### 4. Why might using a real external payment service be undesirable in a unit test?

A. It prevents Java compilation  
B. It can introduce network calls, unpredictability, slowness, and side effects  
C. JUnit forbids real objects  
D. Assertions cannot work with real objects

**Answer: B**

### 5. What does stubbing primarily do?

A. Checks whether an interaction occurred  
B. Specifies how a mock should behave when an interaction occurs  
C. Executes the production application  
D. Replaces the test runner

**Answer: B**

### 6. Which Mockito pattern represents stubbing?

A. `verify(... )`
B. `when(...).thenReturn(...)`
C. `assertEquals(...)`
D. `@Test`

**Answer: B**

### 7. What does this mean?

```java
when(paymentService.charge(100))
    .thenReturn(true);
```

A. Prove that `charge(100)` was called  
B. If `charge(100)` is called on the mock, return `true`  
C. Force the real payment service to return true  
D. Assert that the result is true

**Answer: B**

### 8. What does verification primarily answer?

A. What value should the mock return?  
B. Did the expected interaction with the dependency occur?  
C. Which test class should run?  
D. Whether the Java compiler succeeded

**Answer: B**

### 9. Which Mockito operation is associated with verification?

A. `when`
B. `thenReturn`
C. `verify`
D. `assertTrue`

**Answer: C**

### 10. Which distinction is correct?

A. Stubbing checks interactions; verification supplies return values.  
B. Stubbing controls mock behaviour; verification checks interactions.  
C. Stubbing and verification are identical.  
D. Verification replaces assertions.

**Answer: B**

### 11. Which statement about JUnit and Mockito is correct?

A. Mockito replaces JUnit's test execution system.  
B. JUnit provides the testing framework while Mockito helps control dependencies/test doubles.  
C. JUnit creates all mocks automatically.  
D. Mockito is an assertion library only.

**Answer: B**

### 12. What does a JUnit assertion primarily check?

A. Expected test conditions/results  
B. Whether Mockito created a mock  
C. Whether a dependency was injected  
D. Whether a CSV file exists

**Answer: A**

### 13. What does Mockito verification primarily check?

A. The returned value of every method  
B. Interactions with a mock  
C. Whether JUnit executed the test  
D. Whether production code compiled

**Answer: B**

### 14. Consider:

```java
when(paymentService.charge(100))
    .thenReturn(true);
```

Which statement is NOT the primary meaning?

A. The mock is being configured  
B. A response is being stubbed  
C. The mock should return true for that configured call  
D. The test has proven that `charge(100)` was called

**Answer: D**

**Explanation:** Stubbing does not by itself verify that the interaction occurred.

### 15. Consider:

```java
verify(paymentService).charge(100);
```

What is this primarily checking?

A. That the mock will return 100  
B. That `charge(100)` was invoked on the mock  
C. That `charge` cannot throw an exception  
D. That the real payment service was called

**Answer: B**

### 16. Which sequence best represents a simple Mockito-based unit test?

A. Verify → stub → execute → assert  
B. Stub → execute → assert → verify  
C. Assert → execute → stub → verify  
D. Execute → verify → create dependency → stub

**Answer: B**

**Explanation:** First control the dependency, then execute the unit, assert its result, and verify important interactions.

### 17. Why is mocking useful for isolation?

A. It prevents the unit from executing.  
B. It allows the unit to be tested without depending on the real behaviour of a dependency.  
C. It removes all dependencies from the production code.  
D. It makes every test an integration test.

**Answer: B**

### 18. Which statement is correct?

A. A mock is always a real dependency.  
B. A mock is used in the test as a controlled substitute for a dependency.  
C. A mock is an assertion.  
D. A mock is a lifecycle annotation.

**Answer: B**

### 19. Which statement is closest to the meaning of "stub"?

A. Check whether an interaction occurred  
B. Predetermine a response from a test double  
C. Run a test repeatedly  
D. Skip a test

**Answer: B**

### 20. Which statement is closest to the meaning of "verify"?

A. Predetermine a return value  
B. Check whether an expected interaction occurred  
C. Create a production dependency  
D. Convert arguments

**Answer: B**

### 21. A student says:

> "`when(...).thenReturn(...)` proves that the dependency method was called."

What is the best correction?

A. Correct; stubbing always proves the interaction.  
B. Incorrect; stubbing configures behaviour, while `verify(...)` checks the interaction.  
C. Correct; `when` is a verification method.  
D. Incorrect; Mockito cannot inspect interactions.

**Answer: B**

### 22. A student says:

> "Mockito is the testing framework and JUnit is just an assertion library."

What is the best correction?

A. Correct  
B. Incorrect; JUnit provides the testing framework, while Mockito provides mocking/test-double functionality  
C. Correct only for parameterized tests  
D. Neither framework supports unit testing

**Answer: B**

### 23. Suppose payment succeeds only when the dependency returns `true`. What should the test do if it wants to force the success scenario?

A. Verify the dependency without configuring it  
B. Stub the dependency to return `true`  
C. Replace the assertion with `verify`  
D. Use `@BeforeEach` instead

**Answer: B**

### 24. Suppose the requirement is:

> "When an order is placed, the payment service must be called with the order amount."

Which operation is most directly relevant?

A. `thenReturn`
B. `verify`
C. `@ValueSource`
D. `assertThrows`

**Answer: B**

### 25. Which distinction should be automatic after this session?

A. JUnit = dependency control; Mockito = assertions  
B. JUnit = test execution/assertions; Mockito = mock, stub, and verify dependencies  
C. JUnit = production code; Mockito = test code  
D. JUnit = database; Mockito = web server

**Answer: B**

### 26. Which sequence best captures the conceptual flow?

A. Real dependency → assertion → mock → verification  
B. Unit → controlled dependency → execute → assert/verify  
C. Mockito → production code → JUnit installation → compile  
D. Verification → dependency creation → requirement

**Answer: B**

### 27. What is the most important question before introducing Mockito?

A. Which Mockito annotation is longest?  
B. What problem does mocking solve?  
C. How many mocks can a class contain?  
D. Which IDE is being used?

**Answer: B**

### 28. Which is the strongest reason NOT to automatically mock every dependency?

A. Mockito cannot create mocks  
B. Mocking is a technique for solving a particular isolation/control problem, not a requirement for every test  
C. JUnit forbids multiple objects  
D. Assertions only work with real dependencies

**Answer: B**

### 29. A mock is configured to return `false` from `charge()`. What is the purpose?

A. To change the production implementation permanently  
B. To provide a controlled dependency behaviour for the test  
C. To prove `charge()` was called  
D. To disable JUnit

**Answer: B**

### 30. Final discrimination question: Which pairing is correct?

A. Stubbing → "Did it happen?" / Verification → "What should it return?"  
B. Stubbing → "What should it do?" / Verification → "Did the interaction happen?"  
C. Stubbing → "Should the test run?" / Verification → "Should the class compile?"  
D. Stubbing → "What is JUnit?" / Verification → "What is Mockito?"

**Answer: B**

**Explanation:** This distinction is one of the most important Mockito concepts in this session.
