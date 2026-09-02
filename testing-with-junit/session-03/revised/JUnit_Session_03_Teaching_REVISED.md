# JUnit — Session 3
## Test Lifecycle and Setup/Teardown

*Instructor note: Session 3 of 18 — early in the course. Scaffolding is fully explicit: one step at a time, exact "say this / type this / expect this" sequences. Don't chunk yet, except where flagged below.*

### Session focus
Students have been writing `Calculator` tests since Session 1, repeating `Calculator calculator = new Calculator();` in every test. This session shows them how to automate that repetition — and *proves*, by running real code, exactly when JUnit's lifecycle methods fire, instead of just stating the order.

---

## 1. The problem (recap, tie-back to Sessions 1–2)

**Say:** "Since Session 1, every test you've written for Calculator starts the same way — look at your own code."

Have students look at their own earlier Calculator tests (or show on screen):

```java
@Test
void shouldAddNumbers() {
    Calculator calculator = new Calculator();
    assertEquals(5, calculator.add(2, 3));
}

@Test
void shouldSubtractNumbers() {
    Calculator calculator = new Calculator();
    assertEquals(3, calculator.subtract(5, 2));
}
```

**Ask:** "What line appears in both tests, doing the exact same thing?"

Expected answer: `Calculator calculator = new Calculator();`

**Say:** "JUnit gives us a way to write that line once and have it run automatically before every test. Let's build it."

---

## 2. Build-along: `@BeforeEach`

Fully explicit — one step at a time.

**Step 1 — Say:** "Move the `calculator` field out of the test methods, to the top of the class."

**Type:**
```java
class CalculatorTest {
    Calculator calculator;
}
```

**Step 2 — Say:** "Above a new method called `setUp`, write `@BeforeEach`."

**Type:**
```java
    @BeforeEach
    void setUp() {
        calculator = new Calculator();
    }
```

**Step 3 — Say:** "Now delete the `Calculator calculator = new Calculator();` line from inside both test methods — we don't need it there anymore."

**Expected result:**
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

**Step 4 — Run it.** Both tests pass.

**Note:** "`setUp()` is not a test — it never appears in the results as its own passed/failed item. It's a lifecycle method: preparation JUnit runs *for* the tests."

---

## 3. Prove it: does `@BeforeEach` really run before *every* test?

Don't just state it — make students watch it happen.

**Step 1 — Say:** "Add a line to `setUp()` that prints something."

**Type:**
```java
    @BeforeEach
    void setUp() {
        System.out.println("setUp running");
        calculator = new Calculator();
    }
```

**Step 2 — Predict before you run it:** "We have two `@Test` methods. Before you run this — how many times do you think 'setUp running' will print? Write your prediction down."

**Step 3 — Run it.**

**Expected output:**
```
setUp running
setUp running
```

**Ask:** "Was your prediction right? Why twice, not once?"

Expected answer: because `setUp()` runs before *each* test — two tests, two runs.

---

## 4. `@AfterEach` — same pattern, first light chunking

*Scaffolding note: students just built `@BeforeEach` fully step-by-step. This is a good place for the first soft chunk (two steps combined instead of four) — flag to me live if the class isn't ready and I'll re-split it.*

**Say:** "Add an `@AfterEach` method called `tearDown`, with a print statement — same idea as `setUp`, but it runs after."

**Type:**
```java
    @AfterEach
    void tearDown() {
        System.out.println("tearDown running");
    }
```

**Run it. Expected output (order matters):**
```
setUp running
tearDown running
setUp running
tearDown running
```

**Note:** "Notice the pattern: setUp → test → tearDown, repeated for every test — not all setUps first, then all tests, then all tearDowns."

---

## 5. The problem `@BeforeEach` can't solve

**Say:** "Now imagine Calculator keeps a history of every calculation, and — for the whole test class — we want to write that history to one shared log file, created once, not recreated before every single test."

**Ask:** "If we put file-creation logic inside `@BeforeEach`, what happens?"

Expected answer: the file would get recreated before every test — wasteful, and wrong if we want one shared file for the whole class.

**Say:** "This is what `@BeforeAll` and `@AfterAll` are for — setup and cleanup that happens once for the whole class, not once per test."

---

## 6. Build-along: `@BeforeAll`

**Step 1 — Say:** "Add a `@BeforeAll` method. Print something in it."

**Type:**
```java
    @BeforeAll
    static void setUpAll() {
        System.out.println("setUpAll running");
    }
```

**Step 2 — Run it immediately**, before adding `@AfterAll`. This is where the pitfall lives — don't pre-empt it.

---

## 7. Prove the pitfall: `@BeforeAll` must be `static`

**Say:** "Let's deliberately break this. Remove the word `static` from `setUpAll`."

**Type:**
```java
    @BeforeAll
    void setUpAll() {   // static removed on purpose
        System.out.println("setUpAll running");
    }
```

**Run it.** Let students read the real error JUnit produces on screen (something to the effect of *"@BeforeAll method ... must be static"*) — don't paraphrase it for them first.

**Ask:** "What does the error actually say? Read it out loud."

**Fix it:** put `static` back, run again, confirm it passes.

**Note:** "You'll forget this at least once while writing real tests later. Now you've seen what the error looks like and why it happens — not just that it happens."

---

## 8. Predict before you run it: the full order

**Say:** "Now add `@AfterAll` — same pattern, static method, print statement."

**Type:**
```java
    @AfterAll
    static void tearDownAll() {
        System.out.println("tearDownAll running");
    }
```

**Before running:** "We now have `@BeforeAll`, `@BeforeEach`, two `@Test`s, `@AfterEach`, and `@AfterAll`, all printing. Write down, in order, what you think will print."

**Run it. Expected output:**
```
setUpAll running
setUp running
tearDown running
setUp running
tearDown running
tearDownAll running
```

**Ask:** "Compare your prediction to the real output. Where did you get it wrong, if anywhere?" Most common mistake: assuming `@BeforeAll` and `@BeforeEach` both run before *every* test.

---

## 9. The four annotations — reference table

Now that students have proven the order themselves, this table is a lookup, not new information.

| Annotation | When? | Frequency | Must be static? |
|---|---|---|---|
| `@BeforeAll` | Before all tests in the class | Once per class | Yes |
| `@BeforeEach` | Before each test | Once per test | No |
| `@AfterEach` | After each test | Once per test | No |
| `@AfterAll` | After all tests in the class | Once per class | Yes |

**Say:** "You don't need to memorize this — you just watched it happen. It's here for quick reference."

---

## 10. Full Calculator class, for reference

```java
class CalculatorTest {

    Calculator calculator;

    @BeforeAll
    static void setUpAll() {
        System.out.println("setUpAll running");
    }

    @BeforeEach
    void setUp() {
        System.out.println("setUp running");
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

    @AfterEach
    void tearDown() {
        System.out.println("tearDown running");
    }

    @AfterAll
    static void tearDownAll() {
        System.out.println("tearDownAll running");
    }
}
```

---

## 11. Session takeaway

**Say:** "Each ≠ All. `Each` means every single test. `All` means once for the whole class. You didn't just hear that — you watched it print."

*Scope note: don't go into extensions, nested lifecycle inheritance, custom test-instance lifecycles, or advanced ordering this session. Those come later.*

---

## What changed and why

- **Collapsed four redundant order-diagrams** (old §4, §8, §10, §13 — all prose restatements of the same fact) **into two build-along blocks** (§3 and §8 above), where students print, predict, then run — proving the order instead of reading it four times.
- **Kept `Calculator` as the running example throughout**, including the "why do we need `@BeforeAll`" motivation (§5), instead of switching to an unbuilt database scenario. Old §9's database example was cut entirely — it was comments-only, never actually runnable, and broke continuity for no payoff.
- **Added a real pitfall build-along for the `static` requirement on `@BeforeAll`** (§7) — the old material only stated the rule ("By default... must be static"); now students cause the error, read it, and fix it.
- **Added explicit tie-back to Sessions 1–2** (§1) — this session now visibly automates something students already did by hand, instead of opening as a disconnected new topic.
- **Scaffolding stayed fully explicit** throughout, appropriate for Session 3 of 18 — with one soft first chunk flagged at §4 (`@AfterEach`) as a natural, low-risk place to start the fade, since it directly repeats a pattern just built step-by-step. Flag to me if that's premature for your class.
- **Added a "must be static?" column** to the reference table (§9), since that's now something students proved for themselves rather than were told.
