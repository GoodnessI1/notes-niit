# Chapter 18: Configuration & Profiles

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

So far, values like database URLs have been hardcoded directly in the code. This chapter shows how to move those values out of the code entirely — and how to switch between different environments (dev, test, prod) without touching a single Java file.

## 🎯 Lesson Goal

Students should understand:

- What configuration is and why it exists
- `application.properties`
- `@Value`
- Profiles
- Environment-specific settings

## 🗺️ Lesson Roadmap

1. Start With the Problem
2. What Is Configuration?
3. Spring Configuration File
4. Accessing Configuration Values (`@Value`)
5. Why Not Hardcode?
6. Profiles
7. How Profile Files Are Named (Important)
8. What `application.properties` vs Profile Files Actually Do
9. Activating a Profile
10. Real-World Environments
11. Common Student Confusions
12. Mini Exercise
13. Closing Statement

---

## 1. Start With the Problem

```java
public class DatabaseConfig {
    String url = "jdbc:mysql://localhost:3306/studentdb";
}
```

Everything works locally. Now you deploy to production. Production database:

```
jdbc:mysql://prod-server:3306/studentdb
```

You must modify the code, recompile, and redeploy. That's a problem.

> 🧠 **Teaching line:** "Applications should change behavior through configuration, not code changes."

---

## 2. What Is Configuration?

**Definition:** Configuration is data that controls how an application behaves without changing the source code.

**Examples:**
- Database URL
- Database username
- Port number
- API keys
- Application name

---

## 3. Spring Configuration File

Spring Boot uses `application.properties`, located at:

```
src/main/resources/application.properties
```

Example:

```properties
app.name=Student Management System
server.port=8081
```

Spring reads this file automatically at startup — you don't need to do anything to load it.

---

## 4. Accessing Configuration Values (`@Value`)

Suppose `application.properties` contains:

```properties
app.name=Student Management System
```

Read it in a component:

```java
@Component
public class AppInfo {

    @Value("${app.name}")
    private String appName;

    public void showName() {
        System.out.println(appName);
    }
}
```

✅ **Output:** `Student Management System`

> 🧠 **Teaching line:** "`@Value` injects configuration values just like `@Autowired` injects beans."

> ⚠️ **Silent failure to watch for:** If the key (`app.name`) doesn't exist in `application.properties`, Spring does **not** silently ignore it — the application **fails to start entirely**. Students who mistype a key will see a startup error, not a runtime one. That's actually good design (fail fast), but it will be confusing the first time it happens.

---

## 5. Why Not Hardcode?

❌ **Bad:**
```java
String appName = "Student Management System";
```

✅ **Good:**
```properties
app.name=Student Management System
```

Why? Because tomorrow you can change the name to `Library Management System` in one place — the properties file — without touching any Java code, recompiling, or redeploying.

---

## 6. Profiles

This is the heart of the chapter.

**The problem:**

During development:
```
Database = localhost
```

During production:
```
Database = cloud server
```

Should you constantly edit code to switch between them? No. Use **profiles**.

**Development profile** — `application-dev.properties`:
```properties
spring.datasource.url=jdbc:mysql://localhost/studentdb
```

**Production profile** — `application-prod.properties`:
```properties
spring.datasource.url=jdbc:mysql://prod-server/studentdb
```

Spring automatically loads the correct file based on which profile is active.

> 🧠 **Teaching line:** "Profiles allow one application to behave differently in different environments."

---

## 7. How Profile Files Are Named (Important)

This is not magic — it follows a rule:

```
application-{profile}.properties
```

Examples:
- `application-dev.properties`
- `application-prod.properties`
- `application-test.properties`

Spring Boot looks for a file matching this pattern automatically when a profile is active. If you name the file anything else, Spring won't find it and will ignore it silently. Get the naming right.

---

## 8. What `application.properties` vs Profile Files Actually Do

This is the most common point of confusion.

**`application.properties` is always loaded**, regardless of the active profile. Profile-specific files (e.g. `application-dev.properties`) are loaded *on top of it* — they add to or override what's in the base file.

**Practical example:**

```properties
# application.properties (always loaded)
app.name=Student Management System
server.port=8081
```

```properties
# application-dev.properties (loaded only in dev)
spring.datasource.url=jdbc:mysql://localhost/studentdb
```

```properties
# application-prod.properties (loaded only in prod)
spring.datasource.url=jdbc:mysql://prod-server/studentdb
```

When running in `dev`: both `application.properties` and `application-dev.properties` are active. `app.name` and `server.port` come from the base file; the database URL comes from the dev file.

If the same key appears in both `application.properties` and the profile file, the **profile file wins**.

---

## 9. Activating a Profile

**For learning — set it in `application.properties`:**

```properties
spring.profiles.active=dev
```

Switch to production by changing `dev` to `prod`. That's it.

**In real deployments — don't hardcode it.** The whole point of profiles is to avoid changing files between environments. In production, the active profile is typically set via:

An environment variable:
```
SPRING_PROFILES_ACTIVE=prod
```

Or a JVM startup flag:
```
-Dspring.profiles.active=prod
```

This way, the same application artifact runs in dev or prod without any file being modified — just the environment it's launched in changes.

---

## 10. Real-World Environments

Typically:

| Profile | Used by |
|---|---|
| `dev` | Developer's local machine |
| `test` | Testing / QA environment |
| `prod` | Real users |

**Visual flow:**

```
Application starts
      ↓
Active profile detected
      ↓
application.properties loaded (always)
      ↓
application-{profile}.properties loaded on top
      ↓
App runs with combined config
```

---

## 11. Common Student Confusions

> ❓ "Can I have multiple property files?"
> 👉 Yes — `application.properties`, `application-dev.properties`, `application-prod.properties` can all exist at the same time. Only the right ones are loaded.

> ❓ "Do profiles change code?"
> 👉 No. Profiles change configuration. Your Java code stays exactly the same.

> ❓ "I switched to prod but it's still using the dev database."
> 👉 Check that `spring.profiles.active=prod` is set, that the file is named exactly `application-prod.properties`, and that the key you're checking isn't also set in `application.properties` (which would override it if placed after the profile-specific file — but in this system, the profile file always wins for duplicate keys).

---

## 12. Mini Exercise

1. Create `application-dev.properties` with `app.message=Development Environment`
2. Create `application-prod.properties` with `app.message=Production Environment`
3. Inject the value with `@Value("${app.message}")` and print it at startup
4. Switch `spring.profiles.active` between `dev` and `prod` and observe the output change

<details>
<summary>💡 Click to reveal a suggested solution</summary>

```properties
# application.properties
spring.profiles.active=dev
```

```properties
# application-dev.properties
app.message=Development Environment
```

```properties
# application-prod.properties
app.message=Production Environment
```

```java
package org.codex;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class MessageRunner implements CommandLineRunner {

    @Value("${app.message}")
    private String message;

    @Override
    public void run(String... args) {
        System.out.println("Active message: " + message);
    }
}
```

✅ With `spring.profiles.active=dev`:
```
Active message: Development Environment
```

✅ With `spring.profiles.active=prod`:
```
Active message: Production Environment
```

The Java code never changes — only the active profile does.

</details>

---

## ✅ Key Takeaways

- Configuration is data that controls behavior without modifying source code
- `application.properties` is Spring Boot's default config file, always loaded at startup
- `@Value("${key}")` injects a property value — if the key is missing, the app fails to start entirely
- Profile files follow the naming rule `application-{profile}.properties`; Spring finds them automatically
- `application.properties` is always loaded; profile files load on top and override duplicate keys
- Set `spring.profiles.active` in `application.properties` for local development; use environment variables or JVM flags in real deployments

---

**← Previous: Chapter 17** | **Next: Chapter 19 →**
