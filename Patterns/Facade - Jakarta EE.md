# 🏛️ **Facade Pattern – Jakarta EE**

## ✅ Definizione

> Il **Facade Pattern** fornisce un’interfaccia semplificata a un **sottosistema complesso**, nascondendo la complessità interna e centralizzando le operazioni più comuni.

In Jakarta EE si può usare con:

- **EJB stateless** (`@Stateless`) o CDI beans (`@ApplicationScoped`)
- Coordinamento di più servizi o DAO
- Esporre un’unica interfaccia ai client (REST o altri bean)

---

## 💡 Scenario

Supponiamo di avere tre servizi interni:

- `AuthService`: login/logout
- `UserService`: profilo utente
- `PermissionService`: ruoli e permessi

Vogliamo un **facade** che semplifichi l’uso di questi servizi per un client REST.

---

## 1. Servizi interni (CDI beans)

```java
import jakarta.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class AuthService {
    public String login(String username, String password) {
        // Simula generazione token
        return "FAKE-TOKEN";
    }

    public void logout() {
        System.out.println("Logged out");
    }
}

@ApplicationScoped
public class UserService {
    public User getUserProfile() {
        return new User("Alice", "alice@example.com", true);
    }
}

@ApplicationScoped
public class PermissionService {
    public List<String> getPermissions() {
        return List.of("read", "write");
    }
}
```

> `User` è una semplice classe POJO:

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
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;

@ApplicationScoped
public class UserFacade {

    @Inject
    private AuthService authService;

    @Inject
    private UserService userService;

    @Inject
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

## 3. Controller REST (JAX-RS)

```java
import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.Produces;
import jakarta.ws.rs.core.MediaType;
import jakarta.inject.Inject;

@Path("/users")
public class UserController {

    @Inject
    private UserFacade facade;

    @GET
    @Path("/profile")
    @Produces(MediaType.APPLICATION_JSON)
    public UserFacade.UserData getUserData() {
        facade.login("admin", "password"); // simulazione login
        return facade.loadUserData();
    }
}
```

---

## ✅ Vantaggi

| ✅ Aspetto                   | Spiegazione                                          |
| ---------------------------- | ---------------------------------------------------- |
| Riduzione complessità client | Nasconde più servizi dietro un’unica interfaccia     |
| Centralizzazione logica      | Tutte le operazioni comuni sono gestite dal facade   |
| Testabilità                  | Si possono mockare solo i servizi interni nel test   |
| Manutenibilità               | Modifiche interne ai servizi non impattano il client |
| Chiarezza                    | L’interfaccia verso il client è semplice e coerente  |

---
