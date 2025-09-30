# 🕵️‍♂️ **Proxy Pattern** – Jakarta EE

## 📌 Scopo

Il **Proxy Pattern** permette di avere un **oggetto sostitutivo** che controlla l’accesso a un oggetto reale (target).

Tipici utilizzi in Jakarta EE:

- Logging o monitoraggio delle chiamate ai bean.
- Controllo di sicurezza / autorizzazioni.
- Lazy initialization o caching dei dati.
- Wrapping di servizi remoti (EJB, REST client).

---

## 🔑 Concetti chiave in Jakarta EE

1. **Target bean** – L’oggetto reale che fornisce la logica.
2. **Proxy** – Bean che implementa la stessa interfaccia e delega al target, aggiungendo comportamento aggiuntivo.
3. **Injection** – Il proxy può essere iniettato al posto del target usando `@Inject` + `@Named` o CDI Qualifiers.
4. **Interceptors** – Jakarta EE supporta anche **interceptors CDI** (`@Interceptor`) per comportamento trasversale simile al proxy.

---

## 🔁 Esempio minimo Proxy Pattern – Jakarta EE

### 1️⃣ Interfaccia (Target)

```java
public interface UserService {
    String getUserName(int id);
}
```

### 2️⃣ Implementazione reale

```java
import jakarta.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class RealUserService implements UserService {
    @Override
    public String getUserName(int id) {
        return "User-" + id;
    }
}
```

### 3️⃣ Proxy

```java
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;
import jakarta.inject.Named;

@ApplicationScoped
@Named("loggingProxy")
public class LoggingUserProxy implements UserService {

    @Inject
    @Named("realUserService")
    private UserService realService;

    @Override
    public String getUserName(int id) {
        System.out.println("[Proxy] Chiamata a getUserName(" + id + ")");
        return realService.getUserName(id);
    }
}
```

### 4️⃣ Configurazione / Iniezione

```java
import jakarta.inject.Inject;
import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.PathParam;

@Path("/users")
public class UserController {

    @Inject
    @Named("loggingProxy")
    private UserService userService;

    @GET
    @Path("/{id}")
    public String getUser(@PathParam("id") int id) {
        return userService.getUserName(id);
    }
}
```

✅ Quando il controller chiama `userService.getUserName()`, in realtà viene eseguito il **proxy**, che logga e poi chiama il servizio reale.

---

## 🔁 Variante con CDI Interceptor (alternativa moderna)

Invece di creare manualmente un proxy, si può usare un **interceptor**:

```java
import jakarta.interceptor.AroundInvoke;
import jakarta.interceptor.Interceptor;
import jakarta.interceptor.InvocationContext;

@Interceptor
@Loggable
public class LoggingInterceptor {

    @AroundInvoke
    public Object logMethod(InvocationContext ctx) throws Exception {
        System.out.println("[Interceptor] Chiamata a " + ctx.getMethod().getName());
        return ctx.proceed();
    }
}
```

Poi annoti il bean target:

```java
@ApplicationScoped
@Loggable
public class RealUserService implements UserService { ... }
```

🟢 Jakarta EE gestisce automaticamente il proxy dietro le quinte.

---

## ✅ Riassunto

| Concetto         | Jakarta EE                                                   |
| ---------------- | ------------------------------------------------------------ |
| Proxy manuale    | Crei un bean che implementa l’interfaccia e delega al target |
| Proxy automatico | Interceptor CDI (`@AroundInvoke`)                            |
| Iniezione        | `@Inject` + `@Named` / Qualifier                             |
| Usi comuni       | Logging, caching, sicurezza, lazy loading                    |
| Scopes           | `@ApplicationScoped`, `@RequestScoped`, ecc.                 |

---
