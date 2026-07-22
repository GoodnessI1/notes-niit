# Chapter 22: Caching

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

Chapter 21 showed how AOP lets Spring inject behavior around methods. Caching is one of the most practical uses of that exact mechanism. This chapter covers how Spring avoids repeating expensive database calls by remembering results.

## 🎯 Lesson Goal

Students should understand:

- What caching is and why it exists
- Cache hit and cache miss
- `@Cacheable`
- `@CachePut`
- `@CacheEvict`
- How caching connects to AOP

## 🧰 Quick Setup

Spring Boot includes a basic in-memory cache (backed by `ConcurrentHashMap`) with no extra dependencies. Enable it with:

```xml
<!-- No extra dependency needed for the default in-memory cache -->
```

> 📌 **Important limitation:** The default cache lives only in memory. It is **not** shared between server instances and disappears every time the application restarts. It's perfect for learning, but in production you'd swap it for a proper cache provider like **Caffeine** (single-server, fast) or **Redis** (distributed, survives restarts). The annotations below work identically regardless of which provider is underneath.

## 🗺️ Lesson Roadmap

1. Start With the Problem
2. What Is Caching?
3. Cache Hit vs Cache Miss
4. Enabling Caching
5. `@Cacheable`
6. What Is the Cache Key?
7. `@CachePut`
8. `@CacheEvict`
9. Complete Example
10. Visual Flow
11. Caching and AOP
12. Common Student Questions
13. Mini Exercise
14. Closing Statement

---

## 1. Start With the Problem

```java
public Student getStudent(Integer id) {
    return repository.findById(id).orElseThrow();
}
```

```
GET /students/1  → database queried
GET /students/1  → database queried again
GET /students/1  → database queried again
```

Same answer. Same work. Repeated every time.

> 🧠 **Teaching line:** "Why solve the same problem repeatedly?"

---

## 2. What Is Caching?

**Definition:** Caching stores previously computed results so they can be reused later without repeating the original work.

**Real-world analogy:** You memorize your home address — you don't look it up every morning. Your brain is acting as a cache.

---

## 3. Cache Hit vs Cache Miss

**Cache hit** — data found in cache:

```
Request → Cache → Found → Return result
```

No database needed.

**Cache miss** — data not found:

```
Request → Cache → Not Found → Database → Store in Cache → Return result
```

> 🧠 **Teaching line:** "Cache hits are fast. Cache misses are expensive — but they only happen once per key."

---

## 4. Enabling Caching

```java
@SpringBootApplication
@EnableCaching
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

`@EnableCaching` turns caching on. Without it, all caching annotations below are silently ignored.

---

## 5. `@Cacheable`

The most important annotation.

```java
@Cacheable("students")
public Student getStudent(Integer id) {
    return repository.findById(id).orElseThrow();
}
```

- **First request** for `id = 1` → cache miss → database queried → result stored in cache under key `1`
- **Every subsequent request** for `id = 1` → cache hit → database skipped entirely

> 🧠 **Teaching line:** "`@Cacheable` remembers method results."

---

## 6. What Is the Cache Key?

By default, Spring uses the **method arguments** as the cache key.

```java
getStudent(1)  → cache key: 1 → stores Student(1)
getStudent(2)  → cache key: 2 → stores Student(2)
```

Each unique argument gets its own cache entry. Calling `getStudent(1)` again will always return the cached `Student(1)` without touching the database.

---

## 7. `@CachePut`

Updates the cache when data changes.

```java
@CachePut(value = "students", key = "#id")
public Student updateStudent(Integer id, StudentRequest request) {
    Student student = new Student();
    student.setId(id);
    student.setName(request.getName());
    student.setAge(request.getAge());
    student.setEmail(request.getEmail());

    return repository.save(student);
}
```

> ⚠️ **`key = "#id"` is required here.** Without it, Spring uses the full method arguments as the key — in this case, both `id` and the `StudentRequest` object together. That key will never match the plain `id` key stored by `@Cacheable`, so the update would be stored as a separate, unreachable cache entry and the old stale data would remain. Always specify the key explicitly on `@CachePut` so it matches what `@Cacheable` stored.

> 🧠 **Teaching line:** "`@CachePut` refreshes the cache."

---

## 8. `@CacheEvict`

Removes a cache entry when data is deleted.

```java
@CacheEvict(value = "students", key = "#id")
public void deleteStudent(Integer id) {
    repository.deleteById(id);
}
```

Why? Because once the student is deleted, a cached copy of that student is now wrong. The next request for that `id` should hit the database (and get a "not found" result), not return stale cached data.

> 🧠 **Teaching line:** "`@CacheEvict` cleans stale data."

---

## 9. Complete Example

```java
package org.codex.service;

import org.codex.dto.StudentRequest;
import org.codex.exception.StudentNotFoundException;
import org.codex.model.Student;
import org.codex.repository.StudentRepository;
import org.springframework.cache.annotation.CacheEvict;
import org.springframework.cache.annotation.CachePut;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Service;

@Service
public class StudentService {

    private final StudentRepository repository;

    public StudentService(StudentRepository repository) {
        this.repository = repository;
    }

    @Cacheable("students")
    public Student getStudent(Integer id) {
        System.out.println("Hitting database for id: " + id); // remove in production
        return repository.findById(id)
                .orElseThrow(() -> new StudentNotFoundException("Student not found"));
    }

    @CachePut(value = "students", key = "#id")
    public Student updateStudent(Integer id, StudentRequest request) {
        Student student = new Student();
        student.setId(id);
        student.setName(request.getName());
        student.setAge(request.getAge());
        student.setEmail(request.getEmail());
        return repository.save(student);
    }

    @CacheEvict(value = "students", key = "#id")
    public void deleteStudent(Integer id) {
        repository.deleteById(id);
    }
}
```

> 📌 The `System.out.println` in `getStudent()` is for demonstration only — it shows exactly when the database is hit. Call `GET /students/1` twice and you'll see it print only once.

---

## 10. Visual Flow

```
GET Student
      ↓
Cache?
   /      \
Hit       Miss
 ↓           ↓
Return     Database
               ↓
           Store in Cache
               ↓
           Return result
```

---

## 11. Caching and AOP

> 🧠 **Great teaching moment — connects this chapter to the last.**

> 💬 **Ask:** "How does Spring know to check the cache *before* the method runs?"

The answer is AOP. Spring creates a **proxy** around the service:

```
Client
   ↓
Cache Proxy  ← checks cache before/after
   ↓
StudentService.getStudent()
```

The proxy intercepts the call, checks the cache first, and only calls the real method on a miss. `StudentService` itself has no idea the cache exists.

> 🧠 **Teaching line:** "Caching is one of the most practical uses of AOP."

---

## 12. Common Student Questions

> ❓ "Why not cache everything?"
> 👉 Cache uses memory, which is limited. More importantly: cache is only worth it for data that is **read frequently** and **changes rarely**. Data that changes constantly (like live prices or notification counts) would need to be evicted and reloaded so often that the cache adds overhead without benefit.

> ❓ "Can the cache become wrong?"
> 👉 Yes — this is called **stale data**. If a student is updated but the cache isn't refreshed (`@CachePut` missing), the cache returns the old value. That's why `@CachePut` and `@CacheEvict` exist alongside `@Cacheable`.

> ❓ "Does the cache survive a restart?"
> 👉 Not with the default in-memory cache. Everything is lost on restart. For persistent or distributed caching, use Redis.

---

## 13. Mini Exercise

1. Add `@EnableCaching` to your main application class
2. Add `@Cacheable("books")` to `BookService.getBook(Long id)`
3. Add a `System.out.println("Hitting database...")` inside the method
4. Call `GET /books/1` twice via Postman
5. Confirm the print only appears once

Then:

6. Add `@CacheEvict(value = "books", key = "#id")` to `BookService.deleteBook()`
7. Delete the book, then call `GET /books/1` again
8. Confirm the database is hit again (cache was evicted)

<details>
<summary>💡 Click to reveal a suggested solution</summary>

```java
@Cacheable("books")
public Book getBook(Long id) {
    System.out.println("Hitting database for book id: " + id);
    return repository.findById(id)
            .orElseThrow(() -> new BookNotFoundException("Book not found"));
}

@CacheEvict(value = "books", key = "#id")
public void deleteBook(Long id) {
    repository.deleteById(id);
}
```

**Expected behavior:**
- First `GET /books/1` → prints "Hitting database..." → returns book
- Second `GET /books/1` → no print → returns cached book
- `DELETE /books/1` → cache evicted
- `GET /books/1` → prints "Hitting database..." again → throws `BookNotFoundException`

</details>

---

## ✅ Key Takeaways

- Caching stores results so repeated calls skip the database entirely
- A **cache hit** is fast (returns stored result); a **cache miss** queries the database and stores the result for next time
- `@EnableCaching` must be on the main class — without it, all cache annotations are ignored
- `@Cacheable` stores a result on first call and returns it on subsequent calls with the same key
- `@CachePut` always updates the cache after a method runs — always specify `key` explicitly
- `@CacheEvict` removes a cache entry — use it on delete operations to prevent stale data
- The default cache is in-memory only: fast, zero setup, but not shared and doesn't survive restarts
- Caching is powered by AOP — Spring's proxy intercepts the call and checks the cache transparently

---

**← Previous: [Chapter 21 — Aspect-Oriented Programming](./chapter-21-aspect-oriented-programming.md)**
