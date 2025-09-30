# 🔌 Adapter Pattern – Spring

## 🧠 **Definizione**

> L’**Adapter Pattern** permette di **convertire l’interfaccia di una classe in un’altra** che il client si aspetta, utile per integrare componenti con interfacce incompatibili.

---

## 🎯 Scenario

Supponiamo di avere un servizio esterno che restituisce:

```java
public class ExternalUser {
    private String userName;
    private String userMail;
    private int isActive;

    // getters e setters
}
```

Ma il nostro servizio Spring vuole lavorare con:

```java
public class User {
    private String name;
    private String email;
    private boolean active;

    // getters e setters
}
```

Le interfacce non combaciano → serve un **Adapter**.

---

## ✅ 1. **Adapter semplice**

```java
import org.springframework.stereotype.Component;

@Component
public class UserAdapter {

    public User adapt(ExternalUser external) {
        User user = new User();
        user.setName(external.getUserName());
        user.setEmail(external.getUserMail());
        user.setActive(external.getIsActive() != 0);
        return user;
    }
}
```

> `@Component` rende l’adapter **iniettabile tramite Spring DI**.

---

## ✅ 2. **Uso in un servizio Spring**

```java
import org.springframework.stereotype.Service;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.List;
import java.util.stream.Collectors;

@Service
public class UserService {

    private final UserAdapter adapter;

    @Autowired
    public UserService(UserAdapter adapter) {
        this.adapter = adapter;
    }

    public List<User> getUsersFromExternal(List<ExternalUser> externalUsers) {
        return externalUsers.stream()
                .map(adapter::adapt)
                .collect(Collectors.toList());
    }
}
```

---

## ✅ 3. **Uso in un controller REST Spring**

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import java.util.List;

@RestController
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @GetMapping("/users")
    public List<User> getUsers() {
        List<ExternalUser> external = fetchExternalUsers();
        return userService.getUsersFromExternal(external);
    }

    private List<ExternalUser> fetchExternalUsers() {
        // Simula chiamata a servizio esterno
        return List.of(
                new ExternalUser("marco.rossi", "marco@rossi.com", 1),
                new ExternalUser("luca.bianchi", "luca@bianchi.com", 0)
        );
    }
}
```

---

## 🔁 Confronto Spring vs Jakarta EE

| Caratteristica            | Jakarta EE                   | Spring                           |
| ------------------------- | ---------------------------- | -------------------------------- |
| **Iniezione DI**          | `@Inject` (CDI)              | `@Autowired` / costruttore       |
| **Scope di default**      | `@ApplicationScoped`         | Singleton (`@Component`)         |
| **Controller / endpoint** | `@Path`, `@GET`, `@Produces` | `@RestController`, `@GetMapping` |
| **Registrazione Adapter** | `@ApplicationScoped`         | `@Component`                     |
| **Streaming / mapping**   | `Stream.map()`               | `Stream.map()`                   |
| **Testabilità**           | Iniettabile via CDI          | Iniettabile via Spring DI        |

> Entrambi i framework supportano pienamente l’Adapter Pattern; la differenza principale sta nelle **annotazioni e nel ciclo di vita dei bean**.

---
