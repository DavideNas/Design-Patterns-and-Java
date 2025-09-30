# 🕵️‍♂️ **Proxy Pattern – Spring Boot**

## 📌 Scopo

Come in Jakarta EE, il **Proxy Pattern** in Spring permette di avere un oggetto sostitutivo che controlla l’accesso a un bean reale.
Tipici utilizzi:

- Logging / monitoraggio
- Sicurezza / autorizzazioni
- Lazy initialization / caching
- Wrapping di servizi remoti

Spring facilita molto il pattern grazie a **AOP (Aspect-Oriented Programming)** e **Proxy automatici**.

---

## 🔑 Concetti chiave in Spring

1. **Target bean** – Oggetto reale con logica.
2. **Proxy** – Generato automaticamente da Spring AOP o creato manualmente implementando la stessa interfaccia.
3. **Iniezione** – Spring gestisce i proxy dietro le quinte tramite `@Autowired`.
4. **Aspect / Interceptor** – Analogo agli Interceptor CDI, usato per logging, caching, sicurezza ecc.

---

## 🔁 Esempio minimo Proxy Pattern – Spring Boot

### 1️⃣ Interfaccia

```java
public interface UserService {
    String getUserName(int id);
}
```

### 2️⃣ Implementazione reale

```java
import org.springframework.stereotype.Service;

@Service
public class RealUserService implements UserService {
    @Override
    public String getUserName(int id) {
        return "User-" + id;
    }
}
```

### 3️⃣ Proxy manuale (opzionale)

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class LoggingUserProxy implements UserService {

    @Autowired
    private RealUserService realService;

    @Override
    public String getUserName(int id) {
        System.out.println("[Proxy] Chiamata a getUserName(" + id + ")");
        return realService.getUserName(id);
    }
}
```

### 4️⃣ Iniezione del proxy

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService userService; // può essere LoggingUserProxy

    @GetMapping("/{id}")
    public String getUser(@PathVariable int id) {
        return userService.getUserName(id);
    }
}
```

---

## 🔁 Variante con **Spring AOP (proxy automatico)**

### Aspect per logging

```java
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class LoggingAspect {

    @Around("execution(* com.example.demo.UserService.*(..))")
    public Object log(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("[AOP Proxy] Chiamata a " + joinPoint.getSignature().getName());
        return joinPoint.proceed();
    }
}
```

✅ Spring crea automaticamente un proxy per `UserService`, applicando il logging senza modificare il bean reale.

---

## ✅ Riassunto

| Concetto         | Spring Boot                                                  |
| ---------------- | ------------------------------------------------------------ |
| Proxy manuale    | Bean che implementa la stessa interfaccia e delega al target |
| Proxy automatico | Spring AOP (`@Aspect`)                                       |
| Iniezione        | `@Autowired`                                                 |
| Usi comuni       | Logging, caching, sicurezza, lazy loading                    |
| Scopes           | `@Singleton` (default), `@RequestScope`, `@SessionScope`     |

---

## 🔄 Confronto Jakarta EE vs Spring Boot

| Feature                | Jakarta EE                                   | Spring Boot                                   |
| ---------------------- | -------------------------------------------- | --------------------------------------------- |
| Creazione proxy        | Manuale o CDI Interceptor                    | Manuale o Spring AOP (proxy automatico)       |
| Logging / monitoraggio | Interceptor CDI                              | AOP / AspectJ                                 |
| Iniezione              | `@Inject` + `@Named` / Qualifier             | `@Autowired`                                  |
| Lazy / caching         | Proxy manuale o Interceptor                  | Proxy manuale o AOP con @Around / cacheable   |
| Scope                  | `@ApplicationScoped`, `@RequestScoped`, ecc. | `@Singleton` (default), `@RequestScope`, ecc. |

---
