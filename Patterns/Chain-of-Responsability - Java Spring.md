# 🧩 Chain of Responsibility – Spring Boot

In Spring, possiamo sfruttare **Component Scan, Dependency Injection e `@Autowired`** per costruire la catena dei handler senza creare manualmente le istanze.

---

## 1️⃣ Interfaccia Handler

```java
public interface Handler {
    void setNext(Handler next);
    void handle(String request);
}
```

---

## 2️⃣ Classe astratta base

```java
public abstract class AbstractHandler implements Handler {
    protected Handler next;

    @Override
    public void setNext(Handler next) {
        this.next = next;
    }

    @Override
    public void handle(String request) {
        if (next != null) {
            next.handle(request);
        }
    }
}
```

---

## 3️⃣ Handler concreti come `@Component`

```java
import org.springframework.stereotype.Component;

@Component
public class AuthHandler extends AbstractHandler {
    @Override
    public void handle(String request) {
        if ("AUTH".equals(request)) {
            System.out.println("Autenticazione gestita da Spring");
        } else {
            super.handle(request);
        }
    }
}

@Component
public class LoggingHandler extends AbstractHandler {
    @Override
    public void handle(String request) {
        if ("LOG".equals(request)) {
            System.out.println("Logging gestito da Spring");
        } else {
            super.handle(request);
        }
    }
}
```

---

## 4️⃣ Context Spring (`@Service`)

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class RequestProcessor {

    private final Handler chain;

    @Autowired
    public RequestProcessor(AuthHandler authHandler, LoggingHandler loggingHandler) {
        authHandler.setNext(loggingHandler);
        this.chain = authHandler;
    }

    public void process(String request) {
        chain.handle(request);
    }
}
```

---

## 5️⃣ Controller REST

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class RequestController {

    private final RequestProcessor processor;

    public RequestController(RequestProcessor processor) {
        this.processor = processor;
    }

    @GetMapping("/request")
    public String handleRequest(@RequestParam String type) {
        processor.process(type);
        return "Request processed: " + type;
    }
}
```

---

## ✅ Come funziona

- Stesso flusso della versione Jakarta EE: la richiesta attraversa la catena di handler.
- Differenza principale: Spring sfrutta **`@Component` e `@Autowired`**, quindi non dobbiamo usare manualmente `@Inject` o `@ApplicationScoped`.
- Tutto configurato automaticamente tramite **Spring Context**.

---

# 🔹 Confronto Jakarta EE vs Spring

| Caratteristica              | Jakarta EE                                 | Spring Boot                                |
| --------------------------- | ------------------------------------------ | ------------------------------------------ |
| Scope dei bean              | `@ApplicationScoped`, `@RequestScoped`     | `@Component`, `@Service`                   |
| Dependency Injection        | `@Inject`                                  | `@Autowired` o constructor injection       |
| Configurazione della catena | Manuale nel costruttore o factory CDI      | Manuale nel costruttore del bean Spring    |
| REST Integration            | `@Path`, `@GET` (JAX-RS)                   | `@RestController`, `@GetMapping`           |
| Ciclo di vita dei bean      | Gestito dal container Jakarta EE           | Gestito dal Spring Context                 |
| Estensibilità               | Nuovi handler richiedono registrazione CDI | Nuovi handler `@Component` scan automatico |

---

✅ **Osservazioni**

- Entrambi implementano lo stesso pattern: **richiesta passa lungo una catena di handler**.
- Spring semplifica la gestione dei bean e li rileva automaticamente tramite scan.
- Jakarta EE richiede `@Inject` e gestione esplicita dei bean se non si usa `@Produces`.

---
