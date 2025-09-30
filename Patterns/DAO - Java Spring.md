# 🧩 DAO Pattern – Spring Boot

## 1️⃣ Entity JPA

```java
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;

    public User() {}

    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }

    // Getter e setter
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
}
```

---

## 2️⃣ Repository (DAO) – Spring Data JPA

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // CRUD già forniti da JpaRepository
}
```

> Nota: In Spring Boot non serve implementare manualmente le CRUD base, Spring Data JPA le fornisce automaticamente.

---

## 3️⃣ Service Layer

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public User getUser(Long id) {
        return userRepository.findById(id).orElse(null);
    }

    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    public User createUser(String name, String email) {
        return userRepository.save(new User(name, email));
    }

    public User updateUser(User user) {
        return userRepository.save(user);
    }

    public void deleteUser(Long id) {
        userRepository.deleteById(id);
    }
}
```

---

## 4️⃣ REST Controller

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping
    public List<User> getAll() {
        return userService.getAllUsers();
    }

    @GetMapping("/{id}")
    public User getById(@PathVariable Long id) {
        return userService.getUser(id);
    }

    @PostMapping
    public ResponseEntity<User> create(@RequestBody User user) {
        User created = userService.createUser(user.getName(), user.getEmail());
        return ResponseEntity.status(201).body(created);
    }

    @PutMapping("/{id}")
    public User update(@PathVariable Long id, @RequestBody User user) {
        user.setId(id);
        return userService.updateUser(user);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> delete(@PathVariable Long id) {
        userService.deleteUser(id);
        return ResponseEntity.noContent().build();
    }
}
```

---

## ✅ Confronto Spring Boot vs Jakarta EE (DAO Pattern)

| Aspetto                    | Jakarta EE                                     | Spring Boot                                   |
| -------------------------- | ---------------------------------------------- | --------------------------------------------- |
| **Gestione DAO**           | Manuale con `EntityManager` e `@Transactional` | Automatico con `JpaRepository`                |
| **Dependency Injection**   | CDI con `@Inject` e `@ApplicationScoped`       | Spring DI con `@Autowired` o costruttore      |
| **Transaction Management** | `@Transactional` Jakarta                       | `@Transactional` Spring                       |
| **REST Endpoint**          | JAX-RS (`@Path`, `@GET`, `@POST`)              | Spring MVC (`@RestController`, `@GetMapping`) |
| **Modularità**             | DAO separato, implementazione manuale          | DAO/Rrepository generato automaticamente      |
| **Scrittura boilerplate**  | Maggiore (metodi CRUD da implementare)         | Minore (CRUD generati da JpaRepository)       |

---

In sintesi:

- **Jakarta EE** richiede più codice boilerplate, ma offre pieno controllo su `EntityManager` e transazioni.
- **Spring Boot** riduce drasticamente il boilerplate grazie a **Spring Data JPA**, ma la logica CRUD è più “black-box”.

---
