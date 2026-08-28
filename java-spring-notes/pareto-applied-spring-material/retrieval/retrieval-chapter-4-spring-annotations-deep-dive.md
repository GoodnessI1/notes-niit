# Retrieval Practice — Chapter 4: Spring Annotations Deep Dive

> Exam-grade, theory-focused. Full chapter coverage. MCQ options are deliberately close to each other — read every option before choosing.

---

## Multiple Choice

**1. What does the `@Component` annotation actually do?**
A) Runs the class's methods immediately on startup
B) Registers the class as a bean to be managed by Spring
C) Compiles the class into bytecode
D) Deletes the class if it is unused

*Answer: B*

**2. What does `@Autowired` do?**
A) Automatically writes getter and setter methods
B) Finds a matching bean in the container and injects it
C) Creates a new instance every time the class is used
D) Marks a class as the application's entry point

*Answer: B*

**3. What is the correct sequence of events when Spring processes `@Component` and `@Autowired` together?**
A) Spring injects dependencies before creating any beans
B) Spring creates the beans first, then injects dependencies between them
C) Spring only creates beans; `@Autowired` has no effect without `@Bean`
D) Spring injects dependencies only if `@Configuration` is also present

*Answer: B*

**4. Which annotation marks a class as a configuration class where beans can be defined manually?**
A) `@Component`
B) `@Autowired`
C) `@Configuration`
D) `@Repository`

*Answer: C*

**5. When is `@Configuration` + `@Bean` preferred over `@Component`?**
A) Never — `@Component` should always be used instead
B) When registering classes from an external library that you don't own and can't annotate directly
C) Only when the class has no constructor
D) Only for classes with more than one method

*Answer: B*

**6. How many times does a `@Bean`-annotated method run, by default?**
A) Once per method call anywhere in the app
B) Once — the result is cached as a singleton
C) Once per class that autowires it
D) Every time the application context is queried

*Answer: B*

**7. A student writes `Student student = new Student();` for a class with an `@Autowired` field, instead of fetching it from the container. What happens to the autowired field?**
A) It is still correctly injected, because Spring watches all objects created anywhere
B) It remains `null`, because Spring never created this instance and cannot inject into it
C) A compile-time error occurs
D) Spring throws `NoSuchBeanDefinitionException`

*Answer: B*

**8. What is the effect of removing `@ComponentScan` from a `@Configuration` class, while `@Component` annotations remain on other classes?**
A) No effect — `@Component` alone is always sufficient
B) Spring never scans the project, so those classes are never registered as beans
C) The application fails to compile
D) Spring uses `@Bean` methods automatically as a fallback

*Answer: B*

**9. Which statement correctly distinguishes `@Component` from `@Bean`?**
A) They are functionally identical in every situation with no differences
B) `@Component` is a class-level annotation for automatic registration; `@Bean` is a method-level annotation for manual registration, typically inside a `@Configuration` class
C) `@Bean` can only be used with interfaces
D) `@Component` requires `@Autowired` to function; `@Bean` does not

*Answer: B*

**10. What error is thrown when a class was never registered as a bean (missing annotation or missing scan), but the code attempts to fetch it from the container?**
A) `NullPointerException`
B) `NoSuchBeanDefinitionException`
C) `ClassCastException`
D) `IllegalArgumentException`

*Answer: B*

**11. In the phrase "Spring can't inject what it didn't create," what does "create" refer to?**
A) Writing the `.java` source file
B) Spring instantiating the object itself, rather than the object being built with `new` outside the container
C) Compiling the class
D) Naming the bean

*Answer: B*

**12. Which of these correctly reflects why `@Bean` methods are useful for library classes you don't own?**
A) You can add `@Component` directly to library source code instead
B) You can't add annotations to code you don't control, but you can still register it manually via a `@Bean` method that returns an instance of it
C) Library classes are always automatically scanned regardless of annotations
D) `@Bean` is required for all classes, owned or not

*Answer: B*

---

## True or False

**13.** `@Component` alone is enough to register a bean, even if `@ComponentScan` is not covering that class's location.
*Answer: False — without component scanning reaching the class, Spring never discovers the `@Component` annotation, and the class is not registered.*

**14.** A `@Bean` method is called once, and its returned object is reused as a singleton by default.
*Answer: True — matching `@Component`'s default singleton behavior, unless the scope is explicitly changed.*

---

## Short Theory

**15. Explain the practical difference between `@Component` and `@Configuration` + `@Bean`, and give a scenario where the second approach is necessary.**
A: `@Component` is placed directly on a class you own, letting Spring detect and register it automatically during component scanning. `@Configuration` + `@Bean` is used when you define the bean manually inside a separate configuration class — this is necessary when the class comes from an external library and you cannot add `@Component` to its source code directly.

**16. A class has `@Autowired` on a field, but the object was created with `new` instead of being fetched from the Spring container. Explain what happens and why.**
A: The autowired field remains `null` (or causes a `NullPointerException` if used). This happens because Spring's dependency injection only applies to beans it creates and manages itself — creating an object with `new` bypasses the container entirely, so Spring never gets the opportunity to inject anything into it.

---

*12 MCQ, 2 True/False, 2 short theory — 16 questions total, full chapter coverage.*
