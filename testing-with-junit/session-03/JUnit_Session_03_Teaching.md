# JUnit — Session 3
## Test Lifecycle and Setup/Teardown

### Session focus
Tests often need preparation and cleanup. JUnit provides lifecycle annotations so common setup/cleanup can happen automatically around tests.

## 1. The problem

Without lifecycle methods:

```java
@Test
void shouldAddNumbers() {
    Calculator calculator = new Calculator();
    // ...
}

@Test
void shouldSubtractNumbers() {
    Calculator calculator = new Calculator();
    // ...
}
```

The same preparation is repeated.

JUnit lets us move common preparation into lifecycle methods.

## 2. `@BeforeEach`

```java
class CalculatorTest {

    Calculator calculator;

    @BeforeEach
    void setUp() {
        calculator = new Calculator();
    }

    @Test
    void shouldAddNumbers() {
        assertEquals(5, calculator.add(2, 3));
    }

    @Test
    void shouldSubtractNumbers() {
        assertEquals(3, calculator.subtract(5, 2));
    }
}
```

`@BeforeEach` means:

> Run this method before each test method.

JUnit Jupiter defines `@BeforeEach` as a lifecycle annotation for a method executed before each test invocation. citeturn0search3

**Important:** `@BeforeEach` is not a test. It is a lifecycle method used for preparation.

## 3. `@AfterEach`

```java
@AfterEach
void tearDown() {
    // cleanup
}
```

Meaning:

> Run this method after each test method.

Typical uses include clearing temporary state, closing resources, removing test data, or resetting resources. citeturn0search3

## 4. The per-test lifecycle

```text
@BeforeEach
     ↓
   @Test
     ↓
@AfterEach
```

For several tests:

```text
@BeforeEach → @Test A → @AfterEach
@BeforeEach → @Test B → @AfterEach
@BeforeEach → @Test C → @AfterEach
```

## 5. `@BeforeAll`

Sometimes preparation should happen once for the entire test class:

```java
@BeforeAll
static void setUpClass() {
    // one-time setup
}
```

Meaning:

> Run once before all tests in the class.

By default, a JUnit Jupiter `@BeforeAll` method must be `static`. citeturn0search3

## 6. `@AfterAll`

```java
@AfterAll
static void tearDownClass() {
    // one-time cleanup
}
```

Meaning:

> Run once after all tests in the class.

By default, `@AfterAll` methods must also be `static`. citeturn0search3

## 7. The four annotations

| Annotation | When? | Frequency |
|---|---|---|
| `@BeforeEach` | Before each test | Once per test |
| `@AfterEach` | After each test | Once per test |
| `@BeforeAll` | Before all tests | Once per class |
| `@AfterAll` | After all tests | Once per class |

### The key distinction

```text
Each → every test
All  → whole test class
```

## 8. Full lifecycle

```text
@BeforeAll
    ↓
@BeforeEach
    ↓
@Test
    ↓
@AfterEach
    ↓
@BeforeEach
    ↓
@Test
    ↓
@AfterEach
    ↓
@AfterAll
```

So:

- `BeforeAll` → once
- `BeforeEach` → before every test
- `AfterEach` → after every test
- `AfterAll` → once

## 9. Practical example

For database tests:

```java
@BeforeAll
static void createDatabase() {
    // create test database
}

@BeforeEach
void insertTestData() {
    // prepare data for this test
}

@Test
void shouldFindUser() {
    // test user lookup
}

@AfterEach
void deleteTestData() {
    // remove test data
}

@AfterAll
static void destroyDatabase() {
    // remove test database
}
```

The annotations describe **when** each operation belongs in the lifecycle.

## 10. `BeforeEach` vs `BeforeAll`

If there are five test methods:

`@BeforeEach` runs five times.

`@BeforeAll` runs once.

Likewise:

`@AfterEach` runs five times.

`@AfterAll` runs once.

## 11. Lifecycle methods vs test methods

```java
@BeforeEach
void setUp() {
    calculator = new Calculator();
}

@Test
void shouldAddNumbers() {
    assertEquals(5, calculator.add(2, 3));
}
```

Here:

```text
setUp()
→ lifecycle method

shouldAddNumbers()
→ test method
```

The annotation determines the role.

## 12. Why `@BeforeEach` supports isolation

JUnit Jupiter's default test-instance lifecycle creates a new instance of the test class for each test method. This supports isolated execution and avoids unexpected side effects from mutable test-instance state. citeturn0search3

The practical principle is:

> Prepare each test independently rather than making tests depend on previous tests.

## 13. Session takeaway

```text
@BeforeAll
    = once before all tests

@BeforeEach
    = before every test

@Test
    = the test itself

@AfterEach
    = after every test

@AfterAll
    = once after all tests
```

The central distinction is:

> **Each ≠ All**

For this session, do not go deeply into extensions, nested lifecycle inheritance, custom test-instance lifecycles, or advanced ordering. Those can come later.
