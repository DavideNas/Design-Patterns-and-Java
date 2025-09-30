# 🧬 Observer Pattern in Jakarta EE

---

## 🧠 **Definizione**

> Il **Observer Pattern** consente a un oggetto (**Subject**) di notificare automaticamente altri oggetti (**Observer**) quando il suo stato cambia.

In Jakarta EE, si realizza tipicamente usando **CDI** (`@Inject`) e **eventi** (`@Observes`) oppure pattern manuali con liste di osservatori.

---

## 🎯 Scenario

Vogliamo gestire un contatore globale e notificare diversi componenti ogni volta che il contatore cambia.

---

## 1️⃣ **Interfacce**

```java
public interface Observer {
    void update(int value);
}

public interface Subject {
    void addObserver(Observer observer);
    void removeObserver(Observer observer);
    void notifyObservers();
}
```

---

## 2️⃣ **Concrete Subject**

```java
import java.util.ArrayList;
import java.util.List;

public class CounterSubject implements Subject {
    private int counter = 0;
    private List<Observer> observers = new ArrayList<>();

    @Override
    public void addObserver(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }

    @Override
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(counter);
        }
    }

    // Metodi per modificare lo stato
    public void increment() {
        counter++;
        notifyObservers();
    }

    public void decrement() {
        counter--;
        notifyObservers();
    }

    public int getCounter() {
        return counter;
    }
}
```

---

## 3️⃣ **Concrete Observer**

```java
import javax.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class CounterDisplay implements Observer {
    private int currentValue;

    @Override
    public void update(int value) {
        this.currentValue = value;
        System.out.println("Counter updated: " + currentValue);
    }

    public int getCurrentValue() {
        return currentValue;
    }
}
```

---

## 4️⃣ **Esempio d’uso con CDI**

```java
import javax.inject.Inject;
import javax.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class CounterService {

    private final CounterSubject counterSubject;

    @Inject
    public CounterService(CounterSubject counterSubject, CounterDisplay display) {
        this.counterSubject = counterSubject;
        counterSubject.addObserver(display);
    }

    public void incrementCounter() {
        counterSubject.increment();
    }

    public void decrementCounter() {
        counterSubject.decrement();
    }
}
```

---

## 5️⃣ **Main Demo**

```java
import javax.enterprise.inject.se.SeContainer;
import javax.enterprise.inject.se.SeContainerInitializer;

public class ObserverDemo {

    public static void main(String[] args) {
        try (SeContainer container = SeContainerInitializer.newInstance().initialize()) {
            CounterService service = container.select(CounterService.class).get();

            service.incrementCounter(); // Notifica l'osservatore
            service.incrementCounter();
            service.decrementCounter();
        }
    }
}
```

---

## ✅ Vantaggi

| ✅  | Vantaggio   | Descrizione                                                 |
| --- | ----------- | ----------------------------------------------------------- |
| 🔄  | Decoupling  | Subject e Observer non dipendono l’uno dall’altro           |
| ⚡  | Reattività  | Gli observer sono notificati automaticamente                |
| ♻️  | Scalabilità | Aggiungere nuovi observer non richiede modifiche al Subject |
| 🧪  | Testabilità | Possibile mockare observer separatamente                    |

---
