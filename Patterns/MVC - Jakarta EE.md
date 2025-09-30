# 🧩 Pattern MVC – Jakarta EE

## 📌 Descrizione

Il **Model-View-Controller (MVC)** è un pattern architetturale che **separa chiaramente le responsabilità** di un’applicazione in tre componenti principali:

1. **Model (Modello)**

   - Contiene **lo stato e la logica di business** dell’applicazione.
   - Interagisce con il database tramite **JPA / EntityManager**.
   - Non gestisce la presentazione o l’interazione con l’utente.

2. **View (Vista)**

   - Gestisce **la presentazione dei dati** all’utente.
   - In Jakarta EE si realizza tipicamente con **JSP, Facelets o JSF**.
   - Non contiene logica di business, solo logica di presentazione.

3. **Controller (Controllore)**

   - Riceve le **richieste HTTP** dall’utente (via **Servlet o JAX-RS**)
   - Interagisce con il **Model** per elaborare dati e regole di business
   - Determina quale **View** inviare come risposta

---

## 🔁 Flusso tipico in Jakarta EE

1. L’utente invia una richiesta HTTP.
2. Il **Controller** riceve la richiesta.
3. Il Controller chiama il **Model** per ottenere i dati o eseguire logica di business.
4. Il **Model** restituisce i dati al Controller.
5. Il Controller inoltra i dati alla **View**.
6. La **View** genera l’output HTML/XML/JSON per l’utente.

---

## 🛠️ Esempio minimo Jakarta EE

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

### 2️⃣ Controller – Servlet

```java
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.util.List;

@WebServlet("/products")
public class ProductController extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {
        // Recupero dati tramite Model / DAO
        List<Product> products = ProductDAO.getAll();

        // Passaggio dati alla View
        req.setAttribute("products", products);
        req.getRequestDispatcher("/WEB-INF/views/products.jsp").forward(req, resp);
    }
}
```

### 3️⃣ View – JSP

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head><title>Products</title></head>
<body>
<h1>Lista Prodotti</h1>
<ul>
<% for(Product p : (List<Product>)request.getAttribute("products")) { %>
    <li><%= p.getName() %> - <%= p.getPrice() %> €</li>
<% } %>
</ul>
</body>
</html>
```

---

## ✅ Punti chiave di MVC in Jakarta EE

- **Separazione chiara dei ruoli**: Model = logica di business, View = presentazione, Controller = orchestrazione.
- **Scalabilità enterprise**: facile sostituire JSP con JSF, REST endpoint o altre View.
- **Integrazione con JPA e CDI**: Model e Controller possono usare pienamente Dependency Injection (`@Inject`) e gestione transazioni (`@Transactional`).
- **Standard Java EE / Jakarta EE**: compatibile con qualsiasi server certificato Jakarta EE (WildFly, Payara, GlassFish, ecc.).

---
