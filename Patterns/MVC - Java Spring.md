# 🧩 Pattern MVC – Spring Boot

## 📌 Descrizione

Il **Model-View-Controller (MVC)** in Spring Boot segue la stessa filosofia: separare chiaramente responsabilità di business, presentazione e orchestrazione.

1. **Model (Modello)**

   - Contiene **lo stato e la logica di business**.
   - In Spring Boot si realizza tipicamente tramite **entità JPA**, **DTO** o **servizi**.

2. **View (Vista)**

   - Gestisce la **presentazione dei dati**.
   - In Spring Boot può essere **Thymeleaf**, JSP o altre tecnologie di template.

3. **Controller (Controllore)**

   - Riceve le richieste HTTP tramite **`@Controller`** o **`@RestController`**.
   - Interagisce con il **Model** (tipicamente tramite servizi) e invia dati alla **View**.

---

## 🔁 Flusso tipico in Spring Boot

1. L’utente invia una richiesta HTTP.
2. Il **Controller** gestisce la richiesta.
3. Il Controller chiama il **Service / Repository** per elaborare dati.
4. Il Service restituisce i dati.
5. Il Controller li passa alla **View** (Thymeleaf, JSP) o restituisce JSON/REST.

---

## 🛠️ Esempio minimo Spring Boot

### 1️⃣ Model – Entity JPA

```java
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;

    // Getter e setter
}
```

### 2️⃣ Repository – DAO Spring Data JPA

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface ProductRepository extends JpaRepository<Product, Long> {
}
```

### 3️⃣ Service – Logica di business

```java
import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class ProductService {
    private final ProductRepository repository;

    public ProductService(ProductRepository repository) {
        this.repository = repository;
    }

    public List<Product> getAllProducts() {
        return repository.findAll();
    }
}
```

### 4️⃣ Controller – `@Controller`

```java
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class ProductController {

    private final ProductService service;

    public ProductController(ProductService service) {
        this.service = service;
    }

    @GetMapping("/products")
    public String products(Model model) {
        model.addAttribute("products", service.getAllProducts());
        return "products"; // Thymeleaf template "products.html"
    }
}
```

### 5️⃣ View – Thymeleaf

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
  <head>
    <title>Products</title>
  </head>
  <body>
    <h1>Lista Prodotti</h1>
    <ul>
      <li th:each="p : ${products}">
        <span th:text="${p.name}"></span> - <span th:text="${p.price}"></span> €
      </li>
    </ul>
  </body>
</html>
```

---

## ✅ Punti chiave MVC in Spring Boot

- **Controller annotato** (`@Controller`, `@RestController`) gestisce la richiesta HTTP.
- **Service layer** per logica di business, spesso con **repository JPA**.
- **View** gestita tramite Thymeleaf o JSP.
- **Dependency Injection** nativa con `@Autowired` o constructor injection.
- Compatibile con architetture REST e microservizi.

---

# 📊 Confronto MVC – Jakarta EE vs Spring Boot

| Caratteristica          | Jakarta EE                      | Spring Boot                            |
| ----------------------- | ------------------------------- | -------------------------------------- |
| Controller              | Servlet / JAX-RS                | `@Controller` / `@RestController`      |
| Dependency Injection    | CDI (`@Inject`)                 | Spring DI (`@Autowired`) / Constructor |
| View                    | JSP / JSF / Facelets            | Thymeleaf / JSP / FreeMarker           |
| Service Layer           | EJB / CDI Beans                 | `@Service` Bean                        |
| Repository / DAO        | JPA EntityManager / DAO manuale | Spring Data JPA Repository             |
| Configurazione          | XML o annotation Jakarta EE     | Spring Boot auto-configuration         |
| REST support            | JAX-RS                          | `@RestController` + Spring MVC         |
| Trasparenza transazioni | `@Transactional` Jakarta EE     | `@Transactional` Spring                |
| Server                  | GlassFish, Payara, WildFly      | Embedded Tomcat / Jetty / Undertow     |

---
