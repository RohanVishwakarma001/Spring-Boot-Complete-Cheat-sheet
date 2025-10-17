# Spring Boot Complete Cheat sheet

_From Beginner to Advanced_

## Table of Contents

1. [Project Setup](#project-setup)
2. [Folder Structure](#folder-structure)
3. [Beginner Concepts](#beginner-concepts)
4. [Intermediate Concepts](#intermediate-concepts)
5. [Advanced Concepts](#advanced-concepts)
6. [Configuration](#configuration)
7. [Testing](#testing)
8. [Deployment](#deployment)
9. [Troubleshooting](#troubleshooting)

---

## Project Setup

### 1. Using Spring Initializr (Recommended)

```bash
# Visit https://start.spring.io/
# Or use curl:
curl https://start.spring.io/starter.zip \
  -d dependencies=web,data-jpa,h2,devtools \
  -d type=maven-project \
  -d language=java \
  -d bootVersion=3.2.0 \
  -d baseDir=my-spring-app \
  -o my-spring-app.zip
```

### 2. Manual Maven Setup

```xml
<!-- pom.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
        <relativePath/>
    </parent>

    <groupId>com.example</groupId>
    <artifactId>my-spring-app</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <properties>
        <java.version>17</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

### 3. Gradle Setup

```gradle
// build.gradle
plugins {
    id 'org.springframework.boot' version '3.2.0'
    id 'io.spring.dependency-management' version '1.1.4'
    id 'java'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'
java {
    sourceCompatibility = '17'
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
    useJUnitPlatform()
}
```

---

## Folder Structure

```
src/
├── main/
│   ├── java/
│   │   └── com/
│   │       └── example/
│   │           └── myapp/
│   │               ├── MyAppApplication.java          # Main application class
│   │               ├── config/                       # Configuration classes
│   │               │   ├── DatabaseConfig.java
│   │               │   ├── SecurityConfig.java
│   │               │   └── WebConfig.java
│   │               ├── controller/                    # REST controllers
│   │               │   ├── UserController.java
│   │               │   └── ProductController.java
│   │               ├── service/                       # Business logic
│   │               │   ├── UserService.java
│   │               │   └── ProductService.java
│   │               ├── repository/                    # Data access layer
│   │               │   ├── UserRepository.java
│   │               │   └── ProductRepository.java
│   │               ├── model/                        # Entity/DTO classes
│   │               │   ├── entity/
│   │               │   │   ├── User.java
│   │               │   │   └── Product.java
│   │               │   └── dto/
│   │               │       ├── UserDto.java
│   │               │       └── ProductDto.java
│   │               ├── exception/                    # Custom exceptions
│   │               │   ├── UserNotFoundException.java
│   │               │   └── GlobalExceptionHandler.java
│   │               └── util/                         # Utility classes
│   │                   ├── DateUtils.java
│   │                   └── ValidationUtils.java
│   └── resources/
│       ├── application.properties                    # Main configuration
│       ├── application-dev.properties               # Development config
│       ├── application-prod.properties              # Production config
│       ├── static/                                  # Static resources
│       │   ├── css/
│       │   ├── js/
│       │   └── images/
│       ├── templates/                               # Thymeleaf templates
│       │   ├── index.html
│       │   └── user/
│       └── db/
│           └── migration/                           # Flyway migrations
│               ├── V1__Create_users_table.sql
│               └── V2__Add_products_table.sql
└── test/
    ├── java/
    │   └── com/
    │       └── example/
    │           └── myapp/
    │               ├── MyAppApplicationTests.java
    │               ├── controller/
    │               │   └── UserControllerTest.java
    │               ├── service/
    │               │   └── UserServiceTest.java
    │               └── repository/
    │                   └── UserRepositoryTest.java
    └── resources/
        ├── application-test.properties
        └── test-data.sql
```

---

## Beginner Concepts

### 1. Main Application Class

```java
@SpringBootApplication
public class MyAppApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyAppApplication.class, args);
    }
}
```

### 2. Essential Annotations

#### Core Annotations

```java
@SpringBootApplication          // Main application annotation
@Component                      // Generic component
@Service                        // Service layer component
@Repository                     // Data access layer component
@Controller                     // Web controller
@RestController                 // REST API controller
@Configuration                  // Configuration class
@Bean                           // Method that returns a bean
@Autowired                      // Dependency injection
@Value                          // Inject property values
@Profile                        // Conditional bean creation
```

#### Web Annotations

```java
@RequestMapping                 // Map web requests
@GetMapping                     // Map GET requests
@PostMapping                    // Map POST requests
@PutMapping                     // Map PUT requests
@DeleteMapping                  // Map DELETE requests
@PathVariable                   // Extract path variables
@RequestParam                   // Extract query parameters
@RequestBody                    // Extract request body
@ResponseBody                   // Return response body
@CrossOrigin                    // Enable CORS
```

### 3. Basic REST Controller

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping
    public List<User> getAllUsers() {
        return userService.findAll();
    }

    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userService.findById(id);
    }

    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.save(user);
    }

    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody User user) {
        return userService.update(id, user);
    }

    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        userService.deleteById(id);
    }
}
```

### 4. Basic Service Layer

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public List<User> findAll() {
        return userRepository.findAll();
    }

    public User findById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException("User not found with id: " + id));
    }

    public User save(User user) {
        return userRepository.save(user);
    }

    public User update(Long id, User userDetails) {
        User user = findById(id);
        user.setName(userDetails.getName());
        user.setEmail(userDetails.getEmail());
        return userRepository.save(user);
    }

    public void deleteById(Long id) {
        userRepository.deleteById(id);
    }
}
```

### 5. Basic Entity

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(unique = true, nullable = false)
    private String email;

    // Constructors
    public User() {}

    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }

    // Getters and Setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
}
```

### 6. Basic Repository

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByName(String name);
    Optional<User> findByEmail(String email);
    List<User> findByNameContainingIgnoreCase(String name);
}
```

---

## Intermediate Concepts

### 1. JPA/Hibernate Advanced

#### Entity Relationships

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private List<Order> orders;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "department_id")
    private Department department;
}

@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id")
    private User user;

    @ManyToMany
    @JoinTable(
        name = "order_products",
        joinColumns = @JoinColumn(name = "order_id"),
        inverseJoinColumns = @JoinColumn(name = "product_id")
    )
    private Set<Product> products;
}
```

#### Custom Queries

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {

    // JPQL Query
    @Query("SELECT u FROM User u WHERE u.email = :email")
    Optional<User> findByEmailCustom(@Param("email") String email);

    // Native Query
    @Query(value = "SELECT * FROM users WHERE name LIKE %:name%", nativeQuery = true)
    List<User> findByNameLike(@Param("name") String name);

    // Method Query
    List<User> findByDepartmentName(String departmentName);

    // Pageable Query
    Page<User> findByActiveTrue(Pageable pageable);

    // Custom Query with @Modifying
    @Modifying
    @Query("UPDATE User u SET u.lastLogin = :date WHERE u.id = :id")
    int updateLastLogin(@Param("id") Long id, @Param("date") LocalDateTime date);
}
```

### 2. Spring Security

#### Basic Security Configuration

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(authz -> authz
                .requestMatchers("/api/public/**").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .httpBasic(Customizer.withDefaults())
            .csrf(csrf -> csrf.disable());

        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

#### JWT Authentication

```java
@Component
public class JwtUtil {
    private String secret = "mySecretKey";
    private int jwtExpirationInMs = 86400000; // 24 hours

    public String generateToken(UserDetails userDetails) {
        Map<String, Object> claims = new HashMap<>();
        return createToken(claims, userDetails.getUsername());
    }

    private String createToken(Map<String, Object> claims, String subject) {
        return Jwts.builder()
            .setClaims(claims)
            .setSubject(subject)
            .setIssuedAt(new Date(System.currentTimeMillis()))
            .setExpiration(new Date(System.currentTimeMillis() + jwtExpirationInMs))
            .signWith(SignatureAlgorithm.HS512, secret)
            .compact();
    }

    public Boolean validateToken(String token, UserDetails userDetails) {
        final String username = extractUsername(token);
        return (username.equals(userDetails.getUsername()) && !isTokenExpired(token));
    }
}
```

### 3. Validation

#### Entity Validation

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @NotBlank(message = "Name is required")
    @Size(min = 2, max = 50, message = "Name must be between 2 and 50 characters")
    private String name;

    @NotBlank(message = "Email is required")
    @Email(message = "Email should be valid")
    private String email;

    @Min(value = 18, message = "Age must be at least 18")
    @Max(value = 100, message = "Age must not exceed 100")
    private Integer age;
}
```

#### Controller Validation

```java
@RestController
public class UserController {

    @PostMapping("/users")
    public ResponseEntity<User> createUser(@Valid @RequestBody User user,
                                        BindingResult bindingResult) {
        if (bindingResult.hasErrors()) {
            throw new ValidationException("Validation failed");
        }
        return ResponseEntity.ok(userService.save(user));
    }
}
```

### 4. Exception Handling

#### Global Exception Handler

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFound(UserNotFoundException ex) {
        ErrorResponse error = new ErrorResponse("USER_NOT_FOUND", ex.getMessage());
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationExceptions(
            MethodArgumentNotValidException ex) {
        ErrorResponse error = new ErrorResponse("VALIDATION_ERROR",
            ex.getBindingResult().getFieldError().getDefaultMessage());
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(error);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGenericException(Exception ex) {
        ErrorResponse error = new ErrorResponse("INTERNAL_ERROR", "An unexpected error occurred");
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
    }
}
```

### 5. Configuration Properties

#### Application Properties

```properties
# application.properties
server.port=8080
spring.application.name=my-spring-app

# Database Configuration
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# JPA Configuration
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

# Logging
logging.level.com.example.myapp=DEBUG
logging.level.org.springframework.web=DEBUG
```

#### Configuration Classes

```java
@ConfigurationProperties(prefix = "app")
@Component
public class AppProperties {
    private String name;
    private String version;
    private Database database = new Database();

    // Getters and Setters
    public static class Database {
        private String host;
        private int port;
        private String name;

        // Getters and Setters
    }
}
```

---

## Advanced Concepts

### 1. Microservices Architecture

#### Service Discovery with Eureka

```java
// Eureka Server
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }
}

// Eureka Client
@SpringBootApplication
@EnableEurekaClient
public class UserServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(UserServiceApplication.class, args);
    }
}
```

#### API Gateway

```java
@SpringBootApplication
@EnableZuulProxy
public class ApiGatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(ApiGatewayApplication.class, args);
    }
}
```

### 2. Caching

#### Redis Cache Configuration

```java
@Configuration
@EnableCaching
public class CacheConfig {

    @Bean
    public RedisConnectionFactory redisConnectionFactory() {
        return new LettuceConnectionFactory();
    }

    @Bean
    public RedisTemplate<String, Object> redisTemplate() {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory());
        template.setDefaultSerializer(new GenericJackson2JsonRedisSerializer());
        return template;
    }
}
```

#### Cache Usage

```java
@Service
public class UserService {

    @Cacheable(value = "users", key = "#id")
    public User findById(Long id) {
        return userRepository.findById(id).orElse(null);
    }

    @CacheEvict(value = "users", key = "#user.id")
    public User updateUser(User user) {
        return userRepository.save(user);
    }

    @CacheEvict(value = "users", allEntries = true)
    public void clearCache() {
        // Cache will be cleared
    }
}
```

### 3. Async Processing

#### Async Configuration

```java
@Configuration
@EnableAsync
public class AsyncConfig {

    @Bean(name = "taskExecutor")
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(2);
        executor.setMaxPoolSize(2);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("Async-");
        executor.initialize();
        return executor;
    }
}
```

#### Async Service

```java
@Service
public class EmailService {

    @Async("taskExecutor")
    public CompletableFuture<String> sendEmail(String to, String subject, String body) {
        // Simulate email sending
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        return CompletableFuture.completedFuture("Email sent to " + to);
    }
}
```

### 4. Monitoring and Metrics

#### Actuator Configuration

```properties
# application.properties
management.endpoints.web.exposure.include=health,info,metrics,prometheus
management.endpoint.health.show-details=always
management.metrics.export.prometheus.enabled=true
```

#### Custom Metrics

```java
@Component
public class CustomMetrics {

    private final Counter userCreatedCounter;
    private final Timer userProcessingTimer;

    public CustomMetrics(MeterRegistry meterRegistry) {
        this.userCreatedCounter = Counter.builder("users.created")
            .description("Number of users created")
            .register(meterRegistry);

        this.userProcessingTimer = Timer.builder("users.processing.time")
            .description("Time taken to process user")
            .register(meterRegistry);
    }

    public void incrementUserCreated() {
        userCreatedCounter.increment();
    }

    public void recordUserProcessingTime(Duration duration) {
        userProcessingTimer.record(duration);
    }
}
```

### 5. Event-Driven Architecture

#### Event Publishing

```java
@Service
public class UserService {

    @Autowired
    private ApplicationEventPublisher eventPublisher;

    public User createUser(User user) {
        User savedUser = userRepository.save(user);

        // Publish event
        UserCreatedEvent event = new UserCreatedEvent(savedUser);
        eventPublisher.publishEvent(event);

        return savedUser;
    }
}

@Component
public class UserCreatedEvent extends ApplicationEvent {
    private final User user;

    public UserCreatedEvent(User user) {
        super(user);
        this.user = user;
    }

    public User getUser() {
        return user;
    }
}
```

#### Event Listening

```java
@Component
public class UserEventListener {

    @EventListener
    @Async
    public void handleUserCreated(UserCreatedEvent event) {
        User user = event.getUser();
        // Send welcome email, create user profile, etc.
        System.out.println("User created: " + user.getName());
    }
}
```

---

## Configuration

### 1. Profiles

#### Profile-specific Properties

```properties
# application-dev.properties
spring.datasource.url=jdbc:h2:mem:devdb
spring.jpa.hibernate.ddl-auto=create-drop
logging.level.com.example.myapp=DEBUG

# application-prod.properties
spring.datasource.url=jdbc:mysql://localhost:3306/proddb
spring.jpa.hibernate.ddl-auto=validate
logging.level.com.example.myapp=WARN
```

#### Profile Usage

```java
@Configuration
@Profile("dev")
public class DevConfig {
    @Bean
    public DataSource dataSource() {
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.H2)
            .build();
    }
}

@Configuration
@Profile("prod")
public class ProdConfig {
    @Bean
    public DataSource dataSource() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:mysql://localhost:3306/proddb");
        return new HikariDataSource(config);
    }
}
```

### 2. Environment Variables

```bash
# Set environment variables
export SPRING_PROFILES_ACTIVE=prod
export DATABASE_URL=jdbc:mysql://localhost:3306/mydb
export DATABASE_USERNAME=myuser
export DATABASE_PASSWORD=mypassword
```

### 3. External Configuration

```yaml
# application.yml
spring:
  profiles:
    active: dev
  datasource:
    url: ${DATABASE_URL:jdbc:h2:mem:testdb}
    username: ${DATABASE_USERNAME:sa}
    password: ${DATABASE_PASSWORD:}
  jpa:
    hibernate:
      ddl-auto: ${DDL_AUTO:create-drop}
    show-sql: ${SHOW_SQL:true}

server:
  port: ${PORT:8080}

logging:
  level:
    com.example.myapp: ${LOG_LEVEL:INFO}
```

---

## Testing

### 1. Unit Testing

#### Service Testing

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    @Test
    void shouldReturnUserWhenValidId() {
        // Given
        Long userId = 1L;
        User user = new User("John Doe", "john@example.com");
        when(userRepository.findById(userId)).thenReturn(Optional.of(user));

        // When
        User result = userService.findById(userId);

        // Then
        assertThat(result).isNotNull();
        assertThat(result.getName()).isEqualTo("John Doe");
        verify(userRepository).findById(userId);
    }

    @Test
    void shouldThrowExceptionWhenUserNotFound() {
        // Given
        Long userId = 1L;
        when(userRepository.findById(userId)).thenReturn(Optional.empty());

        // When & Then
        assertThatThrownBy(() -> userService.findById(userId))
            .isInstanceOf(UserNotFoundException.class)
            .hasMessage("User not found with id: " + userId);
    }
}
```

### 2. Integration Testing

#### Repository Testing

```java
@DataJpaTest
class UserRepositoryTest {

    @Autowired
    private TestEntityManager entityManager;

    @Autowired
    private UserRepository userRepository;

    @Test
    void shouldFindUserByEmail() {
        // Given
        User user = new User("John Doe", "john@example.com");
        entityManager.persistAndFlush(user);

        // When
        Optional<User> found = userRepository.findByEmail("john@example.com");

        // Then
        assertThat(found).isPresent();
        assertThat(found.get().getName()).isEqualTo("John Doe");
    }
}
```

#### Web Layer Testing

```java
@WebMvcTest(UserController.class)
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private UserService userService;

    @Test
    void shouldReturnAllUsers() throws Exception {
        // Given
        List<User> users = Arrays.asList(
            new User("John Doe", "john@example.com"),
            new User("Jane Smith", "jane@example.com")
        );
        when(userService.findAll()).thenReturn(users);

        // When & Then
        mockMvc.perform(get("/api/users"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$", hasSize(2)))
            .andExpect(jsonPath("$[0].name", is("John Doe")))
            .andExpect(jsonPath("$[1].name", is("Jane Smith")));
    }
}
```

### 3. Test Slices

```java
@SpringBootTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@TestPropertySource(locations = "classpath:application-test.properties")
class UserServiceIntegrationTest {

    @Autowired
    private UserService userService;

    @Autowired
    private UserRepository userRepository;

    @Test
    @Transactional
    void shouldCreateAndRetrieveUser() {
        // Given
        User user = new User("John Doe", "john@example.com");

        // When
        User savedUser = userService.save(user);
        User retrievedUser = userService.findById(savedUser.getId());

        // Then
        assertThat(retrievedUser).isNotNull();
        assertThat(retrievedUser.getName()).isEqualTo("John Doe");
    }
}
```

---

## Deployment

### 1. Docker Configuration

#### Dockerfile

```dockerfile
FROM openjdk:17-jdk-slim

WORKDIR /app

COPY target/my-spring-app-*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

#### Docker Compose

```yaml
version: "3.8"
services:
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=prod
      - DATABASE_URL=jdbc:mysql://db:3306/myapp
    depends_on:
      - db
      - redis

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: myapp
    volumes:
      - db_data:/var/lib/mysql

  redis:
    image: redis:alpine
    ports:
      - "6379:6379"

volumes:
  db_data:
```

### 2. Kubernetes Deployment

#### Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-spring-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-spring-app
  template:
    metadata:
      labels:
        app: my-spring-app
    spec:
      containers:
        - name: my-spring-app
          image: my-spring-app:latest
          ports:
            - containerPort: 8080
          env:
            - name: SPRING_PROFILES_ACTIVE
              value: "prod"
            - name: DATABASE_URL
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: url
---
apiVersion: v1
kind: Service
metadata:
  name: my-spring-app-service
spec:
  selector:
    app: my-spring-app
  ports:
    - port: 80
      targetPort: 8080
  type: LoadBalancer
```

### 3. Build Scripts

#### Maven Build Script

```bash
#!/bin/bash
# build.sh

echo "Building Spring Boot application..."

# Clean and compile
mvn clean compile

# Run tests
mvn test

# Package application
mvn package -DskipTests

# Build Docker image
docker build -t my-spring-app:latest .

echo "Build completed successfully!"
```

---

## Troubleshooting

### 1. Common Issues

#### Port Already in Use

```bash
# Find process using port 8080
netstat -ano | findstr :8080

# Kill process (Windows)
taskkill /PID <PID> /F

# Kill process (Linux/Mac)
kill -9 <PID>
```

#### Database Connection Issues

```properties
# Check database configuration
spring.datasource.url=jdbc:mysql://localhost:3306/mydb?useSSL=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
```

#### Memory Issues

```bash
# Increase heap size
java -Xms512m -Xmx1024m -jar myapp.jar

# Or set in application.properties
spring.jpa.properties.hibernate.jdbc.batch_size=20
spring.jpa.properties.hibernate.order_inserts=true
spring.jpa.properties.hibernate.order_updates=true
```

### 2. Debugging

#### Enable Debug Logging

```properties
# application.properties
logging.level.org.springframework.web=DEBUG
logging.level.org.springframework.security=DEBUG
logging.level.org.hibernate.SQL=DEBUG
logging.level.org.hibernate.type.descriptor.sql.BasicBinder=TRACE
```

#### Health Checks

```java
@Component
public class CustomHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {
        // Check external dependencies
        boolean isDatabaseUp = checkDatabase();
        boolean isExternalServiceUp = checkExternalService();

        if (isDatabaseUp && isExternalServiceUp) {
            return Health.up()
                .withDetail("database", "Available")
                .withDetail("external-service", "Available")
                .build();
        } else {
            return Health.down()
                .withDetail("database", isDatabaseUp ? "Available" : "Unavailable")
                .withDetail("external-service", isExternalServiceUp ? "Available" : "Unavailable")
                .build();
        }
    }
}
```

### 3. Performance Optimization

#### Connection Pool Configuration

```properties
# HikariCP Configuration
spring.datasource.hikari.maximum-pool-size=20
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.idle-timeout=300000
spring.datasource.hikari.max-lifetime=1200000
spring.datasource.hikari.connection-timeout=20000
```

#### JPA Optimization

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "name", length = 100)
    private String name;

    @Column(name = "email", length = 255, unique = true)
    private String email;

    @OneToMany(mappedBy = "user", fetch = FetchType.LAZY)
    private List<Order> orders;

    @CreationTimestamp
    @Column(name = "created_at")
    private LocalDateTime createdAt;

    @UpdateTimestamp
    @Column(name = "updated_at")
    private LocalDateTime updatedAt;
}
```

---

## Quick Reference

### Essential Dependencies

```xml
<!-- Web -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- Data JPA -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- Security -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>

<!-- Validation -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>

<!-- Test -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

### Common Commands

```bash
# Run application
mvn spring-boot:run

# Build JAR
mvn clean package

# Run tests
mvn test

# Generate project
curl https://start.spring.io/starter.zip -d dependencies=web,data-jpa,h2 -o project.zip
```

### Key Properties

```properties
# Server
server.port=8080
server.servlet.context-path=/api

# Database
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=

# JPA
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true

# Logging
logging.level.com.example=DEBUG
```

---

## Best Practices

1. **Use @RestController for REST APIs**
2. **Implement proper exception handling**
3. **Use DTOs for data transfer**
4. **Implement validation on input data**
5. **Use appropriate HTTP status codes**
6. **Implement proper logging**
7. **Use profiles for different environments**
8. **Write comprehensive tests**
9. **Use connection pooling**
10. **Implement caching where appropriate**
11. **Use async processing for long-running tasks**
12. **Monitor application health**
13. **Use proper security measures**
14. **Optimize database queries**
15. **Use proper error messages**

---

_This cheat sheet covers Spring Boot from beginner to advanced concepts. Keep it handy for quick reference during development!_
