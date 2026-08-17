# Spring Framework — Pareto Teaching Notes

## Purpose

This document is the instructor-facing teaching guide for the Spring course.

It is **not** a chapter-by-chapter reproduction of the supplied material. The sequence is organized around **conceptual dependency and learning leverage**.

The governing principle is:

> Teach the smallest set of mental models that unlocks the largest amount of Spring understanding.

The sequence should eventually be mapped onto the 18 sessions, but **session allocation is intentionally left for a later phase**.

---

# Part I — The Teaching Sequence

## Sequence at a Glance

```text
1. Plain Java Objects and Dependencies
        ↓
2. The Problem: Tight Coupling and Manual Construction
        ↓
3. Dependency Injection
        ↓
4. Inversion of Control
        ↓
5. The Spring Container
        ↓
6. Beans and ApplicationContext
        ↓
7. Configuration and Bean Registration
        ↓
8. Component Scanning and Stereotypes
        ↓
9. Autowiring and Dependency Resolution
        ↓
10. Bean Lifecycle and Scope
        ↓
11. Application Architecture with Spring
        ↓
12. Spring Boot: Removing Application Plumbing
        ↓
13. Web/Application Layer and REST
        ↓
14. Data Access and JdbcTemplate
        ↓
15. Testing Spring Applications
        ↓
16. Proxies and AOP
        ↓
17. Cross-Cutting Concerns: Caching, Security, Monitoring
        ↓
18. Microservices and Distributed Systems
        ↓
19. Reactive Programming / WebFlux
        ↓
20. Spring Cloud and Infrastructure
        ↓
21. Docker and Deployment Awareness
```

This is a **conceptual sequence**, not a commitment to equal depth.

---

# Part II — How to Teach Every Concept

Use this recurring pattern:

> **Problem → Why → Concept → Mechanism → Code → Practice → Retrieval**

Do not begin with annotation memorization.

For Spring beginners, repeatedly answer:

1. What problem existed?
2. Why was it a problem?
3. What idea solves it?
4. What does Spring actually do?
5. How does the code express that idea?
6. What happens if we remove/change something?
7. Can the student use the idea without copying?
8. Can the student distinguish it from a similar concept?

---

# 1. Plain Java Objects and Dependencies

## Teaching objective

Students should first understand the Java situation Spring is trying to improve.

### Core mental model

An object often needs other objects to perform its responsibility.

```text
OrderService
     |
     ↓
OrderRepository
```

The repository is a **dependency** of the service.

### Teach

Start with ordinary Java.

```java
class OrderService {
    private OrderRepository repository;

    OrderService() {
        repository = new OrderRepository();
    }
}
```

Ask:

- What does `OrderService` need?
- Which object is the dependency?
- Who creates the dependency?
- Who decides which implementation is used?

### Why this comes first

Students need a concrete Java problem before Spring's solution has meaning.

### Practice

Give students several classes and ask them to identify:

- the object
- its dependencies
- who creates those dependencies
- where coupling exists

### High-value questions

> What is a dependency?

> Is every field a dependency?

> Who is currently responsible for creating `OrderRepository`?

---

# 2. The Problem: Tight Coupling and Manual Construction

## Mental model

The issue is not simply that `new` exists.

The issue is that one component can become responsible for constructing and controlling another component it depends upon.

```text
OrderService
     |
     | creates
     ↓
OrderRepository
```

This can make replacement, testing, and configuration harder.

### Teach

Compare:

```java
repository = new MySqlOrderRepository();
```

with a design where the service receives an abstraction/implementation from outside.

### Why

Students should **experience the problem** before receiving DI as the answer.

### Practice

Ask students to:

1. Identify the coupling.
2. Explain why it could become a problem.
3. Replace the hard-coded construction.
4. Predict what becomes easier.

---

# 3. Dependency Injection

## Core mental model

> An object declares what it needs; another mechanism supplies it.

```text
OrderService
     ↑
     | receives
     |
OrderRepository
```

### Constructor injection

```java
class OrderService {
    private final OrderRepository repository;

    OrderService(OrderRepository repository) {
        this.repository = repository;
    }
}
```

### Teach deeply

Students should understand:

- dependency
- injection
- constructor injection
- setter injection
- why constructor injection is usually preferred
- inversion of responsibility for object construction

### Do not teach only

> "`@Autowired` means dependency injection."

Instead:

> The object needs a dependency, and the dependency is supplied from outside.

### Practice progression

**Guided:** convert a class to constructor injection.

**Prompted:** identify which dependency should be injected.

**Modified:** replace one implementation with another.

**Independent:** design a small service/repository relationship.

**Explain:** defend why the dependency should be supplied rather than constructed internally.

---

# 4. Inversion of Control

## Core mental model

IoC is the broader change in control.

Traditional application:

```text
Application code
    ↓
creates/manages objects
```

Spring-managed application:

```text
Application code
    ↓
declares requirements

Spring
    ↓
manages object creation/wiring
```

### Important distinction

Do not collapse these into synonyms:

- **IoC** = broader principle of transferring control.
- **DI** = a mechanism/pattern through which dependencies can be supplied externally.

### Teaching question

> Is dependency injection the only possible form of inversion of control?

Use this question to prevent rote definitions.

---

# 5. The Spring Container

## Core mental model

The container is the mechanism that manages Spring's objects.

```text
Spring Container
    |
    +-- Service bean
    +-- Repository bean
    +-- Controller bean
```

### Teach

Students should understand that Spring can:

- create/manage objects
- hold/manage beans
- resolve dependencies
- apply configuration
- participate in lifecycle management

### Why

Without the container mental model, annotations become magic.

### Key question

> If Spring is going to inject a dependency, where does the dependency come from?

Answer:

> From the Spring-managed object graph/container.

---

# 6. Beans and ApplicationContext

## Bean

Teach the idea before terminology:

> A bean is an object managed by the Spring container.

Then connect:

```text
Class
  ↓
Spring creates/manages instance
  ↓
Bean
```

### ApplicationContext

Teach it as an important Spring container interface/context used to access and manage application components and configuration.

The supplied material specifically includes BeanFactory, ApplicationContext and bean lifecycle, so these belong in the conceptual foundation, but their depth should be determined by the course outcome.

### Avoid

Turning BeanFactory vs ApplicationContext into a memorization exercise before students understand the container.

### High-value comparison

| Concept | Main idea |
|---|---|
| Object | Java instance |
| Bean | Object managed by Spring |
| Container | Mechanism that manages beans |
| ApplicationContext | A major Spring container/context abstraction |

---

# 7. Configuration and Bean Registration

## Core question

> How does Spring know what it should manage?

Introduce configuration.

Students should understand the distinction between:

- defining a class
- registering a bean
- configuring the container

### `@Bean`

Use it to demonstrate explicit bean registration.

```java
@Configuration
class AppConfig {

    @Bean
    OrderRepository orderRepository() {
        return new JdbcOrderRepository();
    }
}
```

### Key mental model

```text
@Configuration
      ↓
configuration information
      ↓
@Bean
      ↓
bean registration
      ↓
Spring container
```

### Important distinction

`@Bean` and `@Component` are not interchangeable concepts.

The later retrieval system should repeatedly test this distinction.

---

# 8. Component Scanning and Stereotype Annotations

## Problem

Explicitly registering every component becomes cumbersome.

### Concept

Spring can discover components through component scanning.

Introduce common stereotypes:

- `@Component`
- `@Service`
- `@Repository`
- `@Controller`

Teach the shared idea first:

> These annotations help Spring identify classes that should participate in component management.

Then teach their conventional semantic roles.

### Avoid

Making beginners memorize four annotations without understanding the discovery mechanism.

### Practical

Students create a small application and observe which classes Spring discovers.

---

# 9. Autowiring and Dependency Resolution

## Core question

> Spring knows about the beans. How does it decide which bean should satisfy a dependency?

This is where autowiring belongs.

### Teach

Start with the simple case:

```text
Service
  ↓ needs
Repository
  ↓
one matching bean
```

Then introduce ambiguity.

```text
OrderRepository
    ├── MySqlOrderRepository
    └── InMemoryOrderRepository
```

Ask:

> What happens if Spring has multiple candidates?

Then introduce disambiguation concepts such as qualifiers where appropriate.

### High-value mental model

Autowiring is not "magic."

It is **dependency resolution performed by the container**.

---

# 10. Bean Lifecycle and Scope

## Core question

> What happens to a Spring-managed object during the application's lifecycle?

Students should understand the broad lifecycle:

```text
Definition
   ↓
Creation
   ↓
Dependency injection
   ↓
Initialization
   ↓
Use
   ↓
Destruction
```

Do not over-invest in obscure lifecycle details unless required.

### Scope

Teach the reason scope exists:

> How many instances should Spring manage, and for what lifetime/context?

### Pareto rule

Understand the lifecycle model; avoid drowning beginners in lifecycle hooks unless the course specifically requires them.

---

# 11. Application Architecture with Spring

Now move from individual concepts to a system.

A useful beginner architecture:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

### Teaching objective

Students should understand:

- responsibility
- separation of concerns
- dependencies between layers
- how Spring manages the object graph

### Important shift

The question changes from:

> "What does `@Service` mean?"

to:

> "Why does this class have this responsibility, and how does it depend on the others?"

This is where students begin thinking architecturally.

---

# 12. Spring Boot

## Core mental model

Spring Boot should be introduced as a way to make building and running Spring applications easier through conventions, auto-configuration and starter-based setup.

Do not teach it as:

> "The newer Spring."

Teach:

> Spring is the broader framework/ecosystem; Spring Boot provides conventions and infrastructure that simplify application setup.

### Key comparison

| Spring Framework | Spring Boot |
|---|---|
| Core framework/ecosystem | Simplifies Spring application setup |
| Provides foundational capabilities | Uses conventions/auto-configuration |
| Can require more explicit configuration | Reduces application plumbing |

### Practical goal

Students should be able to start and reason about a small Spring Boot application.

---

# 13. Web/Application Layer and REST

## Core mental model

Now students expose application behavior through HTTP.

```text
HTTP Request
    ↓
Controller
    ↓
Service
    ↓
Repository
    ↓
Data
```

### Teach

- request/response
- endpoint
- controller responsibility
- REST basics
- mapping HTTP operations to application behavior

### Important distinction

`@Controller` and `@RestController` should be taught comparatively because students are likely to confuse them.

The exact framework behavior should be demonstrated in code rather than memorized as isolated definitions.

### Practical

Students build a small endpoint and trace a request through the application layers.

---

# 14. Data Access and JdbcTemplate

## Problem

Applications need to communicate with persistent data sources.

Teach the problem before the abstraction:

```text
Application
   ↓
Database
```

Traditional JDBC creates repeated resource-management/data-access concerns.

### JdbcTemplate mental model

> Spring provides a template abstraction that reduces repetitive JDBC plumbing.

### Teach

- DataSource
- JdbcTemplate
- repository/DAO responsibility
- RowMapper at a practical level

Do not spend disproportionate time on every callback/extractor variation unless required.

---

# 15. Testing Spring Applications

Testing should not be treated as an isolated chapter.

Connect it back to DI:

```text
Dependency Injection
       ↓
replace dependency
       ↓
test component independently
```

### Key insight

DI is not merely an architectural style.

It also improves testability.

### Practical

Students should test a service while controlling/replacing its dependency.

Then ask:

> What would be harder if this service created its repository internally?

This reinforces earlier learning through a new context.

---

# 16. Proxies and AOP

## Introduce only after students understand ordinary object calls

Start with:

```text
Caller
   ↓
Proxy
   ↓
Target object
```

### Problem

Some concerns cut across many components:

- logging
- security
- transactions
- timing
- auditing

If each class implements the same concern manually, code can become scattered/tangled.

### AOP mental model

> AOP provides mechanisms for applying cross-cutting behavior around application operations.

Teach:

- aspect
- advice
- pointcut
- join point
- proxy

but keep the conceptual hierarchy clear.

### Practice

A small logging/timing example is more valuable than a large AOP configuration exercise.

---

# 17. Cross-Cutting Concerns

Now connect AOP/proxy ideas to features students encounter elsewhere.

## Caching

Mental model:

```text
Request
   ↓
Cache?
   ├── yes → return cached result
   └── no  → execute → store result
```

Students should understand:

- why caching exists
- when it helps
- what invalidation means
- that caching can be implemented through Spring's abstraction/proxy mechanisms

## Security

Teach the purpose and high-level request protection model.

Avoid turning advanced OAuth2 configuration into the core of a beginner course unless required.

## Monitoring / Actuator

Teach:

> Production systems need visibility into health and behavior.

Connect monitoring back to the application students already built.

---

# 18. Microservices

Only introduce microservices after students understand a single application.

### Sequence

```text
Monolithic application
       ↓
Why split?
       ↓
Independent services
       ↓
Communication
       ↓
Distributed-system problems
```

### Teach

- what a microservice is
- service boundaries
- autonomy
- independent deployment
- communication
- distributed-system tradeoffs

### Critical warning

Do not teach microservices as:

> "Small applications."

Students should understand the operational and architectural consequences.

---

# 19. Reactive Programming / WebFlux

This should be treated as a **supporting/awareness area unless the actual course outcome requires it**.

Teach the problem:

```text
Blocking model
      vs
Non-blocking/reactive model
```

Introduce:

- responsiveness
- scalability
- non-blocking I/O
- back-pressure
- Reactive Streams
- WebFlux

Avoid spending large amounts of beginner classroom time on reactive internals if they do not unlock required capabilities.

---

# 20. Spring Cloud and Distributed Infrastructure

Teach the **problem → capability** relationship.

| Problem | Example capability |
|---|---|
| Configuration across services | Config Server |
| Finding services | Service Discovery |
| Single entry point | API Gateway |
| Communication/events | Messaging/Bus |
| Distributed concerns | Cloud infrastructure |

The objective is conceptual architecture, not memorizing product names.

---

# 21. Docker and Deployment Awareness

Teach the operational mental model:

```text
Application
   ↓
Package
   ↓
Container image
   ↓
Container
   ↓
Deployment environment
```

Students should understand:

- image
- container
- Dockerfile
- registry
- why containers are useful

Detailed orchestration infrastructure should remain low priority unless explicitly required.

---

# Part III — The Four Teaching Depths

Every topic should eventually be assigned one of these:

### 🔴 Core

Students should:

- explain it
- implement it
- debug it
- apply it independently
- distinguish it from related concepts

### 🟡 Supporting

Students should:

- explain the purpose
- understand the basic mechanism
- perform targeted practical work
- recognize common exam traps

### 🟢 Awareness

Students should:

- know what it is
- know why it exists
- recognize when they might encounter it

### ⚪ Low Priority

Mention only when useful or required.

Do not allow broad material to consume core teaching time.

---

# Part IV — The Recurring Teaching Loop

For every major concept:

## 1. Retrieval

"What do you remember from last time?"

## 2. Problem

"What problem are we facing?"

## 3. Why

"Why is the problem important?"

## 4. Concept

"What idea solves it?"

## 5. Mechanism

"How does Spring actually make it happen?"

## 6. Code

Implement the smallest useful example.

## 7. Deliberate breakage

Remove/change something.

Ask students to predict the result.

## 8. Practice

Guided → Prompted → Modified → Independent.

## 9. Explanation

Student explains the reasoning.

## 10. Retrieval again

Bring the concept back later.

---

# Part V — Instructor Rules

### Rule 1

Do not measure success by how much was covered.

### Rule 2

Do not solve student problems immediately.

Use the hint ladder:

```text
Question
  ↓
Question about their goal
  ↓
Question about relevant concept
  ↓
Small hint
  ↓
Stronger hint
  ↓
Demonstration
```

### Rule 3

Do not let practical work become copying.

### Rule 4

Do not allow annotations to become the mental model.

### Rule 5

Repeatedly ask:

> "What is Spring doing for you here?"

### Rule 6

Make old concepts return.

### Rule 7

Prefer transfer exercises over identical repetition.

### Rule 8

Personally execute the important code before teaching it.

### Rule 9

Use AI to challenge understanding, not replace it.

### Rule 10

Protect the 2-hour preparation ceiling.

---

# Part VI — What Is Deliberately Not Yet Decided

The following remain open until the actual course requirements are established:

- exact 18-session allocation
- exact hours per topic
- final Core/Supporting/Awareness/Low classification
- exact depth of AOP
- exact depth of JDBC
- exact depth of microservices
- exact role of reactive programming
- exact Docker/infrastructure coverage
- final exam strategy
- final assessment design

The next planning phase should allocate the 36 classroom hours only after these decisions are sufficiently constrained.
