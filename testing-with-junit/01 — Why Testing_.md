# 01 — Why Testing?

## 1. What Is Software Testing?

Software testing is the process of checking whether software behaves as expected.

The fundamental question behind testing is:

> **What should this thing do?**

Because if we don't know what something should do, we cannot determine whether it works.

Testing is essentially a comparison:

```text
Expected Behaviour
        VS
Actual Behaviour
```

If the actual behaviour matches what we expected → ✅ **Pass**

If it does not → ❌ **Fail**

---

## 2. Why Do We Test?

Software can behave incorrectly even when the code looks correct.

Testing gives us a systematic way to check important behaviours and detect defects.

We test to:

- Detect defects.
- Verify expected behaviour.
- Catch regressions when code changes.
- Reduce risk.
- Increase confidence in the software.
- Repeat checks automatically.

### The key idea

> **We test to reduce the risk that our software behaves incorrectly.**

---

## 3. Before Automated Testing

Imagine testing an application manually.

A developer would:

1. Run the code.
2. Check the result.
3. Decide whether it is correct.
4. Record the result.
5. Repeat the process whenever the code changes.

This becomes increasingly difficult as software grows.

```text
Manual testing
      ↓
Slow and repetitive
      ↓
Easy to forget things
      ↓
Difficult to repeat consistently
```

---

## 4. What JUnit Changes

JUnit allows developers to describe what they expect their code to do and automatically check those expectations.

Instead of:

```text
Developer checks results
Developer decides pass/fail
Developer reruns tests
Developer tracks failures
```

we can have:

```text
Developer defines expectations
            ↓
        JUnit runs tests
            ↓
     Computer checks results
            ↓
         Pass / Fail
```

JUnit therefore helps make testing:

- **Automated**
- **Repeatable**
- **Organized**

---

# 5. Verification vs Validation

Testing is not only about asking whether the software works. We also need to consider whether we built the right thing.

### Verification

> **"Did we build it right?"**

Verification checks whether the software conforms to its specified requirements.

For example:

```text
Requirement:
A calculator should return 4 when given 2 + 2.

Test:
2 + 2 → 4
```

We are checking whether the implementation behaves according to the requirement.

### Validation

> **"Did we build the right thing?"**

Validation asks whether the software actually meets the user's or business's needs.

For example:

A requirement might say:

> "Users should be able to delete their account."

We can verify that the delete-account feature works correctly.

But whether users **should** have that feature in the first place is a validation/business question.

### The distinction

```text
VERIFICATION
Did we build it right?
        ↓
Does it meet the requirements?

VALIDATION
Did we build the right thing?
        ↓
Does it meet the user's/business need?
```

### Where does JUnit fit?

JUnit is primarily used to help with **verification**.

It can check whether our code behaves according to defined expectations.

It cannot, by itself, determine whether those expectations represent the right product or business requirement.

---

# 6. What Is a Test?

A test is a piece of code whose job is to verify another piece of code.

> **Production code solves business problems.**  
> **Test code verifies production code.**

A test is simply another Java class that calls our production code and checks whether it behaves as expected.

```text
Test
 ↓
Calls production code
 ↓
Gets actual result
 ↓
Compares with expectation
 ↓
Pass / Fail
```

---

# 7. The Psychology Behind Testing

When testing, we are not simply asking:

> "Does this code look correct?"

We are asking:

> **"Does reality match my expectation?"**

For example:

```text
Expectation:
2 + 2 should produce 4

Actual:
2 + 2 produces 4

Result:
✅ Pass
```

If the program instead produced `5`:

```text
Expectation:
4

Actual:
5

Result:
❌ Fail
```

This idea — **Expected Behaviour vs Actual Behaviour** — is the foundation of testing.

---

# 8. Testing Principles

### 1. Testing requires expectations

You must know what the software is supposed to do before you can test it.

### 2. Testing is comparison

Testing compares:

```text
Expected Behaviour
        VS
Actual Behaviour
```

### 3. Testing is about risk

We test to reduce the risk that our software behaves incorrectly.

### 4. Testing cannot prove correctness

Passing tests increase confidence, but they cannot guarantee that the entire system is correct.

### 5. Testing shows the presence of defects, not their absence

If a test fails, we have evidence that something is wrong.

If all our tests pass, we only know that the behaviours covered by those tests passed.

---

# 9. The Big Picture

The entire idea can be reduced to this:

```text
             WHAT SHOULD HAPPEN?
                      ↓
                 EXPECTATION
                      ↓
                Run the code
                      ↓
                 ACTUAL RESULT
                      ↓
               Compare the two
                      ↓
              ┌───────┴───────┐
              ↓               ↓
           Matches         Doesn't
              ↓               ↓
           PASS             FAIL
```

And JUnit helps us automate this process.

### Key Takeaways

> **Testing is comparing expected behaviour with actual behaviour.**

> **Verification asks: "Did we build it right?"**

> **Validation asks: "Did we build the right thing?"**

> **JUnit primarily helps us verify software behaviour.**

> **Testing increases confidence and reduces risk; it does not prove that software is completely correct.**