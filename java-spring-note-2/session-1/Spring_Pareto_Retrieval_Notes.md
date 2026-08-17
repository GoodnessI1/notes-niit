# Spring Framework — Pareto Retrieval & Retention Bank

## Purpose

This document is the retrieval companion to the instructor Teaching Notes.

Its purpose is not simply to ask students to remember definitions.

It is designed to make students repeatedly retrieve:

1. **Facts**
2. **Mental models**
3. **Relationships between concepts**
4. **Differences between similar concepts**
5. **Code behavior**
6. **Troubleshooting reasoning**
7. **Application/transfer**
8. **Exam-style discrimination**

Older concepts deliberately return throughout the bank.

---

# Retrieval Difficulty Ladder

## Level 1 — Recall

Can the student retrieve the concept?

## Level 2 — Explain

Can the student explain it without repeating a memorized definition?

## Level 3 — Distinguish

Can the student tell similar concepts apart?

## Level 4 — Code Reasoning

Can the student predict what code will do?

## Level 5 — Troubleshooting

Can the student diagnose a broken Spring application?

## Level 6 — Exam Simulation

Can the student select the best answer among plausible distractors?

---

# How to Use This Bank

Do **not** reveal the answers immediately.

Use:

```text
Question
↓
Student retrieves
↓
Student explains reasoning
↓
Answer
↓
Correct misconception
```

For oral retrieval, ask the student to answer before looking at the answer.

For written retrieval, require a short answer before showing the key.

---

# PART I — DEPENDENCIES

## Level 1 — Recall

### Q1
What is a dependency in an object-oriented application?

**Answer:**  
A dependency is another object/component that a class needs in order to perform its responsibility.

### Q2
In `OrderService` using `OrderRepository`, which is the dependency?

**Answer:**  
`OrderRepository` is the dependency of `OrderService`.

---

## Level 2 — Explain

### Q3
Why should a service care about the repository it needs without necessarily constructing the repository itself?

**Answer:**  
Because the service needs the repository to perform its responsibility, but construction and selection of that dependency can be handled elsewhere. This reduces coupling and improves replaceability and testability.

---

## Level 3 — Distinguish

### Q4
What is the difference between a dependency and dependency injection?

**Answer:**  
A dependency is something an object needs. Dependency injection is a mechanism/design approach in which that dependency is supplied from outside rather than constructed internally.

---

# PART II — TIGHT COUPLING

## Level 1

### Q5
What is the problem with this?

```java
class OrderService {
    private OrderRepository repository =
        new MySqlOrderRepository();
}
```

**Answer:**  
`OrderService` directly constructs a specific implementation, coupling the service to that implementation.

### Q6
Who creates the repository in the example above?

**Answer:**  
`OrderService` creates it.

---

## Level 3 — Distinguish

### Q7
Which statement is more accurate?

A. Tight coupling means two classes can never communicate.

B. Tight coupling means one component has a strong dependency on a specific implementation/detail.

**Answer:**  
**B.**

---

# PART III — DEPENDENCY INJECTION

## Level 1

### Q8
What is dependency injection?

**Answer:**  
Dependency injection is supplying an object's dependencies from outside the object rather than having the object construct those dependencies itself.

### Q9
What are two common forms of dependency injection covered in Spring?

**Answer:**  
Constructor-based injection and setter-based injection.

---

## Level 2

### Q10
Why does constructor injection make a dependency explicit?

**Answer:**  
The dependency appears in the constructor required to create the object, making the object's required collaborators visible at construction time.

---

## Level 3 — Distinguish

### Q11
DI vs IoC: which is broader?

**Answer:**  
IoC is the broader principle. Dependency injection is one mechanism/pattern for achieving inversion of control.

### Q12
Does `@Autowired` itself define the concept of dependency injection?

**Answer:**  
No. Dependency injection is the underlying design concept. `@Autowired` is one Spring mechanism for facilitating dependency resolution/injection.

---

## Level 4 — Code Reasoning

### Q13
What does this class expect?

```java
class UserService {
    private final UserRepository repository;

    UserService(UserRepository repository) {
        this.repository = repository;
    }
}
```

**Answer:**  
It expects a `UserRepository` to be supplied when the `UserService` is constructed.

---

# PART IV — INVERSION OF CONTROL

## Level 1

### Q14
What does inversion of control mean at a high level?

**Answer:**  
Control over some application operations—such as object creation and management—is transferred from application code to an external framework/container.

### Q15
How does DI relate to IoC?

**Answer:**  
DI is a mechanism through which control over supplying dependencies is moved outside the dependent object.

---

## Level 3 — Distinguish

### Q16
Which statement is best?

A. IoC and DI are completely unrelated.

B. IoC is the broader principle and DI is one way to implement it.

C. DI is broader than IoC.

**Answer:**  
**B.**

---

# PART V — SPRING CONTAINER

## Level 1

### Q17
What is the Spring container?

**Answer:**  
It is the Spring mechanism responsible for managing application objects/beans, including their creation, configuration and dependency relationships.

### Q18
Why is the container important to dependency injection?

**Answer:**  
The container provides/manages the objects that can be injected and resolves their dependencies.

---

## Level 2

### Q19
A student says: "Spring injects an object from nowhere."

What should you correct?

**Answer:**  
The object is not coming from nowhere. Spring manages objects as beans in its container and resolves the dependency from the available configuration/bean definitions.

---

# PART VI — BEANS

## Level 1

### Q20
What is a Spring bean?

**Answer:**  
An object managed by the Spring container.

### Q21
Is every Java object automatically a Spring bean?

**Answer:**  
No. An object must participate in Spring's bean configuration/registration mechanisms to be managed as a Spring bean.

---

## Level 3 — Distinguish

### Q22
Object vs bean: what is the key distinction?

**Answer:**  
An object is a Java instance. A bean is an object that is managed by the Spring container.

### Q23
Bean vs container: what is the difference?

**Answer:**  
A bean is a managed object; the container is the mechanism that manages beans.

---

# PART VII — APPLICATIONCONTEXT

### Q24
What role does `ApplicationContext` play?

**Answer:**  
It is a major Spring application context/container abstraction used to manage application components, configuration and beans.

### Q25
Why should a beginner understand the container before memorizing `ApplicationContext`?

**Answer:**  
Because otherwise `ApplicationContext` becomes vocabulary without a mental model. Understanding the container explains why the context exists.

---

# PART VIII — CONFIGURATION AND `@BEAN`

## Level 1

### Q26
Why does Spring need configuration?

**Answer:**  
Spring needs information about what objects/components should be managed and how the application object graph should be constructed.

### Q27
What is the purpose of a method annotated with `@Bean`?

**Answer:**  
It explicitly declares an object to be managed as a Spring bean.

---

## Level 3 — Distinguish

### Q28
`@Component` vs `@Bean`: what is the major distinction?

**Answer:**  
`@Component` marks a class for component scanning/discovery. `@Bean` explicitly declares a bean through a configuration method.

### Q29
Which is more explicit about constructing the returned object?

A. `@Component`

B. `@Bean`

**Answer:**  
**B. `@Bean`.**

---

# PART IX — COMPONENT SCANNING

### Q30
What problem does component scanning help solve?

**Answer:**  
It allows Spring to discover eligible component classes without requiring every component to be explicitly registered through individual configuration methods.

### Q31
Name common stereotype annotations.

**Answer:**  
`@Component`, `@Service`, `@Repository`, and `@Controller`.

---

## Level 3 — Distinguish

### Q32
Are `@Service` and `@Component` completely unrelated?

**Answer:**  
No. `@Service` is a specialized stereotype used to express a service-layer role while participating in component scanning.

### Q33
Why should students learn the shared mechanism before memorizing the individual stereotypes?

**Answer:**  
Because the important mental model is Spring discovering/managing components. The annotations communicate participation and conventional role.

---

# PART X — AUTOWIRING AND DEPENDENCY RESOLUTION

### Q34
What problem does autowiring address?

**Answer:**  
It helps Spring resolve and supply a dependency from the available Spring-managed beans.

### Q35
What happens conceptually if Spring has exactly one suitable bean for a dependency?

**Answer:**  
Spring can resolve that bean as the dependency.

---

## Level 3 — Ambiguity

### Q36
Suppose Spring has:

```text
OrderRepository
    ├── MySqlOrderRepository
    └── InMemoryOrderRepository
```

A service needs an `OrderRepository`.

What problem might occur?

**Answer:**  
There may be multiple candidates, so Spring may not know which bean should satisfy the dependency.

### Q37
Why are qualifiers/disambiguation mechanisms needed?

**Answer:**  
They allow the application to specify which candidate should satisfy an ambiguous dependency.

---

# PART XI — BEAN LIFECYCLE

### Q38
Put these broad lifecycle stages in a sensible order:

- initialization
- creation
- destruction
- dependency injection

**Answer:**

```text
Creation
↓
Dependency injection
↓
Initialization
↓
Destruction
```

### Q39
Why does lifecycle matter?

**Answer:**  
Because Spring manages more than merely constructing an object; it can control aspects of the bean's lifecycle.

---

# PART XII — ARCHITECTURE

### Q40
Complete the common beginner application flow:

```text
Controller
   ↓
?
   ↓
Repository
```

**Answer:**  
Service.

### Q41
Why should a controller generally not contain all business logic?

**Answer:**  
Separating responsibilities improves organization, maintainability, testing and architectural clarity.

---

## Cumulative Retrieval

### Q42
How does DI support this architecture?

**Answer:**  
The controller can depend on a service, and the service can depend on a repository. Spring can manage and inject these collaborators rather than each class constructing them itself.

---

# PART XIII — SPRING BOOT

### Q43
What problem does Spring Boot primarily address?

**Answer:**  
It simplifies the setup and development of Spring applications through conventions, auto-configuration and related application infrastructure.

### Q44
Is Spring Boot the same thing as the entire Spring Framework?

**Answer:**  
No. Spring Boot is built around Spring and simplifies Spring application development; Spring is the broader framework/ecosystem.

---

## Level 3 — Distinguish

### Q45
Which is the better statement?

A. Spring Boot replaces the concepts of dependency injection and the Spring container.

B. Spring Boot makes it easier to build and configure applications using Spring.

**Answer:**  
**B.**

---

# PART XIV — REST / WEB

### Q46
What is the basic role of a controller?

**Answer:**  
It handles web/application requests at the controller layer and coordinates with application logic.

### Q47
What is the common flow?

**Answer:**

```text
HTTP request
    ↓
Controller
    ↓
Service
    ↓
Repository
    ↓
Data
```

---

## Level 3 — Distinguish

### Q48
Why is `@RestController` likely to be confused with `@Controller`?

**Answer:**  
Both identify controller-layer components, but their handling of web responses differs; `@RestController` is intended for REST-style response bodies.

---

# PART XV — DATA ACCESS

### Q49
What problem does `JdbcTemplate` help solve?

**Answer:**  
It reduces repetitive JDBC data-access/resource-management plumbing.

### Q50
What is the role of a repository/data-access component?

**Answer:**  
It handles persistence/data-access responsibilities rather than application/business logic.

---

## Cumulative

### Q51
Why does DI help testing a service that uses a repository?

**Answer:**  
The repository dependency can be replaced or controlled during testing rather than the service constructing a concrete repository itself.

---

# PART XVI — TESTING

### Q52
What is the connection between DI and testability?

**Answer:**  
DI separates object construction from object use, making it easier to substitute dependencies during tests.

### Q53
A service creates its repository with `new`.

Why can this complicate unit testing?

**Answer:**  
The service is tightly coupled to that concrete construction, making dependency replacement more difficult.

---

# PART XVII — PROXIES AND AOP

### Q54
What is a proxy conceptually?

**Answer:**  
An intermediary object that can sit between the caller and the target object and intercept/decorate behavior.

```text
Caller
  ↓
Proxy
  ↓
Target
```

### Q55
What problem does AOP address?

**Answer:**  
Cross-cutting concerns that would otherwise become scattered or tangled across many components.

### Q56
Give examples of cross-cutting concerns.

**Answer:**  
Logging, security, transaction-related behavior, timing, auditing, and similar concerns.

---

## Level 3 — Distinguish

### Q57
Dependency injection vs AOP: are they solving the same problem?

**Answer:**  
No. DI primarily addresses how dependencies are supplied and managed. AOP addresses cross-cutting behavior that can be applied around operations.

---

# PART XVIII — CACHING

### Q58
What problem does caching solve?

**Answer:**  
It can avoid repeatedly performing expensive operations by reusing previously obtained results when appropriate.

### Q59
What is the conceptual flow of a cache lookup?

**Answer:**

```text
Request
 ↓
Cache?
 ├─ hit → return cached result
 └─ miss → execute operation → store result
```

---

# PART XIX — MICROSERVICES

### Q60
Why should microservices be introduced after students understand a single application?

**Answer:**  
Students need a baseline architecture before they can understand what is gained, lost and complicated by distributing that architecture across services.

### Q61
Name several characteristics associated with microservices.

**Answer:**  
Service autonomy, independent deployment, defined service boundaries, lightweight architecture, and distributed communication.

### Q62
What is a major danger of teaching microservices as simply "small applications"?

**Answer:**  
Students miss distributed-system concerns such as communication, deployment, resilience, observability and operational complexity.

---

# PART XX — REACTIVE PROGRAMMING

### Q63
What broad problem does reactive programming address?

**Answer:**  
It addresses application designs involving responsiveness, non-blocking processing, scalability and asynchronous streams of data/events.

### Q64
What is back-pressure?

**Answer:**  
A mechanism for preventing a producer from overwhelming a consumer by allowing demand to influence the rate at which data is produced/processed.

---

# PART XXI — SPRING CLOUD

### Q65
Why might a microservice ecosystem need service discovery?

**Answer:**  
Services need a mechanism to locate other services dynamically rather than relying entirely on fixed addresses.

### Q66
What problem does an API Gateway address?

**Answer:**  
It can provide a centralized entry point for clients and handle concerns such as routing and other cross-cutting gateway responsibilities.

---

# PART XXII — DOCKER

### Q67
Image vs container: what is the distinction?

**Answer:**  
An image is a packaged template/artifact used to create containers. A container is a running/instantiated environment based on an image.

### Q68
What does a Dockerfile generally describe?

**Answer:**  
Instructions for building a container image.

---

# PART XXIII — HIGH-VALUE COMPARISON QUESTIONS

These should recur repeatedly because they are likely to generate confusion and exam mistakes.

## Q69 — DI vs IoC

Which is broader?

**Answer:** IoC.

Which is a mechanism/pattern for supplying dependencies?

**Answer:** DI.

---

## Q70 — Bean vs Object

Is every object a bean?

**Answer:** No.

Is every bean an object?

**Answer:** A bean is a Spring-managed object/instance.

---

## Q71 — Bean vs Container

Which is managed?

**Answer:** The bean.

Which manages beans?

**Answer:** The container.

---

## Q72 — `@Component` vs `@Bean`

Which identifies a class for component scanning?

**Answer:** `@Component`.

Which explicitly declares a bean through a configuration method?

**Answer:** `@Bean`.

---

## Q73 — Spring vs Spring Boot

Which is the broader ecosystem/framework?

**Answer:** Spring.

Which simplifies Spring application setup?

**Answer:** Spring Boot.

---

## Q74 — Controller vs Service

Which should primarily handle web request concerns?

**Answer:** Controller.

Which should primarily contain application/business logic?

**Answer:** Service.

---

## Q75 — Service vs Repository

Which coordinates business/application operations?

**Answer:** Service.

Which handles persistence/data access?

**Answer:** Repository.

---

## Q76 — DI vs AOP

Which addresses dependency management?

**Answer:** DI.

Which addresses cross-cutting behavior?

**Answer:** AOP.

---

## Q77 — Image vs Container

Which is the reusable packaged artifact?

**Answer:** Image.

Which is the instantiated running environment?

**Answer:** Container.

---

# PART XXIV — CODE REASONING

## Q78

```java
class PaymentService {
    private PaymentRepository repository;

    PaymentService() {
        repository = new PaymentRepository();
    }
}
```

What design problem should you identify?

**Answer:**  
The service directly constructs its dependency, creating tight coupling and making dependency substitution/testing harder.

---

## Q79

```java
class PaymentService {
    private final PaymentRepository repository;

    PaymentService(PaymentRepository repository) {
        this.repository = repository;
    }
}
```

What changed conceptually?

**Answer:**  
The dependency is supplied from outside rather than constructed internally.

---

## Q80

A class is annotated with `@Service`.

What does that communicate to Spring?

**Answer:**  
It identifies the class as a service-layer component eligible for Spring component scanning/management.

---

## Q81

Two beans implement the same interface.

A service requests the interface type.

What concept should you immediately think about?

**Answer:**  
Ambiguous dependency resolution and the need for disambiguation.

---

# PART XXV — TROUBLESHOOTING

## Q82

Spring cannot resolve a dependency.

What should you investigate before randomly changing annotations?

**Answer:**

1. Is the required dependency registered as a bean?
2. Is Spring discovering it?
3. Is component scanning/configuration correct?
4. Is the dependency type correct?
5. Are there multiple candidates?
6. If multiple candidates exist, has the ambiguity been resolved?

---

## Q83

A student says:

> "I added `@Autowired`, so Spring should automatically fix everything."

What is wrong with this reasoning?

**Answer:**  
Injection requires an appropriate dependency/bean to exist and be discoverable/resolvable. The annotation does not manufacture an arbitrary dependency from nowhere.

---

# PART XXVI — EXAM-STYLE MCQs

## Q84

Which best describes dependency injection?

A. Creating every dependency with `new`.

B. Supplying an object's dependencies from outside the object.

C. Preventing objects from having dependencies.

D. Replacing Java classes with Spring classes.

**Answer:** B.

---

## Q85

Which concept is most directly associated with managing Spring beans?

A. Spring container

B. Docker registry

C. HTTP controller

D. JDBC driver

**Answer:** A.

---

## Q86

Which statement best distinguishes IoC from DI?

A. DI is broader than IoC.

B. They are unrelated.

C. IoC is the broader principle; DI is one mechanism for achieving it.

D. IoC only applies to databases.

**Answer:** C.

---

## Q87

Which annotation is commonly used to explicitly declare a bean through a configuration method?

A. `@Bean`

B. `@Service`

C. `@Autowired`

D. `@Repository`

**Answer:** A.

---

## Q88

A Spring application has two beans that both satisfy a required interface. What issue can result?

A. Serialization

B. Ambiguous dependency resolution

C. Docker failure

D. REST routing

**Answer:** B.

---

## Q89

Which sequence best represents the conceptual architecture?

A. Repository → Controller → Service

B. Controller → Service → Repository

C. Service → Controller → Repository

D. Repository → Service → Controller

**Answer:** B.

---

## Q90

Which problem is AOP primarily designed to address?

A. Dependency creation

B. Database schema design

C. Cross-cutting concerns

D. Container image creation

**Answer:** C.

---

# PART XXVII — CUMULATIVE RETRIEVAL CYCLES

Do not teach a concept once and retire its questions.

Use cumulative retrieval.

## Cycle A — After Foundations

Ask students to connect:

- dependency
- coupling
- DI
- IoC
- container
- bean

### Challenge

> Explain the complete chain from a plain Java dependency to Spring-managed dependency injection without using the words "magic" or "annotation."

**Answer:**  
An object has a dependency. Instead of constructing it directly, the dependency can be supplied externally. This is dependency injection and reflects inversion of control. Spring's container manages the relevant objects as beans and resolves/provides the dependencies.

---

## Cycle B — After Configuration

Retrieve everything from Cycle A plus:

- `@Bean`
- `@Component`
- component scanning
- autowiring
- ambiguity

### Challenge

> Explain how Spring can go from a class definition to an injected dependency.

**Answer:**  
Spring discovers/registers eligible components or explicit bean definitions, manages the resulting objects as beans, resolves their dependencies and injects the appropriate beans.

---

## Cycle C — After Architecture

Retrieve:

- DI
- container
- beans
- configuration
- component scanning
- autowiring
- controller
- service
- repository

### Challenge

> Why is DI useful in a Controller → Service → Repository architecture?

**Answer:**  
Each component can declare its collaborators while Spring manages and supplies them. This reduces direct construction/coupling and supports separation of responsibility and testing.

---

## Cycle D — After Spring Boot

Retrieve all previous concepts plus:

- Spring vs Spring Boot
- auto-configuration
- application startup
- REST

### Challenge

> If Spring Boot simplifies configuration, did it eliminate the Spring container?

**Answer:**  
No. Spring Boot simplifies application setup and configuration around Spring; the underlying Spring concepts and container remain fundamental.

---

## Cycle E — After Data Access

Retrieve:

- architecture
- DI
- repository
- JdbcTemplate
- testing

### Challenge

> Why does DI make a service easier to test?

**Answer:**  
Its repository dependency can be supplied with a test double/mock/fake rather than forcing the service to construct a concrete persistence implementation.

---

## Cycle F — After AOP

Retrieve:

- DI
- container
- beans
- proxies
- AOP
- cross-cutting concerns

### Challenge

> A student says "AOP and DI are both just ways Spring creates objects." Correct them.

**Answer:**  
That statement confuses two different concerns. DI concerns supplying/managing dependencies; AOP concerns applying cross-cutting behavior, often through proxies/interception.

---

# PART XXVIII — WEEKLY RETENTION RULE

Every later retrieval session should contain approximately:

```text
20–30%  very recent material
40–50%  material from earlier sessions
20–30%  mixed application/exam questions
```

The exact percentages can be adjusted after classroom evidence.

The principle is more important than the number:

> **Old knowledge must keep being retrieved.**

---

# PART XXIX — THE STUDENT EXPLANATION TEST

For major concepts, ask the student to explain using this structure:

```text
1. What problem exists?
2. Why is it a problem?
3. What concept solves it?
4. How does Spring implement/use the concept?
5. What would happen without it?
6. What similar concept could be confused with it?
```

If the student can answer all six, they are demonstrating more than definition recall.

---

# PART XXX — FINAL EXAM PREPARATION MODE

As the course approaches the examination, retrieval should shift toward discrimination.

Questions should increasingly contain:

- plausible distractors
- similar terminology
- short code snippets
- "which is most accurate?"
- "what happens if?"
- configuration errors
- architecture decisions
- differences between annotations
- differences between Spring concepts

The goal is not merely:

> "Have you seen this topic?"

The goal is:

> **"Can you retrieve the right concept and discriminate it from a plausible wrong concept under pressure?"**

---

# Instructor Reminder

A student who can recognize "`@Autowired`" is not necessarily demonstrating understanding.

A stronger test is:

> "This class has two candidate beans. What happens, why, and how would you resolve it?"

The retrieval system should therefore progressively move:

```text
Recall
  ↓
Explain
  ↓
Distinguish
  ↓
Predict
  ↓
Troubleshoot
  ↓
Apply
  ↓
Exam discrimination
```

That progression is the retention architecture for the course.
