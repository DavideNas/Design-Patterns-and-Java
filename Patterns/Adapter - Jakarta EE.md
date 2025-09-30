# 🔌 Adapter Pattern – Jakarta EE

## 🧠 **Definizione**

> L’**Adapter Pattern** permette di **convertire l’interfaccia di una classe in un’altra** che il client si aspetta.
> Utile quando si devono integrare **componenti con interfacce incompatibili**, senza modificare il codice esistente.

---

## 🎯 Scenario

Immagina di avere un servizio esterno che ritorna dati nel formato:

```java
public class ExternalUser {
    private String userName;
    private String userMail;
    private int isActive;

    // getters e setters
}
```

Ma la tua applicazione Jakarta EE vuole lavorare con un modello interno:

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
import jakarta.enterprise.context.ApplicationScoped;

@ApplicationScoped
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

> Qui `UserAdapter` funge da **traduttore**, mappando i campi da `ExternalUser` a `User`.

---

## ✅ 2. **Uso in un servizio Jakarta EE**

```java
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;
import java.util.List;
import java.util.stream.Collectors;

@ApplicationScoped
public class UserService {

    @Inject
    private UserAdapter adapter;

    public List<User> getUsersFromExternal(List<ExternalUser> externalUsers) {
        return externalUsers.stream()
                .map(adapter::adapt)
                .collect(Collectors.toList());
    }
}
```

---

## ✅ 3. **Iniezione e utilizzo in un controller / endpoint**

```java
import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.Produces;
import jakarta.ws.rs.core.MediaType;
import jakarta.inject.Inject;
import java.util.List;

@Path("/users")
public class UserController {

    @Inject
    private UserService userService;

    @GET
    @Produces(MediaType.APPLICATION_JSON)
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

## 💡 **Quando usare l’Adapter Pattern in Jakarta EE**

| Scenario                    | Utilità                                                         |
| --------------------------- | --------------------------------------------------------------- |
| Integrazione API esterne    | Adatta formati “estranei” al modello interno                    |
| Refactoring graduale        | Mantieni compatibilità tra vecchie e nuove classi               |
| Wrapping di librerie legacy | Trasforma interfacce incompatibili senza modificare la libreria |

---

Con Jakarta EE, l’adapter si integra **nativamente con CDI**, quindi è **iniettabile in qualsiasi servizio o controller** e testabile facilmente.

---
