# Chapter 16: Spring Security Fundamentals

> 📌 **Note on this file:** Blockquoted lines like this one are instructor-only teaching cues. If you're a student reading independently, you can skip them.

Every endpoint built so far has been wide open — anyone can call `DELETE /students/1`. This chapter introduces Spring Security: who's allowed in, and what they're allowed to do once they are.

## 🎯 Lesson Goal

Students should understand:

- Why security exists
- Authentication
- Authorization
- Spring Security basics

## 🗺️ Lesson Roadmap

1. Start With the Problem
2. Authentication vs Authorization
3. Spring Security
4. What Happens Without Configuration?
5. In-Memory User Example
6. Roles
7. Restricting Endpoints
8. Security Filter Chain
9. Real Flow
10. Common Beginner Mistakes
11. Mini Exercise
12. Closing Statement

---

## 1. Start With the Problem

Suppose your API contains:

```
DELETE /students/1
```

Anyone can call it.

> 💬 **Ask:** "Should every user be allowed to delete student records?"

Students immediately answer: ❌ No. That's why security exists.

> 🧠 **Teaching line:** "Security answers two questions: Who are you? What are you allowed to do?"

---

## 2. Authentication vs Authorization

Students constantly confuse these.

**Authentication** — "Who are you?"
```
Username: John
Password: *****
```
The system verifies identity. **Authentication = Identity Verification.**

**Authorization** — "What can you do?"
```
John  → Read Only
Admin → Read + Delete
```
**Authorization = Permission Verification.**

> 🧠 **Teaching line:** "Authentication identifies you. Authorization limits you."

---

## 3. Spring Security

Spring Security is a framework that provides:
- Login
- Logout
- Password protection
- Roles
- Permissions
- Session management

> 🧠 **Teaching line:** "Spring Security is Spring's security toolkit."

---

## 4. What Happens Without Configuration?

Add the dependency:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Run the application. Suddenly, `GET /students` shows a **login page**. Students panic. 😂

> 🧠 **Teaching line:** "Spring Security secures everything by default."

---

## 5. In-Memory User Example

```java
package org.codex;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;

@Configuration
public class SecurityConfig {

    @Bean
    public UserDetailsService userDetailsService() {

        UserDetails user = User.builder()
                .username("admin")
                .password("{noop}password")
                .roles("ADMIN")
                .build();

        return new InMemoryUserDetailsManager(user);
    }
}
```

Now login with: `admin` / `password`

> ⚠️ **Production note:** `{noop}` means "no operation" — the password is stored and compared in **plain text**, with no hashing at all. That's fine for a classroom demo, but never acceptable in a real application. Production apps use a `PasswordEncoder` (typically `BCryptPasswordEncoder`) to hash passwords before storing or comparing them.

---

## 6. Roles

```java
.roles("ADMIN")
```

Possible roles, for example: `ADMIN`, `USER`, `MANAGER`, `TEACHER`.

---

## 7. Restricting Endpoints

```java
.requestMatchers("/admin/**")
.hasRole("ADMIN")
```

Meaning: only `ADMIN` can access anything under `/admin/**`.

---

## 8. Security Filter Chain

Modern Spring Security uses:

```java
@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {

    return http
            .authorizeHttpRequests(auth -> auth
                    .requestMatchers("/students/**").authenticated()
                    .anyRequest().permitAll())
            .build();
}
```

> 📌 Don't worry about every line yet — teach the concept first: `/students/**` requires *some* logged-in user, everything else stays open. (Combine this with what you saw in Section 7 — `hasRole(...)` restricts by role, `authenticated()` just requires *any* logged-in user. They solve different problems.)

> 🧠 **Teaching line:** "The security filter chain decides which requests are allowed."

---

## 9. Real Flow

```
Request
   ↓
Spring Security
   ↓
Authenticated?
 /         \
No          Yes
↓            ↓
Reject     Continue
```

**Why this matters:** without security, anyone can call `DELETE /students/1`. With it, only authenticated users — or only admins — can perform sensitive actions.

---

## 10. Common Beginner Mistakes

❌ **Mistake 1** — Confusing Authentication with Authorization.

❌ **Mistake 2** — Thinking Spring Security is only for login pages. It protects APIs too.

❌ **Mistake 3** — Ignoring security until the end. Security should be considered from the start of a project, not bolted on afterward.

---

## 11. Mini Exercise

Extend the security config so that:
1. A new `TEACHER` role exists
2. Only a `TEACHER` can `POST` to `/books/**`
3. Anyone logged in can otherwise access `/books/**`
4. Everything else stays open

<details>
<summary>💡 Click to reveal a suggested solution</summary>

```java
@Bean
public UserDetailsService userDetailsService() {

    UserDetails teacher = User.builder()
            .username("teacher")
            .password("{noop}password")
            .roles("TEACHER")
            .build();

    return new InMemoryUserDetailsManager(teacher);
}

@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {

    return http
            .authorizeHttpRequests(auth -> auth
                    .requestMatchers(HttpMethod.POST, "/books/**").hasRole("TEACHER")
                    .requestMatchers("/books/**").authenticated()
                    .anyRequest().permitAll())
            .build();
}
```

> 📌 Order matters here — the more specific rule (`POST /books/**` needs `TEACHER`) is listed *before* the more general one (`/books/**` just needs to be authenticated). Spring Security checks rules top to bottom and uses the first match.

</details>

---

## 12. Closing Statement

## ✅ Key Takeaways

- Authentication answers "who are you?"; authorization answers "what can you do?"
- Spring Security secures every endpoint by default the moment the dependency is added
- `hasRole(...)` restricts by role; `authenticated()` only requires *some* logged-in user
- `{noop}` passwords are for learning only — real apps hash passwords with a `PasswordEncoder`
- More specific security rules should be listed before general ones, since Spring Security matches top to bottom

---

**← Previous: [Chapter 15 — Exception Handling](./chapter-15-exception-handling.md)**
