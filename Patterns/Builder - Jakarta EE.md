# 🧱 Builder Pattern in Jakarta EE / Java

---

## 🧠 **Definizione**

> Il **Builder Pattern** separa la costruzione di un oggetto complesso dalla sua rappresentazione, permettendo di costruire lo stesso oggetto passo-passo in modi diversi.

È utile quando un oggetto ha molte proprietà opzionali, mantenendo il codice leggibile e scalabile.

---

## 🎯 Scenario

Creiamo un oggetto `UserProfile` con proprietà opzionali: nome, email, avatar, bio, ruolo, ecc., evitando costruttori con troppi parametri.

---

## ✅ Obiettivo: costruire così

```java
UserProfile user = new UserProfileBuilder()
    .setName("Mario Rossi")
    .setEmail("mario@rossi.com")
    .setRole("admin")
    .build();
```

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

    public UserProfileBuilder setName(String name) {
        this.name = name;
        return this;
    }

    public UserProfileBuilder setEmail(String email) {
        this.email = email;
        return this;
    }

    public UserProfileBuilder setAvatarUrl(String avatarUrl) {
        this.avatarUrl = avatarUrl;
        return this;
    }

    public UserProfileBuilder setBio(String bio) {
        this.bio = bio;
        return this;
    }

    public UserProfileBuilder setRole(String role) {
        this.role = role;
        return this;
    }

    public UserProfile build() {
        return new UserProfile(this);
    }

    // Getters usati da UserProfile
    public String getName() { return name; }
    public String getEmail() { return email; }
    public String getAvatarUrl() { return avatarUrl; }
    public String getBio() { return bio; }
    public String getRole() { return role; }
}
```

---

## 🧪 3. **Esempio d’uso**

```java
public class BuilderDemo {
    public static void main(String[] args) {
        UserProfile user = new UserProfileBuilder()
            .setName("Mario Rossi")
            .setEmail("mario@rossi.com")
            .setRole("admin")
            .setBio("Fullstack Developer")
            .build();

        System.out.println("Name: " + user.getName());
        System.out.println("Email: " + user.getEmail());
        System.out.println("Role: " + user.getRole());
        System.out.println("Bio: " + user.getBio());
    }
}
```

---

## ✅ Vantaggi

| Vantaggio       | Descrizione                                      |
| --------------- | ------------------------------------------------ |
| 🧩 Modularità   | Ogni metodo imposta solo una proprietà           |
| 📦 Immutabilità | L’oggetto finale può essere reso immutabile      |
| ✨ Fluent API   | Codice leggibile e a catena                      |
| ⚙️ Complessità  | Ottimo per oggetti con molte variabili opzionali |

---
