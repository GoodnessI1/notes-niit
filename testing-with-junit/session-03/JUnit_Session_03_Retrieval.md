# JUnit Retrieval — Session 3
## Test Lifecycle — MCQ Exam Drill

> Choose one answer before checking the explanation. Distractors are deliberately similar.

### 1. What does `@BeforeEach` indicate?

A. Once before all tests  
B. Before each test method  
C. After each test method  
D. A test that runs before every test

**Answer: B**

**Explanation:** `@BeforeEach` is a lifecycle method executed before each test invocation.

### 2. What does `@BeforeAll` indicate?

A. Before every test  
B. After every test  
C. Once before all tests in the class  
D. Before every assertion

**Answer: C**

**Explanation:** `BeforeAll` operates once at the test-class level.

### 3. Which is appropriate for common setup every test needs?

A. `@BeforeAll`  
B. `@AfterEach`  
C. `@BeforeEach`  
D. `@AfterAll`

**Answer: C**

**Explanation:** The requirement is before **each** test.

### 4. Which is appropriate for cleanup after every test?

A. `@BeforeEach`  
B. `@AfterEach`  
C. `@BeforeAll`  
D. `@AfterAll`

**Answer: B**

**Explanation:** `AfterEach` is per-test cleanup.

### 5. Which is appropriate for one-time cleanup after the entire class?

A. `@AfterEach`  
B. `@BeforeAll`  
C. `@AfterAll`  
D. `@BeforeEach`

**Answer: C**

**Explanation:** `AfterAll` runs once after all tests.

### 6. A class contains five test methods. How many times does `@BeforeEach` normally run?

A. Once  
B. Twice  
C. Five times  
D. Six times

**Answer: C**

**Explanation:** `Each` means once for every test.

### 7. The same class contains five test methods. How many times does `@BeforeAll` normally run?

A. Once  
B. Five times  
C. Once per assertion  
D. Once per test instance

**Answer: A**

**Explanation:** `All` means once for the whole test class.

### 8. Which sequence best represents one test?

A. `@BeforeEach → @AfterEach → @Test`  
B. `@Test → @BeforeEach → @AfterEach`  
C. `@BeforeEach → @Test → @AfterEach`  
D. `@BeforeAll → @AfterAll → @Test`

**Answer: C**

**Explanation:** The basic per-test sequence is setup, test, cleanup.

### 9. Which correctly distinguishes `@BeforeEach` and `@BeforeAll`?

A. BeforeEach runs before assertions; BeforeAll runs before tests  
B. BeforeEach runs before every test; BeforeAll runs once before all tests  
C. BeforeEach runs once; BeforeAll runs before every test  
D. They have the same purpose

**Answer: B**

**Explanation:** The key distinction is **each test** versus **all tests in the class**.

### 10. Which correctly distinguishes `@AfterEach` and `@AfterAll`?

A. AfterEach runs after every test; AfterAll runs once after all tests  
B. AfterEach runs once after the class; AfterAll runs after every test  
C. AfterEach runs before every test; AfterAll runs before the class  
D. They are aliases

**Answer: A**

**Explanation:** `Each` is per-test; `All` is per-class.

### 11. Which is a lifecycle method rather than a test method?

A. `@Test void shouldAddNumbers() { }`  
B. `@BeforeEach void setUp() { }`  
C. `@Test void shouldSubtractNumbers() { }`  
D. `@Test void shouldMultiplyNumbers() { }`

**Answer: B**

**Explanation:** `@BeforeEach` marks setup; `@Test` marks a test.

### 12. A student says "`@BeforeEach` is another test." Best correction?

A. Correct; every annotation creates a test  
B. Correct, but it cannot contain assertions  
C. Incorrect; it is a lifecycle method used for preparation before each test  
D. Incorrect; it is only used after tests

**Answer: C**

**Explanation:** Lifecycle methods and test methods have different roles.

### 13. Which is the best reason to use `@BeforeEach`?

A. Guarantee class execution order  
B. Put common preparation before every test  
C. Perform one expensive operation once  
D. Make one test depend on another

**Answer: B**

**Explanation:** `BeforeEach` centralizes repeated per-test setup.

### 14. Which is the best reason to use `@BeforeAll`?

A. Preparation once for the whole test class  
B. Reset state before every test  
C. Cleanup after every test  
D. Identify a test method

**Answer: A**

**Explanation:** `BeforeAll` is class-level setup.

### 15. Which statement about `@AfterEach` is most accurate?

A. Runs once after all tests  
B. Runs before each test  
C. Runs after each test invocation  
D. Identifies a test method

**Answer: C**

**Explanation:** `AfterEach` is per-test cleanup.

### 16. Which statement about `@AfterAll` is most accurate?

A. Runs after every test  
B. Runs once after all tests in the class  
C. Runs before all tests  
D. Runs before each test

**Answer: B**

**Explanation:** `AfterAll` is class-level cleanup.

### 17. Which pair has the same frequency?

A. `@BeforeEach` and `@AfterAll`  
B. `@BeforeAll` and `@AfterEach`  
C. `@BeforeEach` and `@AfterEach`  
D. `@BeforeAll` and `@BeforeEach`

**Answer: C**

**Explanation:** Both `Each` annotations run once around each test.

### 18. Which pair operates at whole-class level?

A. `@BeforeEach` and `@AfterEach`  
B. `@BeforeAll` and `@AfterAll`  
C. `@BeforeEach` and `@BeforeAll`  
D. `@AfterEach` and `@AfterAll`

**Answer: B**

**Explanation:** Both `All` annotations operate once around the class.

### 19. Consider:

```java
@BeforeEach
void setUp() {
    calculator = new Calculator();
}

@Test
void shouldAddNumbers() { }

@Test
void shouldSubtractNumbers() { }
```

What is most accurate?

A. `setUp()` runs once before the class  
B. `setUp()` runs before both test methods  
C. `setUp()` is itself a test  
D. `setUp()` runs only if a test fails

**Answer: B**

**Explanation:** `BeforeEach` executes before each test method.

### 20. Consider:

```java
@BeforeAll
static void setUpClass() { }

@Test
void testA() { }

@Test
void testB() { }
```

How many times does `setUpClass()` normally execute?

A. Zero  
B. Once  
C. Twice  
D. Once before every assertion

**Answer: B**

**Explanation:** `BeforeAll` executes once for the class.

### 21. A temporary resource must be prepared separately for every test and cleaned afterward. Best arrangement?

A. `@BeforeAll` + `@AfterAll`  
B. `@BeforeEach` + `@AfterEach`  
C. `@BeforeEach` + `@AfterAll`  
D. `@BeforeAll` + `@AfterEach`

**Answer: B**

**Explanation:** The resource has a per-test lifetime.

### 22. A resource should be created once before the class and destroyed once afterward. Best arrangement?

A. `@BeforeEach` + `@AfterEach`  
B. `@BeforeAll` + `@AfterAll`  
C. `@BeforeEach` + `@AfterAll`  
D. `@BeforeAll` + `@AfterEach`

**Answer: B**

**Explanation:** Both operations have class-level scope.

### 23. Which statement is NOT correct?

A. `@BeforeEach` runs before each test  
B. `@AfterEach` runs after each test  
C. `@BeforeAll` runs once before all tests  
D. `@AfterAll` runs after each test

**Answer: D**

**Explanation:** `@AfterAll` runs once after all tests.

### 24. Which statement is NOT correct?

A. `@BeforeEach` is per-test setup  
B. `@BeforeAll` is class-level setup  
C. `@AfterEach` is per-test cleanup  
D. `@AfterAll` identifies a test method

**Answer: D**

**Explanation:** `@AfterAll` identifies a lifecycle method.

### 25. By default, what is required of a `@BeforeAll` method in JUnit Jupiter?

A. `private`  
B. A return value  
C. `static`  
D. `@Test`

**Answer: C**

**Explanation:** By default, `@BeforeAll` methods must be static. citeturn0search3

### 26. By default, what is required of an `@AfterAll` method?

A. `static`  
B. `private`  
C. `@Test`  
D. It must run before every test

**Answer: A**

**Explanation:** By default, `@AfterAll` methods must be static. citeturn0search3

### 27. Which annotation fits an operation that must happen before every test?

A. `@BeforeAll`  
B. `@BeforeEach`  
C. `@AfterEach`  
D. `@AfterAll`

**Answer: B**

**Explanation:** The word **Each** is the deciding clue.

### 28. Which annotation fits an operation that must happen once before the entire class?

A. `@BeforeEach`  
B. `@AfterEach`  
C. `@BeforeAll`  
D. `@AfterAll`

**Answer: C**

**Explanation:** The word **All** indicates whole-class scope.

# High-Discrimination Questions

### 29. A temporary file must be deleted after every test. Which is best?

A. `@BeforeAll`  
B. `@BeforeEach`  
C. `@AfterEach`  
D. `@AfterAll`

**Answer: C**

**Explanation:** The requirement is **after every test**.

### 30. A test database must be created once before the class and removed after the class. Which pair is best?

A. `@BeforeEach` + `@AfterEach`  
B. `@BeforeAll` + `@AfterAll`  
C. `@BeforeEach` + `@AfterAll`  
D. `@BeforeAll` + `@AfterEach`

**Answer: B**

**Explanation:** Both operations have whole-class scope.

### 31. What do the words in these annotations tell you?

A. Before/After = frequency; Each/All = timing  
B. Before/After = timing; Each/All = scope/frequency  
C. Before/After = whether it is a test; Each/All = assertions  
D. They have no meaningful distinction

**Answer: B**

**Explanation:** `Before`/`After` tells you when; `Each`/`All` tells you the scope.

### 32. Which lifecycle sequence is most accurate for two tests?

A.
```text
@BeforeEach
@BeforeEach
@Test A
@Test B
@AfterEach
@AfterEach
```

B.
```text
@BeforeAll
@BeforeEach
@Test A
@AfterEach
@BeforeEach
@Test B
@AfterEach
@AfterAll
```

C.
```text
@BeforeEach
@Test A
@Test B
@AfterEach
@BeforeAll
@AfterAll
```

D.
```text
@BeforeAll
@Test A
@BeforeEach
@Test B
@AfterEach
@AfterAll
```

**Answer: B**

**Explanation:** Class setup surrounds the per-test setup/test/cleanup cycles, followed by class cleanup.

### 33. A student says "`@BeforeAll` is better because it avoids repeated setup." Best response?

A. Correct; `@BeforeEach` should never be used  
B. Correct; all setup should happen once  
C. Not necessarily; choose based on whether setup belongs to each test or the whole class  
D. Incorrect; both always execute equally often

**Answer: C**

**Explanation:** Frequency is not the only concern. Scope and test isolation matter.

### 34. Which best describes lifecycle methods?

A. They replace assertions  
B. They control preparation and cleanup around tests  
C. They are alternative names for tests  
D. They determine whether production code is correct

**Answer: B**

**Explanation:** Lifecycle methods manage the test environment; they do not replace the test or assertion.

# Final Retrieval

### 35. Which four annotations are the core lifecycle annotations covered here?

A. `@Test`, `@Mock`, `@Run`, `@Verify`  
B. `@BeforeEach`, `@AfterEach`, `@BeforeAll`, `@AfterAll`  
C. `@Setup`, `@Cleanup`, `@Test`, `@Assert`  
D. `@Before`, `@After`, `@Each`, `@All`

**Answer: B**

**Explanation:** These are the four core JUnit Jupiter lifecycle annotations for this session.

### 36. Complete the mapping:

```text
@BeforeEach → ?
@AfterEach  → ?
@BeforeAll  → ?
@AfterAll   → ?
```

A. before one / after one / before class / after class  
B. before each test / after each test / before all tests / after all tests  
C. before class / after class / before each test / after each test  
D. all four mean the same thing

**Answer: B**

**Explanation:** This is the central distinction of the session.

### 37. What is the most useful exam shortcut for these four annotations?

A. Ignore `Each` and `All`  
B. `Each` means every test; `All` means the whole class  
C. `Before` means setup and `After` means assertion  
D. All lifecycle methods run once

**Answer: B**

**Explanation:** **Each = every test. All = whole class.**

### 38. Which statement is most accurate?

A. `@BeforeEach` and `@BeforeAll` both run once  
B. `@AfterEach` and `@AfterAll` both run after tests, but one is per-test and one is per-class  
C. `@BeforeAll` is a test and `@BeforeEach` is an assertion  
D. `@AfterAll` runs after every test and after the class

**Answer: B**

**Explanation:** Both are after-hooks, but their scope differs.

### 39. Why is it important not to confuse lifecycle methods with test methods?

A. Lifecycle methods cannot contain Java code  
B. Lifecycle methods manage preparation/cleanup; test methods contain the behaviour being verified  
C. Lifecycle methods are always private  
D. Test methods cannot contain assertions

**Answer: B**

**Explanation:** They serve different roles in the test lifecycle.

### 40. A class has 3 tests. Ignoring failures and special behavior, which count is correct?

A. BeforeAll: 3, BeforeEach: 1, AfterEach: 1, AfterAll: 3  
B. BeforeAll: 1, BeforeEach: 3, AfterEach: 3, AfterAll: 1  
C. BeforeAll: 3, BeforeEach: 3, AfterEach: 1, AfterAll: 1  
D. All four execute 3 times

**Answer: B**

**Explanation:** `All` executes once for the class; `Each` executes once per test.
