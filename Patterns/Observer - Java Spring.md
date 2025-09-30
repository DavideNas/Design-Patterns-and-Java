# 🧬 Observer Pattern in Spring Boot

---

## 🧠 **Definizione**

> Il **Observer Pattern** permette a un oggetto (**Subject**) di notificare automaticamente altri oggetti (**Observer**) quando il suo stato cambia.

In Spring Boot possiamo implementarlo facilmente con **Eventi e ApplicationEventPublisher**.

---

## 1️⃣ **Definizione dell’evento**

```java
import org.springframework.context.ApplicationEvent;

public class CounterEvent extends ApplicationEvent {
    private final int value;

    public CounterEvent(Object source, int value) {
        super(source);
        this.value = value;
    }

    public int getValue() {
        return value;
    }
}
```

---

## 2️⃣ **Subject (Publisher)**

```java
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.stereotype.Component;

@Component
public class CounterSubject {
    private int counter = 0;
    private final ApplicationEventPublisher publisher;

    public CounterSubject(ApplicationEventPublisher publisher) {
        this.publisher = publisher;
    }

    public void increment() {
        counter++;
        publisher.publishEvent(new CounterEvent(this, counter));
    }

    public void decrement() {
        counter--;
        publisher.publishEvent(new CounterEvent(this, counter));
    }

    public int getCounter() {
        return counter;
    }
}
```

---

## 3️⃣ **Observer (Listener)**

```java
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;

@Component
public class CounterDisplay {

    private int currentValue;

    @EventListener
    public void handleCounterEvent(CounterEvent event) {
        this.currentValue = event.getValue();
        System.out.println("Counter updated: " + currentValue);
    }

    public int getCurrentValue() {
        return currentValue;
    }
}
```

---

## 4️⃣ **Esempio d’uso**

```java
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class ObserverDemoApplication implements CommandLineRunner {

    private final CounterSubject counterSubject;

    public ObserverDemoApplication(CounterSubject counterSubject) {
        this.counterSubject = counterSubject;
    }

    public static void main(String[] args) {
        SpringApplication.run(ObserverDemoApplication.class, args);
    }

    @Override
    public void run(String... args) {
        counterSubject.increment(); // Notifica l'observer
        counterSubject.increment();
        counterSubject.decrement();
    }
}
```

---

## ✅ Confronto Jakarta EE vs Spring Boot

| Feature             | Jakarta EE                      | Spring Boot                         |
| ------------------- | ------------------------------- | ----------------------------------- |
| Subject / Publisher | Lista manuale di Observer       | `ApplicationEventPublisher`         |
| Observer / Listener | `@ApplicationScoped` + Observer | `@Component` + `@EventListener`     |
| Iniezione           | `@Inject`                       | `@Autowired` / constructor          |
| Notifica            | `notifyObservers()`             | `publishEvent()`                    |
| Scope               | `@ApplicationScoped`            | singleton di default (`@Component`) |

---
