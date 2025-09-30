# 🧪 **Abstract Factory Pattern in Java (Jakarta EE)**

## ✅ Definizione

L’**Abstract Factory** fornisce un’interfaccia per creare **famiglie di oggetti correlati** senza specificare le classi concrete.

---

## 🧱 1. **Interfacce astratte**

```java
public interface Logger {
    void log(String message);
}

public interface Formatter {
    String format(String message);
}
```

---

## 🧩 2. **Famiglia `Development`**

```java
public class DevLogger implements Logger {
    private final Formatter formatter;

    public DevLogger(Formatter formatter) {
        this.formatter = formatter;
    }

    @Override
    public void log(String message) {
        System.out.println("DEV: " + formatter.format(message));
    }
}

public class SimpleFormatter implements Formatter {
    @Override
    public String format(String message) {
        return ">>> " + message;
    }
}
```

---

## 🧩 3. **Famiglia `Production`**

```java
public class ProdLogger implements Logger {
    private final Formatter formatter;

    public ProdLogger(Formatter formatter) {
        this.formatter = formatter;
    }

    @Override
    public void log(String message) {
        // Simulazione invio a un server
        System.out.println("PROD LOG SENT: " + formatter.format(message));
    }
}

import java.time.Instant;
import com.google.gson.Gson;

public class JsonFormatter implements Formatter {
    private final Gson gson = new Gson();

    @Override
    public String format(String message) {
        return gson.toJson(
            new LogMessage(message, Instant.now().toString())
        );
    }

    // Helper interno
    private static class LogMessage {
        String message;
        String timestamp;

        LogMessage(String message, String timestamp) {
            this.message = message;
            this.timestamp = timestamp;
        }
    }
}
```

*(nota: qui uso `Gson` per la serializzazione JSON, ma puoi anche costruire la stringa manualmente)*

---

## 🏭 4. **Abstract Factory Interface**

```java
public interface LoggingFactory {
    Logger createLogger();
    Formatter createFormatter();
}
```

---

## 🏗️ 5. **Implementazioni concrete della Factory**

```java
public class DevLoggingFactory implements LoggingFactory {
    @Override
    public Formatter createFormatter() {
        return new SimpleFormatter();
    }

    @Override
    public Logger createLogger() {
        return new DevLogger(createFormatter());
    }
}

public class ProdLoggingFactory implements LoggingFactory {
    @Override
    public Formatter createFormatter() {
        return new JsonFormatter();
    }

    @Override
    public Logger createLogger() {
        return new ProdLogger(createFormatter());
    }
}
```

---

## 🧪 6. **Selettore di factory (runtime)**

```java
public class LoggingFactoryProvider {
    public static LoggingFactory getFactory(String env) {
        switch (env.toLowerCase()) {
            case "dev":
                return new DevLoggingFactory();
            case "prod":
                return new ProdLoggingFactory();
            default:
                throw new IllegalArgumentException("Unknown environment: " + env);
        }
    }
}
```

---

## 🧩 7. **Uso nel client**

```java
public class App {
    public static void main(String[] args) {
        // Supponiamo che l’ambiente arrivi da una variabile di sistema
        String env = System.getProperty("app.env", "dev");

        LoggingFactory factory = LoggingFactoryProvider.getFactory(env);
        Logger logger = factory.createLogger();

        logger.log("Messaggio di test");
    }
}
```

Eseguendo con:

```bash
java -Dapp.env=dev App
```

👉 Uscita:

```
DEV: >>> Messaggio di test
```

oppure con:

```bash
java -Dapp.env=prod App
```

👉 Uscita:

```
PROD LOG SENT: {"message":"Messaggio di test","timestamp":"2025-09-30T19:01:22.123Z"}
```

---

## ✅ Vantaggi

| Vantaggio         | Descrizione                                                   |
| ----------------- | ------------------------------------------------------------- |
| 🔄 Flessibilità   | Cambi **tutta la famiglia di oggetti** senza toccare il resto |
| ♻️ Riutilizzabile | Le famiglie sono intercambiabili                              |
| 🧪 Testabile      | Ogni componente può essere testato e mockato separatamente    |

---
