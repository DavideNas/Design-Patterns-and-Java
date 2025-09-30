# 🏭 **Factory Method Pattern in Spring Boot**

## 🎯 Scenario

Creiamo dinamicamente diversi tipi di `Logger` (`ConsoleLogger`, `ApiLogger`) senza che il client sappia quale istanziare, usando Spring Boot con `@Configuration` e `@Bean`.

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

## 🏗️ Step 3: **Factory dinamica con `@Configuration`**

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.beans.factory.annotation.Value;

@Configuration
public class LoggerConfig {

    @Value("${app.logger.type:api}") // legge da application.properties, default "api"
    private String loggerType;

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

## 🚀 Step 4: **Uso in un servizio Spring**

```java
import org.springframework.stereotype.Service;

@Service
public class LogService {

    private final Logger logger;

    public LogService(Logger logger) {
        this.logger = logger;
    }

    public void sendLog() {
        logger.log("Messaggio dal servizio Spring con Factory Method!");
    }
}
```

---

## 🧪 Step 5: **Main Application**

```java
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class App implements CommandLineRunner {

    private final LogService logService;

    public App(LogService logService) {
        this.logService = logService;
    }

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }

    @Override
    public void run(String... args) {
        logService.sendLog();
    }
}
```

---

## ⚙️ Step 6: **Configurazione `application.properties`**

```properties
# Scegli il tipo di logger: console o api
app.logger.type=console
```

Eseguendo con:

```bash
mvn spring-boot:run
```

Vedrai in console:

```
[Console] Messaggio dal servizio Spring con Factory Method!
```

---

## ✅ Confronto Jakarta EE vs Spring Boot

| Feature                 | Jakarta EE (CDI)                     | Spring Boot                          |
| ----------------------- | ------------------------------------ | ------------------------------------ |
| Factory Method dinamico | `@Produces`                          | `@Bean` method                       |
| Configurazione runtime  | Variabile locale / config + `@Named` | `@Value` + `application.properties`  |
| Iniezione               | `@Inject @Named`                     | Constructor injection (`@Autowired`) |
| Scope                   | `@ApplicationScoped`                 | Singleton di default (`@Bean`)       |

---
