# Spring Framework Basics

## Spring Configuration Methods

Spring provides multiple ways to configure applications:

1. **XML Configuration:**
    - Uses XML files to define beans and their dependencies.
    - Example: `<beans>`, `<bean>`, `<property>` tags.

2. **Java-based Configuration:**
    - Uses Java classes annotated with `@Configuration` and `@Bean`.

3. **Annotation-based Configuration:**
    - Uses annotations directly on classes and methods.
    - Common annotations:
        - `@Component`: Marks a class as a Spring-managed component.
        - `@Autowired`: Injects dependencies automatically.
        - `@ComponentScan`: Scans for components in specified packages.

Each method can be used independently or combined based on project requirements.

## Spring Bean Lifecycle

The lifecycle of a Spring bean is managed by the Spring container. The main stages are:

1. **Instantiation:** The container creates an instance of the bean.
2. **Populate Properties:** Dependencies are injected into the bean.
3. **Set Bean Name:** If the bean implements `BeanNameAware`, `setBeanName()` is called.
4. **Set Bean Factory:** If the bean implements `BeanFactoryAware`, `setBeanFactory()` is called.
5. **Pre-Initialization:** `BeanPostProcessors` are applied before initialization.
6. **Initialization:** The container calls `afterPropertiesSet()` (if `InitializingBean` is implemented) or a custom init method.
7. **Post-Initialization:** `BeanPostProcessors` are applied after initialization.
8. **Ready for Use:** The bean is now available for use.
9. **Destruction:** The container calls `destroy()` (if `DisposableBean` is implemented) or a custom destroy method.

## Load the Spring Context

Spring context can be loaded in different ways:

???+ info "Spring Context"

    === "XML Configuration"

        ```java
        import org.springframework.context.ApplicationContext;
        import org.springframework.context.support.ClassPathXmlApplicationContext;

        ApplicationContext context = new ClassPathXmlApplicationContext("config_file_name.xml");
        ```

    === "Java Configuration"

        ```java
        import org.springframework.context.ApplicationContext;
        import org.springframework.context.annotation.AnnotationConfigApplicationContext;

        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        ```

    === "Annotation Configuration"

        ```java
        import org.springframework.context.ApplicationContext;
        import org.springframework.context.annotation.AnnotationConfigApplicationContext;

        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        ```

        - Use `@ComponentScan` to specify the package to scan for components.
        - Use `@EnableAspectJAutoProxy` to enable AspectJ support.
        - Use `@EnableTransactionManagement` to enable transaction management.
        - Use `@PropertySource` to load properties files.
        - Use `@Import` to import other configuration classes.
        - Use `@ImportResource` to import XML configuration files.

## Scope of Beans

Spring supports different bean scopes:

1. **Singleton:** Default scope. A single instance is created and shared across the application.
2. **Prototype:** A new instance is created each time the bean is requested.
3. **Request:** A new instance is created for each HTTP request (web applications).
4. **Session:** A new instance is created for each HTTP session (web applications).
5. **Global Session:** A new instance is created for each global HTTP session (web applications).
6. **Application:** A single instance is created for the lifecycle of the application context.
7. **WebSocket:** A new instance is created for each WebSocket session (web applications).

## Setter Injection

Setter injection is a way to inject dependencies into a Spring bean using setter methods. It allows for more flexibility and can be used to change the dependencies at runtime.

???+ example

    === "Class Definition"

        === "Processor.java"

            ```java
            public class Processor {
                private String brand;
                private String model;

                public void setModel(String model) {
                    this.model = model;
                }

                public void setBrand(String brand) {
                    this.brand = brand;
                }

                public String getDetails() {
                    return brand + " " + model;
                }
            }
            ```

        === "Memory.java"

            ```java
            public class Memory {
                private String type;
                private int size;

                public void setType(String type) {
                    this.type = type;
                }

                public void setSize(int size) {
                    this.size = size;
                }

                public String getDetails() {
                    return type + " " + size + "GB";
                }
            }
            ```

        === "Computer.java"

            ```java
            public class Computer {
                private Processor processor;
                private Memory memory;

                public void setProcessor(Processor processor) {
                    this.processor = processor;
                }

                public void setMemory(Memory memory) {
                    this.memory = memory;
                }

                public void displaySpecs() {
                    System.out.println("Computer Specs:");
                    System.out.println("Processor: " + processor.getDetails());
                    System.out.println("Memory: " + memory.getDetails());
                }
            }
            ```

    === "XML Configuration"

        ```xml
        <bean id="processor" class="fullpackage.Processor">
            <property name="brand" value="Intel"/>
            <property name="model" value="Core i7"/>
        </bean>
        <bean id="memory" class="fullpackage.Memory">
            <property name="type" value="DDR4"/>
            <property name="size" value="16"/>
        </bean>
        <bean id="computer" class="fullpackage.Computer">
            <property name="processor" ref="processor"/>
            <property name="memory" ref="memory"/>
        </bean>
        ```

    === "Getting Bean"

        ```java
        Computer computer = context.getBean("computer", Computer.class);
        computer.displaySpecs(); // (1)!
        ```

        1.  
            ```plaintext title="Output"
            Computer Specs:
            Processor: Intel Core i7
            Memory: DDR4 16GB
            ```

## Constructor Injection

Constructor injection is a way to inject dependencies into a Spring bean using constructor parameters. It is useful when dependencies are mandatory for the bean to function and cannot be changed at runtime.

???+ example

    === "Class Definition"

        === "Processor.java"

            ```java
            public class Processor {
                private String brand;
                private String model;

                public Processor(String brand, String model) {
                    this.brand = brand;
                    this.model = model;
                }

                public String getDetails() {
                    return brand + " " + model;
                }
            }
            ```

        === "Memory.java"

            ```java
            public class Memory {
                private String type;
                private int size;

                public Memory(String type, int size) {
                    this.type = type;
                    this.size = size;
                }

                public String getDetails() {
                    return type + " " + size + "GB";
                }
            }
            ```

        === "Computer.java"

            ```java
            public class Computer {
                private Processor processor;
                private Memory memory;

                public Computer(Processor processor, Memory memory) {
                    this.processor = processor;
                    this.memory = memory;
                }

                public void displaySpecs() {
                    System.out.println("Computer Specs:");
                    System.out.println("Processor: " + processor.getDetails());
                    System.out.println("Memory: " + memory.getDetails());
                }
            }
            ```

    === "XML Configuration"

        - For constructor injection, use `<constructor-arg>` tags to specify the constructor arguments.
        - The order of arguments must match the constructor's parameter list unless you use attributes  like:
            - `index`: Specifies the position of the constructor argument explicitly.
            - `type`: Specifies the data type of the constructor argument (useful when arguments have   **different types** and the sequence is not maintained).
            - `name`: Specifies the name of the constructor argument. This requires the     `@ConstructorProperties` annotation in the constructor to map argument names.

        ```xml
        <bean id="processor" class="fullpackage.Processor">
            <constructor-arg value="Intel"/>
            <constructor-arg value="Core i7"/>
        </bean>
        <bean id="memory" class="fullpackage.Memory">
            <constructor-arg value="DDR4"/>
            <constructor-arg value="16"/>
        </bean>
        <bean id="computer" class="fullpackage.Computer">
            <constructor-arg ref="processor"/>
            <constructor-arg ref="memory"/>
        </bean>
        ```

    === "Getting Bean"

        ```java
        Computer computer = context.getBean("computer", Computer.class);
        computer.displaySpecs(); // (1)!
        ```

        1.  
            ```plaintext title="Output"
            Computer Specs:
            Processor: Intel Core i7
            Memory: DDR4 16GB
            ```

## Autowiring

Autowiring is a feature in Spring that allows the container to automatically resolve and inject dependencies into a bean. It reduces the need for explicit configuration.

### Autowiring Modes

1. **no (default):** No autowiring. Dependencies must be explicitly defined.
2. **byName:** Autowires by matching the property name with a bean name.
3. **byType:** Autowires by matching the property type with a bean type.
4. **constructor:** Autowires by matching constructor parameters with bean types.

???+ example

    === "Class Definition"

        === "Processor.java"

            ```java
            public class Processor {
                private String brand;
                private String model;

                public void setBrand(String brand) {
                    this.brand = brand;
                }

                public void setModel(String model) {
                    this.model = model;
                }

                public String getDetails() {
                    return brand + " " + model;
                }
            }
            ```

        === "Computer.java"

            ```java
            public class Computer {
                private Processor processor;

                public Processor getProcessor() {
                    return processor;
                }

                public void setProcessor(Processor processor) {
                    this.processor = processor;
                }

                public void displaySpecs() {
                    System.out.println("Processor: " + processor.getDetails());
                }
            }
            ```

    === "XML Configuration"

        ```xml
        <bean id="processor" class="fullpackage.Processor">
            <property name="brand" value="Intel"/>
            <property name="model" value="Core i7"/>
        </bean>

        <bean id="memory" class="fullpackage.Memory">
            <property name="type" value="DDR4"/>
            <property name="size" value="16"/>
        </bean>

        <bean id="computer" class="fullpackage.Computer" autowire="byName"/>
        ```

    === "Getting Bean"

        ```java
        Computer computer = context.getBean("computer", Computer.class);
        computer.displaySpecs(); // (1)!
        ```

        1.  
            ```plaintext title="Output"
            Processor: Intel Core i7
            ```

### Autowiring with Annotations

Spring also supports autowiring using annotations like `@Autowired`.

???+ example

    === "Class Definition"

        === "Processor.java"

            ```java
            import org.springframework.stereotype.Component;

            @Component
            public class Processor {
                private String brand = "Intel";
                private String model = "Core i7";

                public String getDetails() {
                    return brand + " " + model;
                }
            }
            ```

        === "Computer.java"

            ```java
            import org.springframework.beans.factory.annotation.Autowired;
            import org.springframework.stereotype.Component;

            @Component
            public class Computer {
                @Autowired
                private Processor processor;

                public void displaySpecs() {
                    System.out.println("Processor: " + processor.getDetails());
                }
            }
            ```

    === "Java Configuration"

        ```java
        import org.springframework.context.ApplicationContext;
        import org.springframework.context.annotation.AnnotationConfigApplicationContext;
        import org.springframework.context.annotation.ComponentScan;
        import org.springframework.context.annotation.Configuration;

        @Configuration
        @ComponentScan(basePackages = "fullpackage")
        public class AppConfig {
        }

        public class Main {
            public static void main(String[] args) {
                ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
                Computer computer = context.getBean(Computer.class);
                computer.displaySpecs(); // (1)!
            }
        }
        ```

        1.  
            ```plaintext title="Output"
            Processor: Intel Core i7
            ```

## Using `Primary` to Resolve Bean Ambiguity

When multiple beans of the same type are eligible for injection, the `@Primary` annotation or the `primary` attribute in XML can be used to mark one bean as the default choice.

???+ example

    === "Class Definition"

        === "Processor.java"

            ```java
            public class Processor {
                private String brand;
                private String model;

                public Processor(String brand, String model) {
                    this.brand = brand;
                    this.model = model;
                }

                public String getDetails() {
                    return brand + " " + model;
                }
            }
            ```

        === "Computer.java"

            ```java
            import org.springframework.beans.factory.annotation.Autowired;
            import org.springframework.stereotype.Component;

            @Component
            public class Computer {
                @Autowired
                private Processor processor;

                public void displaySpecs() {
                    System.out.println("Processor: " + processor.getDetails());
                }
            }
            ```

    === "XML Configuration"

        ```xml
        <bean id="intelProcessor" class="fullpackage.Processor">
            <constructor-arg value="Intel"/>
            <constructor-arg value="Core i7"/>
        </bean>

        <bean id="amdProcessor" class="fullpackage.Processor" primary="true">
            <constructor-arg value="AMD"/>
            <constructor-arg value="Ryzen 7"/>
        </bean>
        ```

    === "Java Configuration"

        ```java
        @Configuration
        public class AppConfig {
            @Bean
            public Processor intelProcessor() {
                return new Processor("Intel", "Core i7");
            }

            @Bean
            @Primary
            public Processor amdProcessor() {
                return new Processor("AMD", "Ryzen 7");
            }
        }
        ```

## Inner Beans

Inner beans are beans defined within the `<property>` or `<constructor-arg>` tags of another bean. They are used when a bean is only relevant as a dependency of another bean and does not need to be reused elsewhere.

???+ example

    === "Class Definition"

        === "Processor.java"

            ```java
            public class Processor {
                private String brand;
                private String model;

                public Processor(String brand, String model) {
                    this.brand = brand;
                    this.model = model;
                }

                public String getDetails() {
                    return brand + " " + model;
                }
            }
            ```

        === "Memory.java"

            ```java
            public class Memory {
                private String type;
                private int size;

                public Memory(String type, int size) {
                    this.type = type;
                    this.size = size;
                }

                public String getDetails() {
                    return type + " " + size + "GB";
                }
            }
            ```

        === "Computer.java"

            ```java
            public class Computer {
                private Processor processor;
                private Memory memory;

                public Computer(Processor processor, Memory memory) {
                    this.processor = processor;
                    this.memory = memory;
                }

                public void displaySpecs() {
                    System.out.println("Computer Specs:");
                    System.out.println("Processor: " + processor.getDetails());
                    System.out.println("Memory: " + memory.getDetails());
                }
            }
            ```

    === "XML Configuration"

        ```xml
        <bean id="computer" class="fullpackage.Computer">
            <constructor-arg>
                <bean class="fullpackage.Processor">
                    <constructor-arg value="Intel"/>
                    <constructor-arg value="Core i7"/>
                </bean>
            </constructor-arg>
            <constructor-arg>
                <bean class="fullpackage.Memory">
                    <constructor-arg value="DDR4"/>
                    <constructor-arg value="16"/>
                </bean>
            </constructor-arg>
        </bean>
        ```

    === "Getting Bean"

        ```java
        Computer computer = context.getBean("computer", Computer.class);
        computer.displaySpecs(); // (1)!
        ```

        1.  
            ```plaintext title="Output"
            Computer Specs:
            Processor: Intel Core i7
            Memory: DDR4 16GB
            ```
