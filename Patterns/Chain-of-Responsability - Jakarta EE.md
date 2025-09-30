Perfetto! Adesso passiamo al **Chain of Responsibility solo per Jakarta EE**, senza Angular.

---

# 🧩 Chain of Responsibility – Jakarta EE

Il **Chain of Responsibility (CoR)** è un pattern comportamentale che permette a più oggetti di gestire una richiesta senza conoscere chi la processerà. Ogni handler decide se gestire la richiesta o passarla al successivo.

---

## 1️⃣ Definizione dell’interfaccia Handler

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

## 3️⃣ Handler concreti

```java
import jakarta.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class AuthHandler extends AbstractHandler {
    @Override
    public void handle(String request) {
        if ("AUTH".equals(request)) {
            System.out.println("Autenticazione gestita");
        } else {
            super.handle(request);
        }
    }
}

@ApplicationScoped
public class LoggingHandler extends AbstractHandler {
    @Override
    public void handle(String request) {
        if ("LOG".equals(request)) {
            System.out.println("Logging gestito");
        } else {
            super.handle(request);
        }
    }
}
```

---

## 4️⃣ Context (EJB o CDI Bean)

```java
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;

@ApplicationScoped
public class RequestProcessor {

    private Handler chain;

    @Inject
    public RequestProcessor(AuthHandler authHandler, LoggingHandler loggingHandler) {
        // costruzione della catena
        authHandler.setNext(loggingHandler);
        this.chain = authHandler;
    }

    public void process(String request) {
        chain.handle(request);
    }
}
```

---

## 5️⃣ Uso in un endpoint REST

```java
import jakarta.inject.Inject;
import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.QueryParam;

@Path("/request")
public class RequestResource {

    @Inject
    private RequestProcessor processor;

    @GET
    public String handleRequest(@QueryParam("type") String type) {
        processor.process(type);
        return "Request processed: " + type;
    }
}
```

---

## ✅ Come funziona

1. La richiesta arriva al **context** (`RequestProcessor`).
2. La catena di handler prova a gestirla uno dopo l’altro.
3. Solo l’handler corretto reagisce; gli altri passano la richiesta avanti.
4. Tutto configurato tramite **CDI e `@ApplicationScoped`**, senza hardcoding di istanze.

---

## 🔹 Vantaggi in Jakarta EE

- Decoupling tra **client** e **handler**
- Facile aggiungere nuovi handler senza modificare il client
- Integrabile con **EJB, CDI e REST endpoints**
- Catena configurabile a runtime tramite injection

---
