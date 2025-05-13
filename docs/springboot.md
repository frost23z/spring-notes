# Spring Boot Basics

## Introduction to Spring Boot

Spring Boot simplifies the development of Spring-based applications by providing pre-configured setups and reducing boilerplate code. It is built on top of the Spring Framework.

## Key Features of Spring Boot

1. **Auto-Configuration:** Automatically configures Spring beans based on the classpath and application properties.
2. **Standalone Applications:** Allows running applications without requiring an external application server.
3. **Embedded Servers:** Comes with embedded servers like Tomcat, Jetty, or Undertow.
4. **Production-Ready Features:** Includes metrics, health checks, and externalized configuration.
5. **Spring Boot Starter Dependencies:** Simplifies dependency management by providing starter POMs.
6. **Spring Boot CLI:** Allows running Groovy scripts for rapid prototyping.

## Spring Boot Application

???+ example

    ```java title="Application.java"
    package com.example;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    public class Application {
        public static void main(String[] args) {
            ApplicationContext context = SpringApplication.run(Application.class, args);
            Computer computer = context.getBean("computer", Computer.class);
            computer.displaySpecs();
        }
    }
    ```

## Dependency Injection in Spring Boot

Spring Boot supports dependency injection using annotations like `@Autowired`, `@Qualifier`, and `@Primary`.

???+ example

    === "Field Injection"

        ```java
        @Component
        public class Computer {
            @Autowired
            private Processor processor;

            @Autowired
            private Memory memory;

            public void displaySpecs() {
                System.out.println("Processor: " + processor.getDetails());
                System.out.println("Memory: " + memory.getDetails());
            }
        }
        ```

    === "Constructor Injection"

        ```java
        @Component
        public class Computer {
            private final Processor processor;
            private final Memory memory;

            @Autowired
            public Computer(Processor processor, Memory memory) {
                this.processor = processor;
                this.memory = memory;
            }

            public void displaySpecs() {
                System.out.println("Processor: " + processor.getDetails());
                System.out.println("Memory: " + memory.getDetails());
            }
        }
        ```

    === "Setter Injection"

        ```java
        @Component
        public class Computer {
            private Processor processor;
            private Memory memory;

            @Autowired
            public void setProcessor(Processor processor) {
                this.processor = processor;
            }

            @Autowired
            public void setMemory(Memory memory) {
                this.memory = memory;
            }

            public void displaySpecs() {
                System.out.println("Processor: " + processor.getDetails());
                System.out.println("Memory: " + memory.getDetails());
            }
        }
        ```

## Configuration Properties

Spring Boot allows binding external configuration to Java objects using `@ConfigurationProperties`.

???+ example

    === "Configuration Class"

        ```java
        @Component
        @ConfigurationProperties(prefix = "app")
        public class AppConfig {
            private String name;
            private String version;

            // Getters and setters
            public String getName() {
                return name;
            }

            public void setName(String name) {
                this.name = name;
            }

            public String getVersion() {
                return version;
            }

            public void setVersion(String version) {
                this.version = version;
            }
        }
        ```

    === "application.properties"

        ```properties
        app.name=SpringBootApp
        app.version=1.0.0
        ```

    === "Usage"

        ```java
        @RestController
        public class AppController {
            @Autowired
            private AppConfig appConfig;

            @GetMapping("/info")
            public String getAppInfo() {
                return "App: " + appConfig.getName() + ", Version: " + appConfig.getVersion();
            }
        }
        ```

## Profiles in Spring Boot

Spring Boot supports profiles to define environment-specific configurations.

???+ example

    === "application-dev.properties"

        ```properties
        server.port=8081
        ```

    === "application-prod.properties"

        ```properties
        server.port=8082
        ```

    === "Activating Profiles"

        ```bash
        java -Dspring.profiles.active=dev -jar app.jar
        ```

## Validation

Spring Boot supports validation using `@Valid` and `@Validated`.

???+ example

    === "DTO Class"

        ```java
        public class UserDTO {
            @NotNull
            @Size(min = 3, max = 50)
            private String name;

            @Email
            private String email;

            // Getters and setters
        }
        ```

    === "Controller"

        ```java
        @RestController
        public class UserController {
            @PostMapping("/users")
            public ResponseEntity<String> createUser(@Valid @RequestBody UserDTO user) {
                return ResponseEntity.ok("User created: " + user.getName());
            }
        }
        ```

## Event Handling

Spring Boot supports event handling using `ApplicationEvent` and `@EventListener`.

???+ example

    === "Custom Event"

        ```java
        public class CustomEvent extends ApplicationEvent {
            private String message;

            public CustomEvent(Object source, String message) {
                super(source);
                this.message = message;
            }

            public String getMessage() {
                return message;
            }
        }
        ```

    === "Event Publisher"

        ```java
        @Component
        public class EventPublisher {
            @Autowired
            private ApplicationEventPublisher publisher;

            public void publishEvent(String message) {
                publisher.publishEvent(new CustomEvent(this, message));
            }
        }
        ```

    === "Event Listener"

        ```java
        @Component
        public class EventListener {
            @EventListener
            public void handleCustomEvent(CustomEvent event) {
                System.out.println("Received event: " + event.getMessage());
            }
        }
        ```

## Logging

Spring Boot uses SLF4J with Logback as the default logging framework.

???+ example

    === "application.properties"

        ```properties
        logging.level.org.springframework=DEBUG
        logging.file.name=app.log
        ```

    === "Usage"

        ```java
        @RestController
        public class LoggingController {
            private static final Logger logger = LoggerFactory.getLogger(LoggingController.class);

            @GetMapping("/log")
            public String logMessage() {
                logger.info("This is an info message");
                return "Check the logs!";
            }
        }
        ```
