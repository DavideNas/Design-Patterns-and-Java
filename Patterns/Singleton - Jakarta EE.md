# 🏭 **Factory Method Pattern in Java con Jakarta EE (CDI)**

## 🎯 Scenario

Abbiamo due implementazioni di `Logger`:

* `ConsoleLogger` (stampa a console)
* `ApiLogger` (simula invio ad API).

Con **CDI** possiamo usare `@Produces` per ottenere il comportamento simile a `useFactory` di Angular.

---

## 🔧 Step 1: **Interfaccia Logger**

```java
public interface Logger {
    void log(String message);
}
```

---

## 🧱 Step 2: **Implementazioni concrete**

```java
import jakarta.enterprise.context.Dependent;

@Dependent
public class ConsoleLogger implements Logger {
    @Override
    public void log(String message) {
        System.out.println("[Console] " + message);
    }
}

@Dependent
public class ApiLogger implements Logger {
    @Override
    public void log(String message) {
        // Simulazione invio log a un'API
        System.out.println("[API] " + message + " (simulazione invio)");
    }
}
```

---

## 🏭 Step 3: **Factory con @Produces**

```java
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.enterprise.inject.Produces;
import jakarta.inject.Named;

@ApplicationScoped
public class LoggerFactory {

    private final String loggerType = "api"; // potrebbe venire da config/env

    @Produces
    @Named("dynamicLogger")
    public Logger produceLogger() {
        switch (loggerType.toLowerCase()) {
            case "console":
                return new ConsoleLogger();
            case "api":
                return new ApiLogger();
            default:
                throw new IllegalArgumentException("Unknown logger type: " + loggerType);
        }
    }
}
```

---

## 🚀 Step 4: **Uso in un bean CDI**

```java
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;
import jakarta.inject.Named;

@ApplicationScoped
public class LogService {

    @Inject
    @Named("dynamicLogger")
    private Logger logger;

    public void sendLog() {
        logger.log("Messaggio dal servizio con Factory Method in Jakarta EE!");
    }
}
```

---

## 🧪 Step 5: **Bootstrap in un'app Jakarta EE**

Ad esempio in un `Servlet`:

```java
import jakarta.inject.Inject;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/log")
public class LogServlet extends HttpServlet {

    @Inject
    private LogService logService;

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {
        logService.sendLog();
        resp.getWriter().write("Log inviato!");
    }
}
```

---

## ✅ Confronto

| Angular (`useFactory`) | Jakarta EE (CDI `@Produces`)   |
| ---------------------- | ------------------------------ |
| `InjectionToken`       | `@Named`                       |
| `useFactory`           | `@Produces` method             |
| `providers`            | CDI `@ApplicationScoped` bean  |
| `@Inject(Logger)`      | `@Inject @Named("...") Logger` |

---
