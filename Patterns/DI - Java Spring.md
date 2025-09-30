# 🧩 Dependency Injection (DI) – Spring Boot

## 📌 Descrizione

La **Dependency Injection** in Spring permette di **iniettare automaticamente le dipendenze** nei bean gestiti dal **Spring IoC Container**.

- Riduce l’accoppiamento e aumenta la testabilità.
- Spring supporta **iniezione tramite field, setter o costruttore**.
- Le dipendenze sono tipicamente annotati con `@Component`, `@Service`, `@Repository`, `@Controller`, e iniettati con `@Autowired` o `@Inject`.

---

## 🔁 Concetti chiave

1. **Bean** – Classe gestita dal container (`@Component`, `@Service`, `@Repository`, ecc.).
2. **Injection point** – Punto in cui il bean viene iniettato (`@Autowired` o tramite costruttore).
3. **Qualifier** – Permette di distinguere tra più implementazioni dello stesso tipo (`@Qualifier`, `@Primary`).
4. **Configuration / @Bean** – Per creare bean personalizzati o complessi.

---

## 🔁 Esempio minimo DI – Spring Boot

### 1️⃣ Service

```java
import org.springframework.stereotype.Service;

@Service
public class ProductService {
    public String getWelcomeMessage() {
        return "Benvenuto ai prodotti!";
    }
}
```

### 2️⃣ Controller / Component

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class ProductController {

    private final ProductService service;

    @Autowired
    public ProductController(ProductService service) {
        this.service = service;
    }

    public void printMessage() {
        System.out.println(service.getWelcomeMessage());
    }
}
```

---

### 🔁 Qualifier – più implementazioni

```java
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

@Service
@Qualifier("fast")
public class FastProductService extends ProductService {}
```

```java
@Autowired
@Qualifier("fast")
private ProductService service; // Inietta FastProductService
```

---

## ✅ Punti chiave DI in Spring Boot

- Gestione centralizzata dei bean dal **Spring IoC Container**.
- Iniezione tramite **field, setter o costruttore**.
- **Scopes** gestibili con `@Scope` (`singleton`, `prototype`, `request`, `session`, ecc.).
- Qualifiers e `@Primary` per distinguere implementazioni multiple.
- Configurazione di bean complessi tramite `@Configuration` + `@Bean`.
- Testabilità semplice con **Spring Test + Mockito**.

---

## 📊 Tabella di confronto – Jakarta EE vs Spring Boot

| Caratteristica             | Jakarta EE (CDI)                                         | Spring Boot                                       |
| -------------------------- | -------------------------------------------------------- | ------------------------------------------------- |
| Annotazione DI             | `@Inject`                                                | `@Autowired` (o `@Inject`)                        |
| Scope                      | `@ApplicationScoped`, `@RequestScoped`, `@SessionScoped` | `@Singleton` (default), `@Scope("request")`, ecc. |
| Qualifier / selezione bean | `@Qualifier`, `@Named`                                   | `@Qualifier`, `@Primary`                          |
| Producer / Factory         | `@Produces`                                              | `@Bean` in `@Configuration`                       |
| Managed bean               | Container CDI gestisce ciclo di vita                     | Spring IoC Container gestisce bean                |
| Testabilità                | Mock con CDI Unit o Weld                                 | Mock con Spring Test + Mockito                    |
| Iniezione in costruttore   | Possibile, spesso setter/field                           | Possibile, molto comune (consigliata)             |
| Container                  | GlassFish, WildFly, Payara                               | Spring ApplicationContext                         |

---
