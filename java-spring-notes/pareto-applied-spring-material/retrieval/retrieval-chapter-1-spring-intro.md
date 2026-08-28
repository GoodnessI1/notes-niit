# Retrieval Practice — Chapter 1: Getting Started with Spring & Design Patterns

> Theory-focused, exam-grade Q&A. Covers the full chapter. No code-writing required — matches the theory emphasis of most Spring exams.

---

**Q1. Define tight coupling.**
A: A situation where one class is directly and strongly dependent on a specific other class, typically because it creates that dependency itself (e.g. with `new`) rather than receiving it from outside.

**Q2. State two problems caused by tight coupling.**
A: Any two of: hard to change (modifying one class forces changes in dependent classes), hard to test (can't substitute a mock/fake object), low flexibility (rigid, brittle code), poor maintainability (changes ripple across the codebase).

**Q3. What happens to a tightly coupled class if the class it depends on changes?**
A: The dependent class must also be modified, since it directly references the specific implementation.

**Q4. Define Dependency Injection (DI).**
A: A technique where a class receives its dependencies from an external source rather than creating them itself.

**Q5. State the key principle behind Dependency Injection, as a single line.**
A: "Don't create what you need — receive it."

**Q6. What is the difference between a class creating its own dependency versus receiving it via a constructor?**
A: Creating its own dependency (e.g. `Engine engine = new Engine();`) results in tight coupling, since the class is locked to that specific implementation. Receiving it via a constructor allows the class to work with any implementation that satisfies the required type, since the responsibility for creating the object shifts outside the class.

**Q7. What does "programming to an interface" mean?**
A: Depending on a general type (an interface or abstract class) rather than a specific concrete implementation, so the dependent class does not need to know or care which exact implementation it is given.

**Q8. Why is programming to an interface useful?**
A: It allows behavior to be changed or extended without modifying the existing class that depends on it — new implementations can be introduced and swapped in freely.

**Q9. Define Inversion of Control (IoC).**
A: The transfer of responsibility for object creation away from a class itself to an external system.

**Q10. Explain the relationship between IoC and DI.**
A: DI is the technique used to implement IoC — IoC is the concept/principle ("give up control of object creation"), and DI is the specific mechanism ("receive dependencies from outside") used to achieve it.

**Q11. Before DI is applied, who is "in control" of object creation? After it is applied, who is?**
A: Before: the class itself is in control, creating its own dependencies. After: an external system (initially the calling code, later the Spring container) is in control.

**Q12. Define Spring, as a one-line definition suitable for an exam answer.**
A: Spring is a framework that manages objects and their dependencies in a Java application, implementing IoC and DI automatically.

**Q13. List the four things Spring does automatically, as covered in this chapter.**
A: 1) Creates objects (Beans); 2) Connects them via Dependency Injection; 3) Manages their lifecycle; 4) Configures application structure.

**Q14. What is the Spring Container?**
A: The part of Spring responsible for creating, holding, and wiring together the objects (beans) in an application.

**Q15. Name the two main types of Spring Container discussed and give one distinguishing feature of each.**
A: `BeanFactory` — the basic container, lightweight, loads beans lazily. `ApplicationContext` — the full-featured container, extends `BeanFactory`, used in most real applications.

**Q16. Define a Bean.**
A: Any object that is created and managed by the Spring Container.

**Q17. List the three stages of the bean lifecycle covered in this chapter, in order.**
A: Created (Spring instantiates the object via its constructor) → Initialized (Spring injects dependencies and runs setup logic) → Destroyed (Spring cleans up the object when it is no longer needed).

**Q18. Name two design patterns that Spring is built on, and state where each is used within Spring.**
A: Any two of: Factory Pattern (the Spring container creates objects for you); Dependency Injection (Spring wires dependencies between beans); Template Pattern (used in classes like `JdbcTemplate`, `RestTemplate`); Proxy Pattern / AOP (Spring wraps beans to add cross-cutting concerns).

**Q19. Why does Spring use the Proxy Pattern?**
A: To wrap beans and add cross-cutting concerns (such as logging, security, or transactions) without modifying the bean's own code — this is the basis of Aspect-Oriented Programming (AOP) in Spring.

**Q20. Summarize, in one or two sentences, why Spring exists.**
A: Spring exists to automate the manual work of creating objects, wiring their dependencies, and managing their lifecycle — work that would otherwise have to be done by hand, as shown by writing that logic manually before Spring is introduced.

---

*20 questions — full chapter coverage. Recommend running this as spaced retrieval (not all at once): a first pass right after the chapter, then a second pass a few days later, then a final pass before any assessment.*
