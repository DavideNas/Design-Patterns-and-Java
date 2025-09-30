# 🧬 Prototype Pattern in Jakarta EE / Java

---

## 🧠 **Definizione**

> Il **Prototype Pattern** consente di **clonare** oggetti già esistenti invece di crearli da zero. Utile quando l’**istanza è costosa da creare** o richiede molte configurazioni.

---

## 🎯 Scenario d’uso

Supponiamo di avere un oggetto `Product` che rappresenta un prodotto di base. Vogliamo creare **copie personalizzate** senza ricrearlo da zero.

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

## 🧪 3. **Esempio d’uso**

```java
import java.util.Arrays;

public class PrototypeDemo {
    public static void main(String[] args) {
        Product baseProduct = new Product(
            "Laptop Pro",
            1299,
            "Un potente laptop per sviluppatori.",
            Arrays.asList("electronics", "laptop", "developer")
        );

        // Clona il prodotto
        Product clonedProduct = baseProduct.clone();
        clonedProduct.setName("Laptop Pro (Copy)");
        clonedProduct.getTags().add("clone");

        System.out.println("Original: " + baseProduct.getName() + " Tags: " + baseProduct.getTags());
        System.out.println("Clone: " + clonedProduct.getName() + " Tags: " + clonedProduct.getTags());
    }
}
```

---

## ✅ Vantaggi

| ✅  | Vantaggio         | Descrizione                                         |
| --- | ----------------- | --------------------------------------------------- |
| ⚡  | Prestazioni       | Evita di ricreare l’oggetto da zero                 |
| 🧠  | Semplicità        | Clona oggetti senza conoscere tutti i dettagli      |
| 🎯  | Personalizzazione | Puoi modificare il clone senza alterare l’originale |

---

## ❗ Attenzione

- Assicurati che `clone()` realizzi una **deep copy** se l’oggetto contiene liste o altri oggetti mutabili.
- Il Prototype Pattern affianca il costruttore, non lo sostituisce.
