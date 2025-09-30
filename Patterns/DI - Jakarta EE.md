# 🧩 Dependency Injection (DI) – Jakarta EE

## 📌 Descrizione

La **Dependency Injection** è un **pattern di progettazione** che permette di **iniettare le dipendenze** di un oggetto dall’esterno, anziché crearle internamente.

- Riduce **accoppiamento** e aumenta **testabilità**.
- In Jakarta EE è gestita principalmente tramite **CDI (Contexts and Dependency Injection)**.
- Le dipendenze vengono tipicamente dichiarate con **`@Inject`**, e i bean possono essere gestiti dal **container**.

---

## 🔁 Flusso tipico DI in Jakarta EE

1. Il container Jakarta EE (**GlassFish, WildFly, Payara**) crea un bean.
2. Analizza i punti di iniezione (`@Inject`).
3. Risolve le dipendenze in base a tipo, nome o qualifier.
4. Inietta i bean necessari prima che l’oggetto sia utilizzato.

---

## 🛠️ Concetti chiave

1. **Bean** – Classe gestita dal container.

   - Può essere annotata con `@ApplicationScoped`, `@RequestScoped`, `@Singleton`, ecc.

2. **Injection point** – Punto in cui un bean viene iniettato.

   - Si utilizza l’annotazione `@Inject`.

3. **Qualifier** – Permette di distinguere più implementazioni dello stesso tipo.

   - Si utilizza `@Named` o `@Qualifier`.

4. **Producer** – Permette di creare bean dinamicamente o con logica complessa.

   - Si utilizza `@Produces`.

---

## 🔁 Esempio minimale di DI – Jakarta EE

### 1️⃣ Service Bean

```java
import jakarta.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class ProductService {
    public String getWelcomeMessage() {
        return "Benvenuto ai prodotti!";
    }
}
```

### 2️⃣ Controller / Managed Bean

```java
import jakarta.inject.Inject;
import jakarta.enterprise.context.RequestScoped;

@RequestScoped
public class ProductController {

    @Inject
    private ProductService service;

    public void printMessage() {
        System.out.println(service.getWelcomeMessage());
    }
}
```

### 3️⃣ Flusso

- Il container crea `ProductController` per ogni richiesta (`@RequestScoped`).
- Individua `@Inject ProductService` e fornisce il bean `ProductService` (`@ApplicationScoped`).
- `ProductController` può usare `service` senza doverlo istanziare manualmente.

---

## 🔁 Con esempio con Qualifier

Se ci sono **più implementazioni**, si usa un **qualifier**:

```java
import jakarta.inject.Qualifier;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Qualifier
@Retention(RetentionPolicy.RUNTIME)
public @interface Fast {}
```

```java
@ApplicationScoped
@Fast
public class FastProductService extends ProductService {}
```

```java
@Inject
@Fast
private ProductService service; // Inietta FastProductService
```

---

## ✅ Punti chiave DI in Jakarta EE

- Riduce accoppiamento tra classi.
- Aumenta **testabilità** (puoi mockare i bean in test).
- Gestione automatica **scopes** (`@RequestScoped`, `@ApplicationScoped`, ecc.).
- Possibilità di **iniettare interfacce**, non solo classi concrete.
- Supporto per **producers**, **qualifiers**, **alternatives**, e **decorators**.

---

## 📊 Tabella di confronto – Jakarta EE vs Spring Boot

| Caratteristica             | Jakarta EE (CDI)                                         | Spring Boot                                       |
| -------------------------- | -------------------------------------------------------- | ------------------------------------------------- |
| Annotazione DI             | `@Inject`                                                | `@Autowired`                                      |
| Scope                      | `@ApplicationScoped`, `@RequestScoped`, `@SessionScoped` | `@Singleton` (default), `@Scope("request")`, ecc. |
| Qualifier / selezione bean | `@Qualifier`, `@Named`                                   | `@Qualifier`, `@Primary`                          |
| Producer / Factory         | `@Produces`                                              | `@Bean` (in `@Configuration`)                     |
| Managed bean               | CDI gestisce il ciclo di vita                            | Spring container gestisce il bean                 |
| Testabilità                | Mock con CDI Unit o Weld                                 | Mock con Mockito + Spring Test                    |
| Iniezione in costruttore   | Possibile, preferibile usare setter/field                | Possibile, molto comune                           |
| Container                  | GlassFish, WildFly, Payara                               | Spring ApplicationContext                         |

---
