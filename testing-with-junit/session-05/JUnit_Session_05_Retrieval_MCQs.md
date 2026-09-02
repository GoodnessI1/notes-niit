# JUnit Retrieval — Session 5
## MCQ Exam Drill: Test Cases, Boundaries, Exceptions, and Parameterization

### 1. What is the most important question before selecting test values?
A. Which annotation has the shortest name?
B. Which values are most likely to reveal different behaviour?
C. How many assertions can fit in one method?
D. Whether the test class is public?

**Answer: B**  
**Explanation:** Test selection should focus on behaviour and meaningful distinctions.

### 2. A method returns `true` when `age >= 18`. Which value is the boundary?
A. 17
B. 18
C. 19
D. 25

**Answer: B**  
**Explanation:** Behaviour changes at 18.

### 3. Which set best tests the boundary around `age >= 18`?
A. 1, 5, 10
B. 18, 25, 40
C. 17, 18, 19
D. 20, 30, 40

**Answer: C**  
**Explanation:** Immediately below, at, and immediately above the boundary are highly informative.

### 4. What does `assertThrows` primarily verify?
A. That no exception occurs
B. That an expected exception type is thrown by an operation
C. That a test method is skipped
D. That two values are equal

**Answer: B**  
**Explanation:** `assertThrows` checks expected exceptional behaviour.

### 5. If an expected exception occurs and `assertThrows` verifies it, what happens?
A. The test must fail because an exception occurred
B. The test can pass because the exception was expected
C. The test is skipped
D. The test becomes parameterized

**Answer: B**  
**Explanation:** An expected exception can be the correct outcome.

### 6. In `assertThrows(Exception.class, () -> operation())`, what does the lambda represent?
A. The expected exception type
B. The test class
C. The operation expected to throw
D. The test result

**Answer: C**  
**Explanation:** The lambda contains the executable code being checked.

### 7. What should happen first?
A. Choose an annotation, then invent behaviour
B. Identify behaviour and meaningful cases, then choose a testing technique
C. Write assertions before understanding the method
D. Always use parameterized tests

**Answer: B**

### 8. Which distinction is correct?
A. Parameterized testing chooses meaningful cases automatically
B. Parameterized testing runs similar logic with different data; test design decides which data matters
C. Test design and parameterization are identical
D. Ordinary tests cannot test boundaries

**Answer: B**

### 9. What is a test case?
A. The entire JUnit framework
B. A particular situation used to verify expected behaviour
C. Only a lifecycle method
D. A Java package

**Answer: B**

### 10. What is a parameterized invocation?
A. The whole test class
B. One execution using one supplied argument set
C. The argument source itself
D. A lifecycle method

**Answer: B**

### 11. A withdrawal is valid when `amount > 0 && amount <= balance`. Which is a boundary case?
A. Amount equals balance
B. Any random large number
C. A random negative number only
D. A value chosen because it looks simple

**Answer: A**

### 12. Which set best explores the boundary around `amount <= balance`?
A. balance - 1, balance, balance + 1
B. 1, 2, 3
C. -10, -20, -30
D. 100, 200, 300 without knowing balance

**Answer: A**

### 13. Which statement is FALSE?
A. Parameterized tests can run multiple data sets
B. Boundary values can be especially informative
C. A test observing an expected exception must always fail
D. Test cases and test methods are not identical concepts

**Answer: C**

### 14. Which order represents stronger testing reasoning?
A. Annotation → assertion → guess input
B. Behaviour → meaningful cases → expected results → test implementation
C. Test runner → package → behaviour
D. Parameterization → behaviour → expected result

**Answer: B**

### 15. Why might `17, 18, 19` be better than `10, 20, 30` for testing `age >= 18`?
A. They contain more numbers
B. They directly examine where behaviour changes
C. JUnit requires consecutive numbers
D. Parameterized tests only accept three values

**Answer: B**

### 16. Parameterized testing answers how to:
A. choose meaningful behaviours
B. run similar test logic with different data
C. replace all ordinary tests
D. avoid assertions

**Answer: B**

### 17. Test design asks which:
A. annotation is newest
B. values and situations should be tested
C. IDE should run JUnit
D. lifecycle method runs first

**Answer: B**

### 18. A student writes ten random values but none at a known behavioural boundary. What is the main weakness?
A. Random values are illegal
B. The tests may miss where behaviour changes
C. The class will not compile
D. Parameterized tests require three values

**Answer: B**

### 19. Which is most likely to reveal an off-by-one defect?
A. A boundary-focused test
B. A random display name
C. A lifecycle method
D. A test interface

**Answer: A**

### 20. Which is NOT automatically solved by parameterized testing?
A. Reusing a test structure
B. Supplying multiple argument sets
C. Deciding which cases are meaningful
D. Running multiple invocations

**Answer: C**

### 21. A method has normal, boundary, and invalid inputs. What is the best approach?
A. Test only normal inputs
B. Identify the different behavioural categories and choose cases from them
C. Always write one test per line of code
D. Use only `@ValueSource`

**Answer: B**

### 22. Which relationship is most accurate?
A. One parameterized method → potentially multiple invocations/test cases
B. One parameterized method → exactly one test case
C. One invocation → multiple test methods
D. One boundary → one JUnit class

**Answer: A**

### 23. Which pairing is correct?
A. Parameterized test → chooses all important cases automatically
B. Boundary analysis → JUnit lifecycle feature
C. `assertThrows` → verifies expected exceptional behaviour
D. Test case → another name for `@BeforeEach`

**Answer: C**

### 24. Which statement best summarizes the session?
A. The most important thing is memorizing annotations
B. Good testing begins with understanding behaviour and selecting meaningful cases
C. Every test should be parameterized
D. Exceptions are always test failures

**Answer: B**

### 25. Final distinction: which sequence is correct?
A. Meaningful behaviour → useful cases → expected outcomes → appropriate JUnit technique
B. Annotation → random data → expected outcome
C. Parameterized test → discover behaviour afterward
D. Assertion → lifecycle → choose behaviour

**Answer: A**
