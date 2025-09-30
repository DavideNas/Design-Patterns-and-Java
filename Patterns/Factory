# 🏭 **Factory Method Pattern in Java**

## 🎯 Scenario

Vogliamo creare dinamicamente diversi tipi di `Logger` (`ConsoleLogger`, `ApiLogger`) senza che il client sappia quale istanziare.

---

## 🔧 Step 1: **Definizione interfaccia Logger**

```java
public interface Logger {
    void log(String message);
}
```

---

## 🧱 Step 2: **Implementazioni concrete**

```java
public class ConsoleLogger implements Logger {
    @Override
    public void log(String message) {
        System.out.println("[Console] " + message);
    }
}

public class ApiLogger implements Logger {
    @Override
    public void log(String message) {
        // Simulazione chiamata API
        System.out.println("[API] " + message + " (simulazione invio)");
    }
}
```

---

## 🏭 Step 3: **Factory**

```java
public class LoggerFactory {

    public static Logger getLogger(String type) {
        switch (type.toLowerCase()) {
            case "console":
                return new ConsoleLogger();
            case "api":
                return new ApiLogger();
            default:
                throw new IllegalArgumentException("Unknown logger type: " + type);
        }
    }
}
```

---

## 🚀 Step 4: **Utilizzo nel client**

```java
public class FactoryDemo {
    public static void main(String[] args) {
        // Il tipo potrebbe arrivare da un file di config, variabile d'ambiente o API
        String loggerType = "api"; 

        Logger logger = LoggerFactory.getLogger(loggerType);
        logger.log("Factory pattern test in Java!");
    }
}
```

---

# 🏭 **Variante con Dependency Injection (simile a Angular useFactory)**

Se vuoi una trasposizione più vicina all’approccio Angular con `useFactory`, puoi usare **Spring** in Java:

---

## ⚙️ 1. **Interfaccia**

```java
public interface Logger {
    void log(String message);
}
```

---

## 🧱 2. **Implementazioni**

```java
import org.springframework.stereotype.Component;

@Component
public class ConsoleLogger implements Logger {
    @Override
    public void log(String message) {
        System.out.println("[Console] " + message);
    }
}

@Component
public class ApiLogger implements Logger {
    @Override
    public void log(String message) {
        System.out.println("[API] " + message + " (simulazione invio)");
    }
}
```

---

## 🏗️ 3. **Factory Bean con configurazione dinamica**

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class LoggerConfig {

    private final String loggerType = "api"; // es. da application.properties

    @Bean
    public Logger logger() {
        switch (loggerType.toLowerCase()) {
            case "console":
                return new ConsoleLogger();
            case "api":
                return new ApiLogger();
            default:
                throw new IllegalArgumentException("Unsupported logger type: " + loggerType);
        }
    }
}
```

---

## 🚀 4. **Uso in un componente Spring**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class LogService {

    private final Logger logger;

    @Autowired
    public LogService(Logger logger) {
        this.logger = logger;
    }

    public void sendLog() {
        logger.log("Messaggio dal servizio Spring con factory method!");
    }
}
```

---

## ✅ Confronto Angular vs Java

| Angular (`useFactory`) | Java (Spring `@Bean`)      |
| ---------------------- | -------------------------- |
| `InjectionToken`       | `@Bean` configuration      |
| `useFactory`           | `@Bean` con logica runtime |
| `providers`            | `@Configuration` class     |
| `@Inject(Logger)`      | `@Autowired Logger`        |

---
