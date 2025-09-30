# 🧩 DAO Pattern – Jakarta EE

**DAO (Data Access Object)** serve a **separare la logica di accesso al database dalla logica di business**, rendendo il codice più modulare e testabile.

---

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

    // Costruttore vuoto necessario per JPA
    public User() {}

    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }

    // getter e setter
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
}
```

---

## 2️⃣ DAO Interface

```java
import java.util.List;

public interface UserDao {
    User findById(Long id);
    List<User> findAll();
    void save(User user);
    void update(User user);
    void delete(Long id);
}
```

---

## 3️⃣ DAO Implementation con Jakarta EE

```java
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;
import jakarta.persistence.EntityManager;
import jakarta.persistence.PersistenceContext;
import jakarta.transaction.Transactional;
import java.util.List;

@ApplicationScoped
public class UserDaoImpl implements UserDao {

    @PersistenceContext(unitName = "myPU")
    private EntityManager em;

    @Override
    public User findById(Long id) {
        return em.find(User.class, id);
    }

    @Override
    public List<User> findAll() {
        return em.createQuery("SELECT u FROM User u", User.class).getResultList();
    }

    @Override
    @Transactional
    public void save(User user) {
        em.persist(user);
    }

    @Override
    @Transactional
    public void update(User user) {
        em.merge(user);
    }

    @Override
    @Transactional
    public void delete(Long id) {
        User user = em.find(User.class, id);
        if (user != null) {
            em.remove(user);
        }
    }
}
```

> Nota: `@Transactional` serve per permettere operazioni di scrittura nel database.

---

## 4️⃣ Service Layer (opzionale ma consigliato)

```java
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;
import java.util.List;

@ApplicationScoped
public class UserService {

    @Inject
    private UserDao userDao;

    public User getUser(Long id) {
        return userDao.findById(id);
    }

    public List<User> getAllUsers() {
        return userDao.findAll();
    }

    public void createUser(String name, String email) {
        userDao.save(new User(name, email));
    }

    public void updateUser(User user) {
        userDao.update(user);
    }

    public void deleteUser(Long id) {
        userDao.delete(id);
    }
}
```

---

## 5️⃣ REST Endpoint con JAX-RS

```java
import jakarta.inject.Inject;
import jakarta.ws.rs.*;
import jakarta.ws.rs.core.MediaType;
import jakarta.ws.rs.core.Response;
import java.util.List;

@Path("/users")
@Produces(MediaType.APPLICATION_JSON)
@Consumes(MediaType.APPLICATION_JSON)
public class UserResource {

    @Inject
    private UserService userService;

    @GET
    public List<User> getAll() {
        return userService.getAllUsers();
    }

    @GET
    @Path("/{id}")
    public User getById(@PathParam("id") Long id) {
        return userService.getUser(id);
    }

    @POST
    public Response create(User user) {
        userService.createUser(user.getName(), user.getEmail());
        return Response.status(Response.Status.CREATED).build();
    }

    @PUT
    @Path("/{id}")
    public Response update(@PathParam("id") Long id, User user) {
        user.setId(id);
        userService.updateUser(user);
        return Response.ok().build();
    }

    @DELETE
    @Path("/{id}")
    public Response delete(@PathParam("id") Long id) {
        userService.deleteUser(id);
        return Response.noContent().build();
    }
}
```

---

## ✅ Vantaggi della soluzione

1. **Separazione dei livelli**: DAO → Service → REST
2. **Indipendenza dal DB**: DAO può essere facilmente mockato nei test.
3. **Riutilizzabile**: puoi avere più Service che usano lo stesso DAO.
4. **Standard Jakarta EE**: usa JPA, CDI, JAX-RS senza framework esterni.

---
