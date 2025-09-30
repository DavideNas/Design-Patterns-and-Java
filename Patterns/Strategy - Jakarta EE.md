# 🧩 Strategy Pattern in Jakarta EE

---

## 🧠 **Definizione**

> Il **Strategy Pattern** permette di definire una famiglia di algoritmi intercambiabili, separando la logica del comportamento dal contesto che lo utilizza.
> È utile quando un oggetto deve cambiare dinamicamente il comportamento senza modificare il client.

---

## 1️⃣ **Definizione dell’interfaccia Strategy**

```java
public interface PaymentStrategy {
    void pay(double amount);
}
```

---

## 2️⃣ **Strategie concrete**

```java
import jakarta.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class CreditCardPaymentStrategy implements PaymentStrategy {

    @Override
    public void pay(double amount) {
        System.out.println("Paying " + amount + " with Credit Card.");
    }
}

@ApplicationScoped
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
import jakarta.inject.Inject;
import jakarta.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class PaymentContext {

    private PaymentStrategy strategy;

    @Inject
    private CreditCardPaymentStrategy creditCardPayment;

    @Inject
    private PaypalPaymentStrategy paypalPayment;

    // Imposta la strategia dinamicamente
    public void setStrategy(String paymentMethod) {
        switch (paymentMethod) {
            case "creditCard":
                this.strategy = creditCardPayment;
                break;
            case "paypal":
                this.strategy = paypalPayment;
                break;
            default:
                throw new IllegalArgumentException("Invalid payment method");
        }
    }

    // Esegue il pagamento usando la strategia selezionata
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

## 4️⃣ **Esempio di utilizzo nel client Jakarta EE**

```java
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;

@ApplicationScoped
public class PaymentService {

    @Inject
    private PaymentContext paymentContext;

    public void executePayments() {
        paymentContext.setStrategy("creditCard");
        paymentContext.pay(100.0);

        paymentContext.setStrategy("paypal");
        paymentContext.pay(200.0);
    }
}
```

---

### ✅ **Spiegazione**

- `PaymentContext` è il **Context** che utilizza la strategia corrente.
- `PaymentStrategy` è l’interfaccia comune.
- `CreditCardPaymentStrategy` e `PaypalPaymentStrategy` sono le strategie concrete.
- Il client (`PaymentService`) seleziona dinamicamente la strategia senza modificare il codice della logica di pagamento.

---
