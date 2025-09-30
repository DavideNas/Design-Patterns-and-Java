Ecco la versione **Builder Pattern in Spring Boot**, seguita da un **confronto semplice con Jakarta EE**:

---

# 🧱 Builder Pattern in Spring Boot / Java

---

## 🎯 Scenario

Costruiamo un oggetto `UserProfile` con proprietà opzionali (nome, email, avatar, bio, ruolo) usando il **Builder Pattern**, integrabile in Spring Boot.

---

## 🏗️ 1. **Classe base: UserProfile**

```java
public class UserProfile {
    private final String name;
    private final String email;
    private final String avatarUrl;
    private final String bio;
    private final String role; // "admin", "user", "guest"

    public UserProfile(UserProfileBuilder builder) {
        this.name = builder.getName();
        this.email = builder.getEmail();
        this.avatarUrl = builder.getAvatarUrl();
        this.bio = builder.getBio();
        this.role = builder.getRole();
    }

    // Getters
    public String getName() { return name; }
    public String getEmail() { return email; }
    public String getAvatarUrl() { return avatarUrl; }
    public String getBio() { return bio; }
    public String getRole() { return role; }
}
```

---

## 🧰 2. **Builder Class**

```java
public class UserProfileBuilder {
    private String name;
    private String email;
    private String avatarUrl;
    private String bio;
    private String role; // "admin", "user", "guest"

    public UserProfileBuilder setName(String name) { this.name = name; return this; }
    public UserProfileBuilder setEmail(String email) { this.email = email; return this; }
    public UserProfileBuilder setAvatarUrl(String avatarUrl) { this.avatarUrl = avatarUrl; return this; }
    public UserProfileBuilder setBio(String bio) { this.bio = bio; return this; }
    public UserProfileBuilder setRole(String role) { this.role = role; return this; }

    public UserProfile build() { return new UserProfile(this); }

    // Getters usati da UserProfile
    public String getName() { return name; }
    public String getEmail() { return email; }
    public String getAvatarUrl() { return avatarUrl; }
    public String getBio() { return bio; }
    public String getRole() { return role; }
}
```

---

## 🧪 3. **Uso in un servizio Spring**

```java
import org.springframework.stereotype.Service;

@Service
public class UserService {

    public UserProfile createAdminProfile() {
        return new UserProfileBuilder()
            .setName("Mario Rossi")
            .setEmail("mario@rossi.com")
            .setRole("admin")
            .setBio("Fullstack Developer")
            .build();
    }
}
```

---

## 🚀 4. **Uso in Spring Boot Application**

```java
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class App implements CommandLineRunner {

    private final UserService userService;

    public App(UserService userService) {
        this.userService = userService;
    }

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }

    @Override
    public void run(String... args) {
        UserProfile admin = userService.createAdminProfile();
        System.out.println("Admin: " + admin.getName() + ", Role: " + admin.getRole());
    }
}
```

---

## ✅ Confronto Jakarta EE vs Spring Boot (semplice)

| Feature               | Jakarta EE (CDI)          | Spring Boot                          |
| --------------------- | ------------------------- | ------------------------------------ |
| Builder dinamico      | standard Java             | standard Java + bean/service Spring  |
| Iniezione             | `@Inject`                 | `@Autowired` / constructor injection |
| Scope                 | `@ApplicationScoped`      | singleton di default (service/bean)  |
| Profilazione / config | variabili locali / config | `@Value` + `application.properties`  |

---
