Perfetto 👍 Ti preparo la **trasposizione del Singleton Pattern in Java**, con lo stesso scenario che hai mostrato in Angular (un contatore globale delle richieste API).

---

# 🔒 **Singleton Pattern in Java**

## ✅ Definizione

Il **Singleton** è un pattern creazionale che assicura l’esistenza di **una sola istanza** di una classe in tutto il programma, fornendo un punto di accesso globale.

---

## 🎯 Scenario

Vogliamo un `LoggerService` che conti quante volte viene invocato il log.
Tutte le parti del programma devono condividere lo **stesso contatore**.


---

## 📦 `LoggerService.java`

```java
public class LoggerService {
    // 1️⃣ Istanza unica, privata e statica
    private static LoggerService instance;

    // 2️⃣ Contatore interno
    private int counter = 0;

    // 3️⃣ Costruttore privato per evitare istanziazioni esterne
    private LoggerService() {}

    // 4️⃣ Metodo statico per ottenere l'istanza unica (lazy initialization)
    public static synchronized LoggerService getInstance() {
        if (instance == null) {
            instance = new LoggerService();
        }
        return instance;
    }

    // 5️⃣ Metodo log
    public void log(String message) {
        counter++;
        System.out.println("[" + counter + "] - " + message);
    }

    // 6️⃣ Getter per il contatore
    public int getCount() {
        return counter;
    }
}
```

---

## 🧪 Uso nel client

```java
public class HomeComponent {
    public static void main(String[] args) {
        // Recupero l'unica istanza
        LoggerService logger = LoggerService.getInstance();

        logger.log("Clicked log button");
        logger.log("Another action");

        System.out.println("Logs: " + logger.getCount());

        // Dimostrazione che è sempre la stessa istanza
        LoggerService logger2 = LoggerService.getInstance();
        logger2.log("Log from another class");

        System.out.println("Logs (da logger2): " + logger2.getCount());
    }
}
```

---

## 🎯 Cosa succede?

* Tutti i client chiamano sempre `LoggerService.getInstance()`.
* La stessa istanza viene riutilizzata, quindi il contatore non si resetta.
* Se provi a creare più "componenti" che usano il logger, vedrai che il conteggio è globale.

---

## 🚫 Anti-pattern (NO Singleton)

Se facessi così:

```java
LoggerService logger1 = new LoggerService(); // ERRORE: costruttore privato
```

Non funzionerebbe perché il costruttore è privato.
Questo impedisce di avere più istanze sparse per il programma.

---

## 📌 TL;DR

| Comportamento     | Configurazione Java                                |
| ----------------- | -------------------------------------------------- |
| Singleton globale | `private static instance + getInstance()`          |
| Istanze multiple  | Costruttore pubblico (anti-pattern in questo caso) |

---

## 🔒 Varianti in Java

## 1️⃣ **Eager Initialization (thread-safe di default)**

L’istanza viene creata **subito** quando la classe viene caricata.

```java
public class LoggerService {
    // Istanza unica creata subito (eager)
    private static final LoggerService instance = new LoggerService();

    private int counter = 0;

    // Costruttore privato
    private LoggerService() {}

    // Punto di accesso globale
    public static LoggerService getInstance() {
        return instance;
    }

    public void log(String message) {
        counter++;
        System.out.println("[" + counter + "] - " + message);
    }

    public int getCount() {
        return counter;
    }
}
```

👉 Pro: semplice e thread-safe.
👉 Contro: l’istanza viene creata anche se non la userai mai.

---

## 2️⃣ **Lazy Initialization (con double-checked locking)**

L’istanza viene creata **solo al primo utilizzo**, ed è thread-safe.

```java
public class LoggerService {
    private static volatile LoggerService instance;
    private int counter = 0;

    private LoggerService() {}

    public static LoggerService getInstance() {
        if (instance == null) { // Primo controllo (veloce)
            synchronized (LoggerService.class) {
                if (instance == null) { // Secondo controllo (sicuro)
                    instance = new LoggerService();
                }
            }
        }
        return instance;
    }

    public void log(String message) {
        counter++;
        System.out.println("[" + counter + "] - " + message);
    }

    public int getCount() {
        return counter;
    }
}
```

👉 Pro: thread-safe, istanza creata solo se serve.
👉 Contro: un po’ più complesso da leggere.

---

## 3️⃣ **Enum Singleton (la più sicura in Java)**

Dal punto di vista delle best practice moderne in Java, l’approccio **enum** è considerato il più robusto, perché evita problemi di serializzazione e reflection.

```java
public enum LoggerService {
    INSTANCE;

    private int counter = 0;

    public void log(String message) {
        counter++;
        System.out.println("[" + counter + "] - " + message);
    }

    public int getCount() {
        return counter;
    }
}
```

👉 Uso:

```java
public class HomeComponent {
    public static void main(String[] args) {
        LoggerService.INSTANCE.log("Clicked log button");
        LoggerService.INSTANCE.log("Another action");

        System.out.println("Logs: " + LoggerService.INSTANCE.getCount());
    }
}
```

---

📌 **Riepilogo**

| Variante              | Pro                                     | Contro                              |
| --------------------- | --------------------------------------- | ----------------------------------- |
| Eager                 | Semplice, thread-safe                   | Sempre istanziato, anche se inutile |
| Lazy (double-checked) | Efficiente, creato solo se serve        | Più complesso da scrivere           |
| Enum                  | Più sicuro (serializzazione/reflection) | Meno flessibile                     |

---
