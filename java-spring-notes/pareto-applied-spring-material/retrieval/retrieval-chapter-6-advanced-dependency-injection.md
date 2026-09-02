# Retrieval Practice — Chapter 6: Advanced Dependency Injection

> Exam-grade, theory-focused. Full chapter coverage. All options are deliberately similar in length and plausibility — there is no shortcut by "spotting the detailed one." You need the concept, not the pattern.

---

## Multiple Choice

**1. What happens when Spring finds two beans matching a single `@Autowired` field, with no qualifier or primary specified?**
A) Spring injects the first bean found alphabetically by class name
B) Spring throws `NoUniqueBeanDefinitionException` because it cannot determine which bean to inject
C) Spring injects both matching beans into a list automatically
D) Spring falls back to whichever bean has the shorter class name

*Answer: B*

**2. What does `@Qualifier` accomplish that `@Autowired` alone cannot?**
A) It creates a fresh instance of the bean every time it is requested
B) It specifies exactly which of several matching beans should be injected
C) It removes the requirement that the bean implement a common interface
D) It allows two beans of unrelated types to be injected into the same field

*Answer: B*

**3. How does `@Primary` resolve ambiguity between multiple beans of the same type?**
A) It marks one bean as the default choice, used only when no more specific selector is provided
B) It permanently disables all other beans of that type for the rest of the application
C) It merges the competing beans into a single combined object at startup
D) It forces every injection point in the project to use `@Qualifier` instead

*Answer: A*

**4. A bean is marked `@Primary`, but a field also carries a `@Qualifier` pointing to a different bean of the same type. Which one gets injected?**
A) The `@Primary` bean always wins, regardless of any qualifier present
B) The bean named by `@Qualifier` is injected, overriding the `@Primary` default
C) Spring throws an exception because the two annotations conflict with each other
D) Whichever bean was registered first during component scanning

*Answer: B*

**5. By default, what name does Spring assign to a bean created from a class such as `JavaCourse`?**
A) The fully qualified class name, including its package path
B) `javaCourse` — the class name with its first letter lowercased
C) A generated identifier unrelated to the original class name
D) The name of the interface the class implements, lowercased

*Answer: B*

**6. What is the central criticism of field injection raised in this chapter?**
A) It executes more slowly at runtime than constructor injection does
B) The dependency requirement isn't visible anywhere in the class's public contract
C) It is incompatible with classes that implement an interface
D) It requires an additional dependency in the build file to function

*Answer: B*

**7. Why does removing a class's no-args constructor make its dependency mandatory "at the language level," independent of Spring?**
A) Spring inserts a runtime check that blocks object creation without dependencies
B) Plain Java itself will not compile a call to a constructor that doesn't exist
C) The JVM refuses to load any class that lacks a no-args constructor
D) Annotations are evaluated at compile time and enforce the rule directly

*Answer: B*

**8. What specifically makes constructor injection easier to test than field injection?**
A) A fully working object can be built directly with `new`, supplying any implementation, entirely outside the Spring container
B) Constructor injection automatically generates mock objects for use in test classes
C) Constructor injection is the only style that testing frameworks are able to recognize
D) Constructor injection prevents a class from being instantiated more than once per run

*Answer: A*

**9. Since Spring 4.3, under what condition is `@Autowired` optional on a constructor?**
A) Only when the constructor itself takes zero parameters
B) When the class has exactly one constructor
C) Only when the class is also marked `@Primary`
D) `@Autowired` is never optional, in any version of Spring

*Answer: B*

**10. In the "Combining Concepts" example, why is placing `@Qualifier` directly on a constructor parameter considered good practice?**
A) It combines the safety of constructor injection with the precision of explicit bean selection, in one place
B) It removes the requirement for the class itself to be marked `@Component`
C) It allows the constructor to accept beans of mismatched, unrelated types
D) It is required syntax — `@Qualifier` cannot legally be placed anywhere else

*Answer: A*

**11. In the build-along where `new Student(new PythonCourse())` was constructed directly, what did this actually demonstrate?**
A) That Spring silently intercepts every `new` call and manages the object anyway
B) That a fully functional object can be assembled by hand, with any implementation, with no Spring container involved at all
C) That constructor injection requires at least one Spring annotation to compile successfully
D) That `PythonCourse` cannot be used unless it is also marked `@Primary`

*Answer: B*

**12. What is the relationship between `@Qualifier` and Spring's default bean-naming convention?**
A) `@Qualifier` requires a special prefix distinct from the bean's registered name
B) `@Qualifier` matches against the bean's registered name, which by default is the class name with a lowercased first letter
C) `@Qualifier` only works for beans defined via `@Bean` methods, never `@Component`
D) `@Qualifier` ignores bean names entirely and matches purely by type

*Answer: B*

---

## True or False

**13.** When both `@Qualifier` and `@Primary` apply to the same injection point, `@Qualifier` takes precedence over `@Primary`.
*Answer: True — this was directly confirmed in the build-along comparing the two.*

**14.** Field injection makes a dependency requirement visible directly in a class's constructor signature.
*Answer: False — that describes constructor injection; field injection hides the requirement inside `@Autowired`-marked fields, not in any public-facing signature.*

---

## Short Theory

**15. Explain, using the compiler as part of your answer, why constructor injection prevents a class from ever existing in an incompletely-wired state.**
A: If a class's only constructor requires a dependency as a parameter, then any attempt to instantiate that class without supplying the dependency fails to compile — Java itself rejects the code before it can run. This makes the dependency mandatory at the language level, not just as a convention Spring enforces at runtime.

**16. A teammate marks two different implementations of the same interface both with `@Primary`. Explain what problem this creates and why.**
A: `@Primary` is meant to name a single default bean to use when no other selector is specified. If two beans of the same type are both marked `@Primary`, Spring again has no way to determine which one should be treated as the default — the ambiguity that `@Primary` was meant to resolve returns, and Spring is likely to throw an exception related to multiple candidates, the same class of problem `@Qualifier`/`@Primary` exist to prevent in the first place.

---

*12 MCQ, 2 True/False, 2 short theory — 16 questions total, full chapter coverage.*
