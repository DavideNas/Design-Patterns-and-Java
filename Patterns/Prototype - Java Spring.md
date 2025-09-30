# 🧬 Prototype Pattern in Spring Boot / Java

---

## 🧠 **Definizione**

> Il **Prototype Pattern** consente di **clonare** oggetti già esistenti invece di crearli da zero. Utile quando l’**istanza è costosa da creare** o richiede molte configurazioni.

---

## 🎯 Scenario

Supponiamo di avere un oggetto `Product` come template. Vogliamo creare **copie personalizzate** senza ricrearlo da zero, integrabile in un contesto Spring Boot.

---

## ✅ 1. **Definizione dell’interfaccia `Clonable`**

```java
public interface Clonable<T> {
    T clone();
}
```

---

## 📦 2. **Classe `Product` che implementa `Clonable`**

```java
import java.util.ArrayList;
import java.util.List;

public class Product implements Clonable<Product> {
    private String name;
    private double price;
    private String description;
    private List<String> tags;

    public Product(String name, double price, String description, List<String> tags) {
        this.name = name;
        this.price = price;
        this.description = description;
        this.tags = new ArrayList<>(tags); // deep copy
    }

    @Override
    public Product clone() {
        return new Product(name, price, description, new ArrayList<>(tags));
    }

    // Getters e setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public double getPrice() { return price; }
    public void setPrice(double price) { this.price = price; }

    public String getDescription() { return description; }
    public void setDescription(String description) { this.description = description; }

    public List<String> getTags() { return tags; }
    public void setTags(List<String> tags) { this.tags = tags; }
}
```

---

## 🧪 3. **Servizio Spring per clonare prodotti**

```java
import org.springframework.stereotype.Service;
import java.util.Arrays;

@Service
public class ProductService {

    private final Product baseProduct = new Product(
        "Laptop Pro",
        1299,
        "Un potente laptop per sviluppatori.",
        Arrays.asList("electronics", "laptop", "developer")
    );

    public Product createClonedProduct(String name) {
        Product cloned = baseProduct.clone();
        cloned.setName(name);
        cloned.getTags().add("clone");
        return cloned;
    }
}
```

---

## 🚀 4. **Uso in Spring Boot Application**

```java
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class App implements CommandLineRunner {

    private final ProductService productService;

    public App(ProductService productService) {
        this.productService = productService;
    }

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }

    @Override
    public void run(String... args) {
        Product cloned = productService.createClonedProduct("Laptop Pro (Copy)");
        System.out.println("Cloned: " + cloned.getName() + " Tags: " + cloned.getTags());
    }
}
```

---

## ✅ Confronto Jakarta EE vs Spring Boot

| Feature            | Jakarta EE (CDI)     | Spring Boot                          |
| ------------------ | -------------------- | ------------------------------------ |
| Prototype dinamico | standard Java + CDI  | standard Java + Spring Service       |
| Iniezione          | `@Inject`            | `@Autowired` / constructor injection |
| Scope              | `@ApplicationScoped` | singleton di default (`@Service`)    |
| Gestione template  | variabili locali     | variabili di servizio singleton      |
