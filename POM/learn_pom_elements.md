# SCOPE
In `pom.xml`, the `<scope>` tag inside a `<dependency>` defines **how and when a dependency is available** in different **build and runtime phases** of your application.

---

## ✅ Common Maven Scopes

| Scope      | Available At Compile? | Included In Final JAR/WAR? | Used At Runtime?  | Example Use Case                                |
| ---------- | --------------------- | -------------------------- | ----------------- | ----------------------------------------------- |
| `compile`  | ✅ Yes                 | ✅ Yes                      | ✅ Yes             | Business logic libraries (default)              |
| `provided` | ✅ Yes                 | ❌ No                       | ❌ No              | Servlet API (provided by container like Tomcat) |
| `runtime`  | ❌ No                  | ✅ Yes                      | ✅ Yes             | JDBC drivers, logging                           |
| `test`     | ❌ No                  | ❌ No                       | ✅ Yes (test only) | JUnit, Mockito                                  |
| `system`   | ✅ Yes                 | ❌ No                       | ❌ No              | Local JARs outside Maven repo                   |
| `import`   | —                     | —                          | —                 | Used with dependencyManagement                  |

---

## 🔍 Detailed Explanation of Each Scope

### 🔹 1. `compile` (default scope)

```xml
<scope>compile</scope>
```

* Available everywhere (compile, test, runtime).
* Default if no scope is specified.

📌 **Example**:

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.12.0</version>
</dependency>
```

---

### 🔹 2. `provided`

* Needed for **compilation**, but expected to be **provided by the environment** (e.g., servlet containers, app servers).

📌 **Use when**:

* You’re writing a **web app**, and **Tomcat/Jetty** will provide `javax.servlet` at runtime.

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope>
</dependency>
```

---

### 🔹 3. `runtime`

* Not needed for compile time, but needed during **runtime** execution.

📌 **Example**:

* JDBC drivers
* Logging implementations

```xml
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <version>42.7.2</version>
    <scope>runtime</scope>
</dependency>
```

---

### 🔹 4. `test`

* Used only in **test phase**
* Not included in build artifact or runtime

📌 **Example**:

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>5.10.2</version>
    <scope>test</scope>
</dependency>
```

---

### 🔹 5. `system` (❌ discouraged)

* Like `provided`, but **you must specify the JAR manually**.
* Not downloaded from Maven Central.

📌 **Use only when**:

* You have a proprietary or legacy JAR in your filesystem.

```xml
<dependency>
    <groupId>com.mycompany</groupId>
    <artifactId>internal-lib</artifactId>
    <version>1.0.0</version>
    <scope>system</scope>
    <systemPath>${project.basedir}/lib/internal-lib.jar</systemPath>
</dependency>
```

---

### 🔹 6. `import` (used with `<dependencyManagement>`)

* Used to import a BOM (Bill of Materials) for **version alignment**.

📌 **Example**:

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>3.2.5</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

---

## ✅ Rule of Thumb

| Situation                                 | Scope      |
| ----------------------------------------- | ---------- |
| General-purpose libs                      | `compile`  |
| Servlet API, Java EE, Spring Boot starter | `provided` |
| DB Drivers, SLF4J Binding                 | `runtime`  |
| JUnit, Mockito                            | `test`     |
| Legacy JAR on disk                        | `system`   |

---

## 🧠 Summary

* `<scope>` controls **visibility** of a dependency during `compile`, `test`, and `runtime`.
* Helps Maven include only the **right dependencies** in the final `.jar` or `.war`.
* **Avoid `system`**, use **BOM + `import`** for dependency version control.
