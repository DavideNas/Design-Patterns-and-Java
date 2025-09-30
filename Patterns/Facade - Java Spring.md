# 🏛️ **Facade Pattern – Java Spring**

## ✅ Definizione

> Il **Facade Pattern** in Spring fornisce un’interfaccia semplificata a più servizi o componenti complessi, nascondendo dettagli interni e facilitando il consumo da parte di controller o client REST.

Si realizza tramite:

- `@Service` o `@Component` per servizi interni
- `@Autowired` per iniezione dei servizi
- Controller REST (`@RestController`) che usa il facade

---

## 💡 Scenario

Tre servizi interni:

- `AuthService`: login/logout
- `UserService`: profilo utente
- `PermissionService`: ruoli e permessi

Vogliamo un **facade** che coordini tutto e offra un’unica interfaccia.

---

## 1. Servizi interni

```java
import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class AuthService {
    public String login(String username, String password) {
        return "FAKE-TOKEN";
    }

    public void logout() {
        System.out.println("Logged out");
    }
}

@Service
public class UserService {
    public User getUserProfile() {
        return new User("Alice", "alice@example.com", true);
    }
}

@Service
public class PermissionService {
    public List<String> getPermissions() {
        return List.of("read", "write");
    }
}
```

> `User` POJO:

```java
public class User {
    private String name;
    private String email;
    private boolean active;

    public User(String name, String email, boolean active) {
        this.name = name;
        this.email = email;
        this.active = active;
    }

    // getters e setters
}
```

---

## 2. Facade

```java
import org.springframework.stereotype.Service;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.List;

@Service
public class UserFacade {

    @Autowired
    private AuthService authService;

    @Autowired
    private UserService userService;

    @Autowired
    private PermissionService permissionService;

    public String login(String username, String password) {
        String token = authService.login(username, password);
        System.out.println("Token ricevuto: " + token);
        return token;
    }

    public void logout() {
        authService.logout();
    }

    public UserData loadUserData() {
        User profile = userService.getUserProfile();
        List<String> permissions = permissionService.getPermissions();
        return new UserData(profile, permissions);
    }

    public static class UserData {
        private User profile;
        private List<String> permissions;

        public UserData(User profile, List<String> permissions) {
            this.profile = profile;
            this.permissions = permissions;
        }

        // getters e setters
    }
}
```

---

## 3. Controller REST

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.beans.factory.annotation.Autowired;

@RestController
public class UserController {

    @Autowired
    private UserFacade facade;

    @GetMapping("/users/profile")
    public UserFacade.UserData getUserData() {
        facade.login("admin", "password"); // simulazione login
        return facade.loadUserData();
    }
}
```

---

## ✅ Vantaggi

- ✅ Riduce l’accoppiamento controller–servizi
- ✅ Nasconde dettagli implementativi dei servizi
- ✅ Centralizza la logica di coordinamento
- ✅ Facilita testing con mock dei servizi o del facade
- ✅ Codice leggibile e manutenibile

---

## 🔁 Confronto rapido: Jakarta EE vs Spring

| Aspetto             | Jakarta EE                      | Spring                              |
| ------------------- | ------------------------------- | ----------------------------------- |
| Bean                | `@ApplicationScoped` CDI        | `@Service` o `@Component`           |
| Iniezione           | `@Inject`                       | `@Autowired`                        |
| REST Controller     | `@Path` + JAX-RS                | `@RestController` + Spring MVC      |
| Lifecycle e scoping | Gestito da CDI                  | Gestito dal container Spring        |
| Uso principale      | Coordinamento di più bean / EJB | Coordinamento di più servizi Spring |
| Testing             | Mock CDI beans                  | Mock Spring beans                   |

---
