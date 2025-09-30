# 🧩 Strategy Pattern in Spring Boot

---

## 1️⃣ **Interfaccia Strategy**

```java
public interface PaymentStrategy {
    void pay(double amount);
}
```

---

## 2️⃣ **Strategie concrete**

```java
import org.springframework.stereotype.Component;

@Component
public class CreditCardPaymentStrategy implements PaymentStrategy {
    @Override
    public void pay(double amount) {
        System.out.println("Paying " + amount + " with Credit Card.");
    }
}

@Component
public class PaypalPaymentStrategy implements PaymentStrategy {
    @Override
    public void pay(double amount) {
        System.out.println("Paying " + amount + " with PayPal.");
    }
}
```

---

## 3️⃣ **Context che utilizza la strategia**

```java
import org.springframework.stereotype.Component;

@Component
public class PaymentContext {

    private final CreditCardPaymentStrategy creditCardPayment;
    private final PaypalPaymentStrategy paypalPayment;

    private PaymentStrategy strategy;

    public PaymentContext(CreditCardPaymentStrategy creditCardPayment,
                          PaypalPaymentStrategy paypalPayment) {
        this.creditCardPayment = creditCardPayment;
        this.paypalPayment = paypalPayment;
    }

    public void setStrategy(String paymentMethod) {
        switch (paymentMethod) {
            case "creditCard" -> this.strategy = creditCardPayment;
            case "paypal" -> this.strategy = paypalPayment;
            default -> throw new IllegalArgumentException("Invalid payment method");
        }
    }

    public void pay(double amount) {
        if (strategy == null) {
            System.out.println("No payment method selected");
            return;
        }
        strategy.pay(amount);
    }
}
```

---

## 4️⃣ **Servizio che utilizza il Context**

```java
import org.springframework.stereotype.Service;

@Service
public class PaymentService {

    private final PaymentContext paymentContext;

    public PaymentService(PaymentContext paymentContext) {
        this.paymentContext = paymentContext;
    }

    public void executePayments() {
        paymentContext.setStrategy("creditCard");
        paymentContext.pay(100.0);

        paymentContext.setStrategy("paypal");
        paymentContext.pay(200.0);
    }
}
```

---

## 5️⃣ **Avvio dell’applicazione**

```java
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class App implements CommandLineRunner {

    private final PaymentService paymentService;

    public App(PaymentService paymentService) {
        this.paymentService = paymentService;
    }

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }

    @Override
    public void run(String... args) {
        paymentService.executePayments();
    }
}
```

---

## ✅ Confronto Jakarta EE vs Spring Boot

| Feature          | Jakarta EE (CDI)                 | Spring Boot                           |
| ---------------- | -------------------------------- | ------------------------------------- |
| Definizione Bean | `@ApplicationScoped`             | `@Component` / `@Service`             |
| Iniezione        | `@Inject`                        | Costructor Injection (default)        |
| Factory dinamica | `PaymentContext` + logica        | `PaymentContext` + logica             |
| Scope singleton  | Default con `@ApplicationScoped` | Default con `@Component` / `@Service` |

---
