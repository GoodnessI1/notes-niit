# Retrieval Practice — Chapter 2: Dependency Injection (Deep Dive) & IoC in Practice

> Exam-grade, theory-focused. Full chapter coverage. MCQ options are deliberately close to each other — read every option before choosing.

---

## Multiple Choice

**1. Which of the following best defines Dependency Injection?**
A) A pattern where a class creates its own dependencies inside a static method
B) A technique where a class receives its dependencies from an external source instead of creating them itself
C) A Spring-only feature with no equivalent in plain Java
D) A method for reducing the number of classes in an application

*Answer: B*

**2. Which type of Dependency Injection is generally recommended as the default/best practice?**
A) Field Injection
B) Setter Injection
C) Constructor Injection
D) Static Injection

*Answer: C*

**3. What is the main risk associated with Setter Injection?**
A) It requires a Spring container to compile
B) The setter may never be called, leaving the object in an incomplete state
C) It cannot be used with interfaces
D) It always creates a new object on every call

*Answer: B*

**4. Which of these is NOT one of the three types of Dependency Injection covered in this chapter?**
A) Constructor Injection
B) Setter Injection
C) Field Injection
D) Interface Injection

*Answer: D*

**5. Why is Field Injection difficult to demonstrate in plain Java, without a framework like Spring?**
A) Java does not allow fields inside classes
B) There is no clean way to assign a value into the field from outside the class without a setter or reflection
C) Fields cannot hold references to interfaces
D) Field Injection only works with primitive types

*Answer: B*

**6. Which statement most accurately distinguishes IoC from DI?**
A) IoC and DI are two names for exactly the same thing, with no distinction
B) IoC is a specific Java annotation; DI is a design principle
C) IoC is the principle of giving up control of object creation; DI is the technique used to implement that principle
D) IoC only applies to Spring; DI only applies to plain Java

*Answer: C*

**7. In the "Manual DI in plain Java" example, which class plays the role that the Spring container will later play?**
A) `Engine`
B) `ElectricEngine`
C) `Car`
D) `MainApp`

*Answer: D*

**8. Which of the following is a genuine advantage of Constructor Injection over Field Injection?**
A) Constructor Injection runs faster at runtime
B) Constructor Injection makes the dependency mandatory and visible in the class's public contract
C) Constructor Injection does not require an interface
D) Constructor Injection is the only type Spring supports

*Answer: B*

**9. A student says: "DI only exists because of Spring." Which response correctly addresses this misconception?**
A) They are correct — DI was invented by the Spring team
B) DI works in plain Java without any framework; Spring only automates it
C) DI is a database concept unrelated to Spring
D) DI cannot be demonstrated without annotations

*Answer: B*

**10. Which of these is one of the four stated benefits of Dependency Injection in this chapter?**
A) Reduced file size
B) Easier testing
C) Faster compilation
D) Automatic error handling

*Answer: B*

**11. In the Setter Injection build-along, what error occurs if `setEngine()` is never called before `drive()` is invoked?**
A) A compile-time error
B) `NullPointerException` at runtime
C) The program silently does nothing
D) `NoSuchBeanDefinitionException`

*Answer: B*

**12. Which of the following correctly completes this sentence: "DI is a way to achieve ___."**
A) Encapsulation
B) Polymorphism
C) IoC
D) Inheritance

*Answer: C*

---

## True or False

**13.** Setter Injection guarantees that a dependency will always be set before the object is used.
*Answer: False — nothing forces the setter to be called; the object can be used in an incomplete state.*

**14.** Manual Dependency Injection in plain Java, without Spring, is a valid and functioning way to achieve loose coupling.
*Answer: True — Chapter 2's `MainApp` example demonstrates DI working correctly with no framework involved.*

---

## Short Theory

**15. Explain, in your own words, why Constructor Injection is considered safer than Field Injection.**
A: Constructor Injection forces the dependency to be provided at the moment the object is created — the object cannot exist in an incomplete state, and the dependency is visible directly in the constructor signature. Field Injection allows an object to be constructed without its dependency ever being set, and the requirement is hidden inside the class body rather than visible from outside.

**16. State the relationship between IoC and DI, and explain why they are not the same thing.**
A: IoC is the broader principle — shifting responsibility for object creation away from a class to an external system. DI is the specific technique used to implement that principle by having a class receive its dependencies rather than create them. IoC could in theory be achieved by other mechanisms; DI is the concrete method used in this course and in Spring.

---

*12 MCQ, 2 True/False, 2 short theory — 16 questions total, full chapter coverage.*
