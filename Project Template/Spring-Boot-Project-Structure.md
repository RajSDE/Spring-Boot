---

# ✅ Spring Boot Template 
---

## 1. **Project: Directory Structure**

```
src/main/java/com/example/demo
│
├── config
│   ├── AppConfig.java
│   ├── CacheConfig.java
│   ├── SecurityConfig.java
│   └── SwaggerConfig.java
│
├── controller
│   ├── ApiResponse.java
│   ├── exception
│   │   ├── GlobalExceptionHandler.java
│   │   ├── ResourceNotFoundException.java
│   │   └── CustomException.java
│   └── v1
│       └── DemoController.java
│
├── service
│   ├── impl
│   │   └── DemoServiceImpl.java
│   └── DemoService.java
│
├── repository
│   └── DemoRepository.java
│
├── model
│   ├── entity
│   │   └── DemoEntity.java
│   └── dto
│       ├── request
│       │   └── DemoRequest.java
│       └── response
│           └── DemoResponse.java
│
├── util
│   └── Constants.java
│
└── DemoApplication.java
```

---

# 2. **Complete Code Layer by Layer**

---

## 2.1 `DemoApplication.java`

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

---

## 2.2 `config/AppConfig.java`

```java
package com.example.demo.config;

import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {
    // General Beans if needed
}
```

---

## 2.3 `config/CacheConfig.java`

```java
package com.example.demo.config;

import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableCaching
public class CacheConfig {
    // Configuration for Caching if needed
}
```

---

## 2.4 `config/SecurityConfig.java`

```java
package com.example.demo.config;

import org.springframework.context.annotation.*;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        return http
                .csrf().disable()
                .authorizeHttpRequests(auth -> auth
                        .requestMatchers("/swagger-ui/**", "/v3/api-docs/**", "/api/v1/demo/public").permitAll()
                        .anyRequest().authenticated()
                )
                .httpBasic()
                .and()
                .build();
    }
}
```

---

## 2.5 `config/SwaggerConfig.java`

```java
package com.example.demo.config;

import io.swagger.v3.oas.models.*;
import io.swagger.v3.oas.models.info.Info;
import org.springframework.context.annotation.*;

@Configuration
public class SwaggerConfig {

    @Bean
    public OpenAPI customOpenAPI() {
        return new OpenAPI()
            .info(new Info()
                .title("Demo API")
                .version("1.0")
                .description("Demo API Documentation"));
    }
}
```

---
## 2.6 `controller/ApiResponse.java`

```java
package com.example.demo.controller;

import lombok.*;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class ApiResponse<T> {
    private boolean success;
    private String message;
    private T data;
}
```

---

## 2.7 `controller/exception/CustomException.java`

```java
package com.example.demo.controller.exception;

public class CustomException extends RuntimeException {
    public CustomException(String message) {
        super(message);
    }
}
```

---

## 2.8 `controller/exception/ResourceNotFoundException.java`

```java
package com.example.demo.controller.exception;

public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```

---

## 2.9 `controller/exception/GlobalExceptionHandler.java`

```java
package com.example.demo.controller.exception;

import com.example.demo.controller.ApiResponse;
import org.springframework.http.*;
import org.springframework.web.bind.annotation.*;

@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ApiResponse<String>> handleResourceNotFound(ResourceNotFoundException ex) {
        return new ResponseEntity<>(ApiResponse.<String>builder()
                .success(false)
                .message(ex.getMessage())
                .build(), HttpStatus.NOT_FOUND);
    }

    @ExceptionHandler(CustomException.class)
    public ResponseEntity<ApiResponse<String>> handleCustomException(CustomException ex) {
        return new ResponseEntity<>(ApiResponse.<String>builder()
                .success(false)
                .message(ex.getMessage())
                .build(), HttpStatus.BAD_REQUEST);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ApiResponse<String>> handleException(Exception ex) {
        return new ResponseEntity<>(ApiResponse.<String>builder()
                .success(false)
                .message("Internal server error: " + ex.getMessage())
                .build(), HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

---

## 2.10 `controller/v1/DemoController.java`

```java
package com.example.demo.controller.v1;

import com.example.demo.controller.ApiResponse;
import com.example.demo.model.dto.request.DemoRequest;
import com.example.demo.model.dto.response.DemoResponse;
import com.example.demo.service.DemoService;
import jakarta.validation.Valid;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/v1/demo")
public class DemoController {

    @Autowired
    private DemoService demoService;

    @PostMapping
    public ApiResponse<DemoResponse> create(@Valid @RequestBody DemoRequest request) {
        return ApiResponse.<DemoResponse>builder()
                .success(true)
                .message("Demo created successfully")
                .data(demoService.createDemo(request))
                .build();
    }

    @GetMapping("/public")
    public String publicEndpoint() {
        return "This is public and does not require authentication.";
    }
}
```

---

## 2.11 `service/DemoService.java`

```java
package com.example.demo.service;

import com.example.demo.model.dto.request.DemoRequest;
import com.example.demo.model.dto.response.DemoResponse;

public interface DemoService {
    DemoResponse createDemo(DemoRequest request);
}
```

---

## 2.12 `service/impl/DemoServiceImpl.java`

```java
package com.example.demo.service.impl;

import com.example.demo.model.dto.request.DemoRequest;
import com.example.demo.model.dto.response.DemoResponse;
import com.example.demo.model.entity.DemoEntity;
import com.example.demo.repository.DemoRepository;
import com.example.demo.service.DemoService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class DemoServiceImpl implements DemoService {

    @Autowired
    private DemoRepository demoRepository;

    @Override
    public DemoResponse createDemo(DemoRequest request) {
        DemoEntity entity = new DemoEntity();
        entity.setName(request.getName());
        DemoEntity saved = demoRepository.save(entity);

        return DemoResponse.builder()
                .id(saved.getId())
                .name(saved.getName())
                .build();
    }
}
```

---

## 2.13 `repository/DemoRepository.java`

```java
package com.example.demo.repository;

import com.example.demo.model.entity.DemoEntity;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface DemoRepository extends JpaRepository<DemoEntity, Long> {
}
```

---

## 2.14 `model/entity/DemoEntity.java`

```java
package com.example.demo.model.entity;

import jakarta.persistence.*;
import lombok.*;

@Entity
@Table(name = "demo")
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class DemoEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;
}
```

---

## 2.15 `model/dto/request/DemoRequest.java`

```java
package com.example.demo.model.dto.request;

import jakarta.validation.constraints.NotBlank;
import lombok.Data;

@Data
public class DemoRequest {

    @NotBlank(message = "Name is mandatory")
    private String name;
}
```

---

## 2.16 `model/dto/response/DemoResponse.java`

```java
package com.example.demo.model.dto.response;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class DemoResponse {
    private Long id;
    private String name;
}
```

---

## 2.17 `util/Constants.java`

```java
package com.example.demo.util;

public class Constants {
    public static final String DEMO_CREATED = "Demo created successfully!";
}
```

---

# 3. **`src/main/resources` Setup** 

## ✅ `.yml` Based Spring Boot Config Structure

> Files inside `src/main/resources/`

```
src/main/resources
├── db
│   └── migration
│       └── V1__init.sql
├── application.yml
├── application-dev.yml
├── application-prod.yml
└── logback-spring.xml
```
---

### `db/migration/V1__init.sql`

```sql
CREATE TABLE demo (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL
);
```

---

### `application.yml`

```yaml
spring:
  profiles:
    active: dev
  datasource:
    url: jdbc:postgresql://localhost:5432/demo_db
    username: demo_user
    password: demo_pass
    driver-class-name: org.postgresql.Driver
  jpa:
    hibernate:
      ddl-auto: validate
    show-sql: true
  flyway:
    enabled: true
    locations: classpath:db/migration

logging:
  level:
    root: INFO
```

---

### `application-dev.yml`

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/demo_db_dev
    username: demo_dev
    password: demo_dev_pass
```

---

### `application-prod.yml`

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/demo_db_prod
    username: demo_prod
    password: demo_prod_pass
```

---

### `logback-spring.xml`

```xml
<configuration>

    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="info">
        <appender-ref ref="CONSOLE"/>
    </root>

</configuration>
```

---

## ✅ `.properties` Based Spring Boot Config Structure

> Files inside `src/main/resources/`

```
src/main/resources
├── application.properties
├── application-dev.properties
├── application-prod.properties
└── logback-spring.xml
```

---

### 📄 1. `application.properties` (Main config)

```properties
# Common configs
spring.profiles.active=dev

spring.datasource.url=jdbc:postgresql://localhost:5432/demo_db
spring.datasource.username=demo_user
spring.datasource.password=demo_pass
spring.datasource.driver-class-name=org.postgresql.Driver

spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=true

spring.flyway.enabled=true
spring.flyway.locations=classpath:db/migration

logging.level.root=INFO
```

✅ Here we **activate** `dev` profile by default.

---

### 📄 2. `application-dev.properties` (for Dev profile)

```properties
# Development profile-specific overrides
spring.datasource.url=jdbc:postgresql://localhost:5432/demo_db_dev
spring.datasource.username=demo_dev
spring.datasource.password=demo_dev_pass
```

✅ These **override** base datasource when profile = `dev`.

---

### 📄 3. `application-prod.properties` (for Prod profile)

```properties
# Production profile-specific overrides
spring.datasource.url=jdbc:postgresql://localhost:5432/demo_db_prod
spring.datasource.username=demo_prod
spring.datasource.password=demo_prod_pass
```

✅ These **override** when profile = `prod`.

---

# 🚀 How Spring Boot loads profile?

1. Loads `application.properties`.
2. Sees `spring.profiles.active=dev`.
3. Loads `application-dev.properties` **on top**.
4. If in prod:
   - you can pass `--spring.profiles.active=prod`
   - or set env variable `SPRING_PROFILES_ACTIVE=prod`
   - then `application-prod.properties` will override.

---

# 🧠 Important Production Tips

| Best Practice | Why |
|:--------------|:----|
| Always keep `application.properties` **minimal**. | Only put common configs. |
| Move secrets (DB passwords) to environment variables, not hardcoded. | Avoid leaking in Git. |
| In Dev, set `spring.jpa.hibernate.ddl-auto=update`. In Prod, use `validate`. | So prod DB is not modified by app. |
| For real prod, encrypt passwords (AWS KMS, Vault, etc). | Better security. |

---

# 🔥 Summary of Mapping

| `.yml` | `.properties` |
|:------|:--------------|
| Structured tree (`spring.datasource.url`) | Flat keys (`spring.datasource.url`) |
| Indentation matters | No indentation, plain |
| Easier for complex nested configs | Simple, but harder for nested configs |
| Human-readable | Machine-friendly |

**→ Functionally same. Spring Boot understands both.**

---

# 🛠 How you run different profiles?

```bash
# By default, uses dev (from spring.profiles.active)
mvn spring-boot:run

# To run PROD
mvn spring-boot:run -Dspring-boot.run.profiles=prod

# Or when jar is created
java -jar demo.jar --spring.profiles.active=prod
```

---

# 🚀 This project is now:
- ✅ Secured (Basic Auth ready)
- ✅ Fully layered (Controller, Service, Repository)
- ✅ Exception handled globally
- ✅ DTO driven (Request and Response)
- ✅ Swagger ready
- ✅ Flyway Database Migration ready
- ✅ Logging ready
- ✅ Environment Profiles (dev/prod) ready

---
