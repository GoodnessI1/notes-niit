# Retrieval Practice — Chapter 5: Bean Scope & Lifecycle

> Exam-grade, theory-focused. Full chapter coverage. MCQ options are deliberately close to each other — read every option before choosing.

---

## Multiple Choice

**1. What does "bean scope" define?**
A) How many methods a bean can have
B) How many instances of a bean Spring creates
C) How long a bean's source file can be
D) Which package a bean belongs to

*Answer: B*

**2. What is the default scope of a Spring bean?**
A) Prototype
B) Request
C) Singleton
D) Session

*Answer: C*

**3. In Singleton scope, if a bean is fetched from the container twice, what is true of the two references?**
A) They point to two different objects with identical values
B) They point to the exact same object in memory
C) The second fetch always throws an exception
D) They point to different objects only if the class has fields

*Answer: B*

**4. In Prototype scope, if a bean is fetched directly from the container twice, what is true of the two references?**
A) They point to the exact same object
B) They point to two different, freshly created objects
C) The first one is destroyed automatically
D) Prototype beans cannot be fetched more than once

*Answer: B*

**5. A prototype-scoped bean is injected as a field into a singleton-scoped bean. If that singleton is fetched from the container multiple times, how many distinct instances of the prototype bean will it ever hold?**
A) A new instance every time the singleton is fetched
B) Exactly one — resolved once, at the moment the singleton itself was created
C) Zero — Spring refuses to inject prototype beans into singletons
D) It depends on how many methods the prototype class has

*Answer: B*

**6. Why does injecting a prototype bean into a singleton NOT produce a fresh instance on every use, even though prototype scope normally means "a new object every time"?**
A) Because prototype scope is ignored inside singletons entirely
B) Because Spring only resolves and injects the dependency once — at the point the singleton bean itself is created
C) Because prototype beans are converted to singletons automatically once injected
D) Because `@Autowired` overrides `@Scope`

*Answer: B*

**7. Which of the following correctly lists Spring bean lifecycle stages, in order?**
A) Destroyed → Created → Initialized
B) Created → Dependencies injected → Init method runs → Bean used → Destroy method runs
C) Initialized → Created → Destroyed
D) Bean used → Created → Destroyed → Initialized

*Answer: B*

**8. Which annotation marks a method to run automatically right after a bean is created and its dependencies are injected?**
A) `@PreDestroy`
B) `@Component`
C) `@PostConstruct`
D) `@Bean`

*Answer: C*

**9. Which annotation marks a method to run automatically just before a bean is destroyed?**
A) `@PostConstruct`
B) `@PreDestroy`
C) `@Autowired`
D) `@Scope`

*Answer: B*

**10. What must be called on the application context for `@PreDestroy` to actually fire?**
A) `context.refresh()`
B) `context.getBean()`
C) `context.close()`
D) Nothing — it fires automatically when `main` ends

*Answer: C*

**11. Does Spring manage the destruction of prototype-scoped beans the same way it manages singleton beans?**
A) Yes, identically — `@PreDestroy` fires for both
B) No — Spring creates prototype beans but does not manage their destruction
C) No — Spring refuses to create prototype beans at all
D) Yes, but only if `context.close()` is called twice

*Answer: B*

**12. When is Singleton scope the appropriate choice, according to this chapter?**
A) When the object holds changing state specific to each individual user
B) When the object represents shared behavior/logic with no per-user changing state
C) Only for classes with no methods
D) Only when the class implements an interface

*Answer: B*

---

## True or False

**13.** Fetching a prototype-scoped bean directly from the container twice always returns two different object instances.
*Answer: True — this is the defining behavior of prototype scope when fetched directly from the container.*

**14.** If `context.close()` is never called, `@PreDestroy` methods will still run automatically when the program finishes.
*Answer: False — without closing the context, Spring never triggers the destroy phase, and `@PreDestroy` does not run.*

---

## Short Theory

**15. Explain why injecting a prototype-scoped bean into a singleton-scoped bean results in only one instance ever being used by that singleton, even across multiple fetches of the singleton itself.**
A: Spring resolves a bean's dependencies once, at the moment that bean is created. Since the singleton is only ever created once, its prototype-scoped dependency is only ever injected once — the singleton holds onto that single instance for its entire lifetime, regardless of how many times the singleton itself is later fetched from the container.

**16. Describe the full order of the Spring bean lifecycle, including the two lifecycle annotations covered in this chapter.**
A: A bean is created (constructor runs) → its dependencies are injected → its `@PostConstruct`-annotated method runs as initialization → the bean is used by the application → its `@PreDestroy`-annotated method runs just before it is destroyed, typically triggered by closing the application context.

---

*12 MCQ, 2 True/False, 2 short theory — 16 questions total, full chapter coverage.*
