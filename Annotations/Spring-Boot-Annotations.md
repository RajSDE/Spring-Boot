---

# ✅ Spring Boot Important Annotations with Use Cases

---

## 1. **Core Spring Annotations**

| Annotation | Use Case | Example |
|:-----------|:---------|:--------|
| `@SpringBootApplication` | Marks the main class of a Spring Boot app. Combines `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan`. | `@SpringBootApplication public class App {}` |
| `@ComponentScan` | Tells Spring where to search for components (@Component, @Service, @Repository, etc.) | `@ComponentScan(basePackages = "com.project")` |
| `@Configuration` | Marks a class as source of bean definitions. | `@Configuration public class AppConfig {}` |
| `@Bean` | Creates a Spring-managed bean manually. | `@Bean public RestTemplate restTemplate() { return new RestTemplate(); }` |
| `@Value` | Injects values from `application.properties`. | `@Value("${db.url}") private String dbUrl;` |
| `@PropertySource` | Loads a properties file manually. | `@PropertySource("classpath:custom.properties")` |

---

## 2. **Dependency Injection Annotations**

| Annotation | Use Case | Example |
|:-----------|:---------|:--------|
| `@Autowired` | Injects a dependency automatically. | `@Autowired private Service service;` |
| `@Qualifier` | When multiple beans of same type exist, choose the correct one. | `@Qualifier("myService")` |
| `@Primary` | If multiple beans exist, this one will be picked by default. | `@Primary @Bean public Service defaultService()` |
| `@Lazy` | Create bean lazily (only when needed). | `@Lazy @Autowired private HeavyService heavyService;` |

---

## 3. **Stereotype Annotations**

| Annotation | Use Case | Example |
|:-----------|:---------|:--------|
| `@Component` | Generic Spring-managed bean. | `@Component public class Helper {}` |
| `@Service` | Marks a **Service layer** class. (business logic) | `@Service public class UserService {}` |
| `@Repository` | Marks a **DAO layer** class, with automatic exception translation. | `@Repository public class UserRepository {}` |
| `@Controller` | Defines a **Web MVC controller**. | `@Controller public class HomeController {}` |
| `@RestController` | `@Controller + @ResponseBody` = Simplified REST APIs. | `@RestController public class ApiController {}` |

---

## 4. **Spring MVC/Web Annotations**

| Annotation | Use Case | Example |
|:-----------|:---------|:--------|
| `@RequestMapping` | Maps HTTP requests to controller methods. | `@RequestMapping("/users")` |
| `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@PatchMapping` | Shortcut for HTTP verbs. | `@GetMapping("/getUser")` |
| `@RequestBody` | Binds request body (JSON) to a Java object. | `@PostMapping("/save") public ResponseEntity save(@RequestBody User user)` |
| `@ResponseBody` | Directly return the object as JSON/XML response. | `@ResponseBody public User getUser()` |
| `@PathVariable` | Bind URL path params. | `@GetMapping("/user/{id}") public User get(@PathVariable Long id)` |
| `@RequestParam` | Bind query params. | `@GetMapping("/search") public List<User> find(@RequestParam String name)` |
| `@CrossOrigin` | Allow CORS for APIs. | `@CrossOrigin(origins = "http://localhost:3000")` |

---

## 5. **Spring Data JPA Annotations**

| Annotation | Use Case | Example |
|:-----------|:---------|:--------|
| `@Entity` | Marks a class as a JPA Entity (DB Table). | `@Entity public class User {}` |
| `@Table` | Customize the table name. | `@Table(name = "users")` |
| `@Id` | Marks the primary key. | `@Id private Long id;` |
| `@GeneratedValue` | Auto-generates PKs (auto-increment etc.). | `@GeneratedValue(strategy = GenerationType.IDENTITY)` |
| `@Column` | Maps fields to DB columns (customize). | `@Column(name = "email_id")` |
| `@OneToOne`, `@OneToMany`, `@ManyToOne`, `@ManyToMany` | Defines entity relationships. | `@OneToMany(mappedBy = "user") List<Order> orders;` |
| `@Repository` | Marks a JPA repository. (Spring auto-implements interfaces.) | `@Repository interface UserRepository extends JpaRepository<User, Long>` |

---

## 6. **Validation Annotations (Hibernate Validator / Jakarta Bean Validation)**

| Annotation | Use Case | Example |
|:-----------|:---------|:--------|
| `@Valid` | Trigger validation on an object (with annotations inside). | `public ResponseEntity save(@Valid @RequestBody User user)` |
| `@NotNull` | Field must not be null. | `@NotNull private String name;` |
| `@NotEmpty` | String/Collection must not be empty. | `@NotEmpty private String email;` |
| `@Size` | Define string/collection size limits. | `@Size(min = 2, max = 10) private String username;` |
| `@Email` | Validate email format. | `@Email private String email;` |
| `@Pattern` | Match regex. | `@Pattern(regexp = "^[0-9]{10}$") private String phone;` |

---

## 7. **Exception Handling Annotations**

| Annotation | Use Case | Example |
|:-----------|:---------|:--------|
| `@ControllerAdvice` | Global exception handler (for all controllers). | `@ControllerAdvice public class GlobalExceptionHandler {}` |
| `@ExceptionHandler` | Handle specific exceptions in a method. | `@ExceptionHandler(ResourceNotFoundException.class)` |
| `@ResponseStatus` | Send specific HTTP status on exception. | `@ResponseStatus(HttpStatus.NOT_FOUND)` |

---

## 8. **Security Annotations (Spring Security)**

| Annotation | Use Case | Example |
|:-----------|:---------|:--------|
| `@EnableWebSecurity` | Enable Spring Security config. | `@EnableWebSecurity public class SecurityConfig extends WebSecurityConfigurerAdapter {}` |
| `@PreAuthorize` | Method-level security before method executes. | `@PreAuthorize("hasRole('ADMIN')")` |
| `@PostAuthorize` | Security after method execution. | `@PostAuthorize("returnObject.owner == authentication.name")` |
| `@Secured` | Simple role-based method security. | `@Secured("ROLE_ADMIN")` |
| `@RolesAllowed` | Role access with JSR-250 standard. | `@RolesAllowed("ROLE_USER")` |

---

## 9. **AOP (Aspect-Oriented Programming) Annotations**

| Annotation | Use Case | Example |
|:-----------|:---------|:--------|
| `@EnableAspectJAutoProxy` | Enable AOP proxy creation. | `@EnableAspectJAutoProxy` |
| `@Aspect` | Marks a class as AOP Aspect. | `@Aspect public class LoggingAspect {}` |
| `@Before`, `@After`, `@AfterReturning`, `@AfterThrowing`, `@Around` | Define AOP Advices around join points. | `@Before("execution(* com.app.service.*.*(..))")` |

---

## 10. **Scheduling & Async Annotations**

| Annotation | Use Case | Example |
|:-----------|:---------|:--------|
| `@EnableScheduling` | Enables scheduled tasks. | `@EnableScheduling` |
| `@Scheduled` | Run method on schedule (cron/fixed rate). | `@Scheduled(cron = "0 0 * * * *")` |
| `@EnableAsync` | Enables asynchronous method execution. | `@EnableAsync` |
| `@Async` | Run method asynchronously (in background). | `@Async public void sendEmail()` |

---

# 🔥 **Pro Tips for Production Level**
- Always use `@RestController` instead of `@Controller + @ResponseBody` for pure REST APIs.
- Always validate request body with `@Valid` to prevent bad data at controller level.
- Use `@ControllerAdvice` and `@ExceptionHandler` for **centralized exception handling**.
- Prefer constructor injection (`@Autowired` on constructor) for better testability.
- Manage custom properties properly with `@ConfigurationProperties`.
- Use AOP (`@Aspect`) for cross-cutting concerns like logging, security, transactions.

---

# 📌 Conclusion

This list covers **almost 90% of real-world production use-cases** in Spring Boot backend applications.  
Whenever building a microservice or backend, these annotations and patterns will **make your code clean, scalable, and enterprise-ready**.

---
