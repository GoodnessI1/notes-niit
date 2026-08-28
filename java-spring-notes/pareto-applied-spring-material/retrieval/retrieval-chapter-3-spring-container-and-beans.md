# Retrieval Practice — Chapter 3: Spring Container & Beans

> Exam-grade, theory-focused. Full chapter coverage. MCQ options are deliberately close to each other — read every option before choosing.

---

## Multiple Choice

**1. What is the Spring Container responsible for?**
A) Only compiling Java source files
B) Creating, managing, and injecting objects (beans)
C) Only storing configuration files
D) Generating database tables

*Answer: B*

**2. Which annotation tells Spring to scan the project for classes to manage as beans?**
A) `@Bean`
B) `@Autowired`
C) `@ComponentScan`
D) `@Service`

*Answer: C*

**3. What is a Bean, precisely?**
A) Any class that has a constructor
B) Any object created and managed by the Spring Container
C) Any class stored in a `.java` file
D) Any object that implements an interface

*Answer: B*

**4. A plain Java class with no `@Component` (or similar) annotation, in a project with component scanning enabled — is it a Bean?**
A) Yes, all classes automatically become beans once Spring is added to a project
B) No, Spring is unaware of it because nothing tells Spring to scan or manage it
C) Yes, but only if it has a `main` method
D) It depends on whether the class has a constructor

*Answer: B*

**5. Which container type uses lazy loading (only creates beans when needed)?**
A) `ApplicationContext`
B) `AnnotationConfigApplicationContext`
C) `BeanFactory`
D) `ComponentScanner`

*Answer: C*

**6. Which container type is the most commonly used in real-world Spring applications?**
A) `BeanFactory`
B) `ApplicationContext`
C) `ComponentScan`
D) `MainApp`

*Answer: B*

**7. What exception is thrown when code attempts to fetch a bean for a class Spring was never told to manage?**
A) `NullPointerException`
B) `ClassNotFoundException`
C) `NoSuchBeanDefinitionException`
D) `IllegalStateException`

*Answer: C*

**8. Which of the following correctly orders the high-level Spring flow?**
A) Spring creates beans → you define classes → Spring scans → Spring injects dependencies
B) You define classes → Spring scans → Spring creates beans → Spring injects dependencies
C) Spring injects dependencies → Spring scans → you define classes → Spring creates beans
D) You define classes → Spring creates beans → Spring injects dependencies → Spring scans

*Answer: B*

**9. What does `ApplicationContext` do differently from `BeanFactory` regarding when beans are created?**
A) `ApplicationContext` never creates beans automatically
B) `ApplicationContext` loads beans eagerly; `BeanFactory` loads them lazily
C) Both load beans at exactly the same time
D) `ApplicationContext` only creates beans when the program exits

*Answer: B*

**10. A student says: "Spring automatically knows about every class in my project." Why is this incorrect?**
A) It's actually true — no correction is needed
B) Spring only manages what is explicitly configured or annotated (e.g. with `@Component`)
C) Spring only manages classes with a `main` method
D) Spring only manages classes stored in a specific folder named `beans`

*Answer: B*

**11. What is the correct annotation combination for a class that acts as the entry point for Spring's component scanning?**
A) `@Component` and `@Autowired`
B) `@Configuration` and `@ComponentScan`
C) `@Bean` and `@Service`
D) `@Repository` and `@Primary`

*Answer: B*

**12. In the Laptop exercise, what makes `Laptop` become a recognized Bean?**
A) Giving it a constructor
B) Adding a `main` method to it
C) Adding the `@Component` annotation to the class
D) Renaming the file to match the class name

*Answer: C*

---

## True or False

**13.** `BeanFactory` and `ApplicationContext` behave identically, and the distinction between them does not matter in practice.
*Answer: False — `ApplicationContext` is more powerful and far more commonly used; the two differ in loading behavior (eager vs. lazy) and available features.*

**14.** A class must have `@Component` (or a related annotation) for the Spring Container to create and manage it as a bean.
*Answer: True — without such an annotation, Spring has no way of knowing the class should be managed, and it is not scanned in.*

---

## Short Theory

**15. Explain what the Spring Container actually does, in your own words, using the analogy given in this chapter.**
A: The Spring Container acts like a factory and manager for all the objects in an application — it creates objects, injects their dependencies, manages their lifecycle, and configures how they relate to one another, so the developer doesn't have to do this manually.

**16. A class is annotated `@Component` but the project has no `@ComponentScan` (or it is not scanning the right package). Will the class become a bean? Explain why or why not.**
A: No. `@Component` only marks a class as eligible to be managed by Spring — it does not register the class on its own. Without `@ComponentScan` covering the class's location, Spring never scans and finds it, so it is never created as a bean, even though the annotation is present.

---

*12 MCQ, 2 True/False, 2 short theory — 16 questions total, full chapter coverage.*
