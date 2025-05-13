# Spring Framework Basics

## Spring Bean Lifecycle

The lifecycle of a Spring bean is managed by the Spring container. The main stages are:

???+ info "Spring Bean Lifecycle"

    1. **Instantiation:** The container creates an instance of the bean.
    2. **Populate Properties:** Dependencies are injected into the bean.
    3. **Set Bean Name:** If the bean implements `BeanNameAware`, `setBeanName()` is called.
    4. **Set Bean Factory:** If the bean implements `BeanFactoryAware`, `setBeanFactory()` is called.
    5. **Pre-Initialization:** `BeanPostProcessors` are applied before initialization.
    6. **Initialization:** The container calls `afterPropertiesSet()` (if `InitializingBean` is implemented) or a custom init method.
    7. **Post-Initialization:** `BeanPostProcessors` are applied after initialization.
    8. **Ready for Use:** The bean is now available for use.
    9. **Destruction:** The container calls `destroy()` (if `DisposableBean` is implemented) or a custom destroy method.

## Base Classes for Examples

The following base classes are used across all examples in this document. Refer to these classes for context when reviewing the examples.

???+ example

    === "Processor.java"

        ```java
        package com.example;

        public class Processor {
            private String brand;
            private String model;

            public Processor() {}

            public Processor(String brand, String model) {
                this.brand = brand;
                this.model = model;
            }

            public String getBrand() {
                return brand;
            }

            public void setBrand(String brand) {
                this.brand = brand;
            }

            public String getModel() {
                return model;
            }

            public void setModel(String model) {
                this.model = model;
            }

            public String getDetails() {
                return brand + " " + model;
            }
        }
        ```

    === "Memory.java"

        ```java
        package com.example;

        public class Memory {
            private String type;
            private int size;

            public Memory() {}

            public Memory(String type, int size) {
                this.type = type;
                this.size = size;
            }

            public String getType() {
                return type;
            }

            public void setType(String type) {
                this.type = type;
            }

            public int getSize() {
                return size;
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
        package com.example;

        public class Computer {
            private Processor processor;
            private Memory memory;

            public Computer() {}

            public Computer(Processor processor, Memory memory) {
                this.processor = processor;
                this.memory = memory;
            }

            public Processor getProcessor() {
                return processor;
            }

            public void setProcessor(Processor processor) {
                this.processor = processor;
            }

            public Memory getMemory() {
                return memory;
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

## Spring Configuration Methods

Spring provides multiple ways to configure applications:

???+ info "Spring Configuration"

    === "XML Configuration"

        - XML configuration is the traditional way of configuring Spring applications.
        - It uses an XML file to define beans and their dependencies.
        - XML files are usually placed in the `src/main/resources` directory.
        - The XML file is loaded into the Spring context using `ClassPathXmlApplicationContext`.

        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans/spring-beans.xsd">
            <!-- Define beans here -->
        </beans>
        ```

    === "Java-based Configuration"

        - Java-based configuration uses Java classes to define beans and their dependencies.
        - It is more type-safe and allows for better refactoring.
        - Java configuration classes are annotated with `@Configuration` and use `@Bean` to define beans.

        ```java
        package com.example;

        @Configuration
        public class AppConfig {
            // Define beans here
        }
        ```

    === "Annotation-based Configuration"

        - Annotation-based configuration uses annotations to define beans and their dependencies.
        - It is more concise and easier to read compared to XML configuration.
        - Common annotations include `@Component`, `@Autowired`, and `@Configuration`.

        ```java
        package com.example;

        @Configuration
        @ComponentScan(basePackages = "com.example")
        public class AppConfig {
            // No need to define beans explicitly, Spring will scan for @Component annotations
        }
        ```

## Load the Spring Context

Spring context can be loaded in different ways:

???+ info "Spring Context"

    === "XML Configuration"

        ```java
        package com.example;

        ApplicationContext context = new ClassPathXmlApplicationContext("config_file_name.xml");
        ```

    === "Java-based Configuration"

        ```java
        package com.example;

        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        ```

    === "Annotation-based Configuration"

        ```java
        package com.example;

        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        ```

    === "Getting Bean"

        ```java
        package com.example;

        Computer computer = context.getBean("computer", Computer.class);
        computer.displaySpecs(); // (1)!
        ```

        1.  
            ```plaintext title="Output"
            Computer Specs:
            Processor: Intel Core i7
            Memory: DDR4 16GB
            ```

## Scope of Beans

Spring supports different bean scopes:

1. **Singleton:** Default scope. A single instance is created and shared across the application.
2. **Prototype:** A new instance is created each time the bean is requested.
3. **Request:** A new instance is created for each HTTP request (web applications).
4. **Session:** A new instance is created for each HTTP session (web applications).
5. **Global Session:** A new instance is created for each global HTTP session (web applications).
6. **Application:** A single instance is created for the lifecycle of the application context.
7. **WebSocket:** A new instance is created for each WebSocket session (web applications).

???+ example "Spring Bean Scopes"

    === "XML Configuration"

        ```xml
        <bean id="myBean" class="com.example.MyBean" scope="prototype"/>
        ```

    === "Java-based Configuration"

        ```java
        package com.example;

        @Configuration
        public class AppConfig {
            @Bean
            @Scope("prototype")
            public MyBean myBean() {
                return new MyBean();
            }
        }
        ```

    === "Annotation-based Configuration"

        ```java
        package com.example;

        @Component
        @Scope("prototype")
        public class MyBean {
        }
        ```

## Dependency Injection

Spring supports multiple ways to inject dependencies into beans. The most common methods are:

### Setter Injection

Setter injection uses setter methods to inject dependencies into a bean. It is flexible and allows changing dependencies at runtime. Must have setter methods defined in the class.

???+ example

    === "XML Configuration"

        ```xml
        <bean id="processor" class="com.example.Processor">
            <property name="brand" value="Intel"/>
            <property name="model" value="Core i7"/>
        </bean>
        <bean id="memory" class="com.example.Memory">
            <property name="type" value="DDR4"/>
            <property name="size" value="16"/>
        </bean>
        <bean id="computer" class="com.example.Computer">
            <property name="processor" ref="processor"/>
            <property name="memory" ref="memory"/>
        </bean>
        ```

    === "Java-based Configuration"

        ```java
        package com.example;

        @Configuration
        public class AppConfig {
            @Bean
            public Processor processor() {
                Processor processor = new Processor();
                processor.setBrand("Intel");
                processor.setModel("Core i7");
                return processor;
            }

            @Bean
            public Memory memory() {
                Memory memory = new Memory();
                memory.setType("DDR4");
                memory.setSize(16);
                return memory;
            }

            @Bean
            public Computer computer() {
                Computer computer = new Computer();
                computer.setProcessor(processor());
                computer.setMemory(memory());
                return computer;
            }
        }
        ```

    === "Annotation-based Configuration"

        === "Processor.java"

            ```java
            package com.example;

            @Component
            public class Processor {
                private String brand;
                private String model;

                @Value("Intel")
                public void setBrand(String brand) {
                    this.brand = brand;
                }

                @Value("Core i7")
                public void setModel(String model) {
                    this.model = model;
                }

                public String getDetails() {
                    return brand + " " + model;
                }
            }
            ```

        === "Memory.java"

            ```java
            package com.example;

            @Component
            public class Memory {
                private String type;
                private int size;

                @Value("DDR4")
                public void setType(String type) {
                    this.type = type;
                }

                @Value("16")
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
            package com.example;

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
                    System.out.println("Computer Specs:");
                    System.out.println("Processor: " + processor.getDetails());
                    System.out.println("Memory: " + memory.getDetails());
                }
            }
            ```

### Constructor Injection

Constructor injection uses constructor parameters to inject dependencies into a bean. It is useful when dependencies are mandatory and cannot be changed at runtime.

???+ example

    === "XML Configuration"

        ```xml
        <bean id="processor" class="com.example.Processor">
            <constructor-arg value="Intel"/>
            <constructor-arg value="Core i7"/>
        </bean>
        <bean id="memory" class="com.example.Memory">
            <constructor-arg value="DDR4"/>
            <constructor-arg value="16"/>
        </bean>
        <bean id="computer" class="com.example.Computer">
            <constructor-arg ref="processor"/>
            <constructor-arg ref="memory"/>
        </bean>
        ```

    === "Java-based Configuration"

        ```java
        package com.example;

        @Configuration
        public class AppConfig {
            @Bean
            public Processor processor() {
                return new Processor("Intel", "Core i7");
            }

            @Bean
            public Memory memory() {
                return new Memory("DDR4", 16);
            }

            @Bean
            public Computer computer() {
                return new Computer(processor(), memory());
            }
        }
        ```

    === "Annotation-based Configuration"

        ```java
        package com.example;

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

### Field Injection

Field injection uses annotations to inject dependencies directly into the fields of a bean. It is less verbose but can make testing and refactoring more difficult. It is generally not recommended for production code.

???+ example

    ```java
    package com.example;

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

### Autowiring

Autowiring automatically resolves and injects dependencies into a bean. It reduces the need for explicit configuration. There are several modes of autowiring:

1. **No Autowiring:** Default mode. No autowiring is performed.
2. **By Type:** The container looks for a bean of the same type and injects it.
3. **By Name:** The container looks for a bean with the same name as the property and injects it.
4. **Constructor:** The container looks for a constructor with matching types and injects the dependencies.
5. **By Type with Qualifier:** The container looks for a bean of the same type and uses the `@Qualifier` annotation to resolve ambiguity.
6. **By Name with Qualifier:** The container looks for a bean with the same name as the property and uses the `@Qualifier` annotation to resolve ambiguity.
7. **By Type with Primary:** The container looks for a bean of the same type and uses the `@Primary` annotation to resolve ambiguity.
8. **By Name with Primary:** The container looks for a bean with the same name as the property and uses the `@Primary` annotation to resolve ambiguity.
9. **By Type with Factory Method:** The container looks for a factory method that returns a bean of the same type and injects it.
10. **By Name with Factory Method:** The container looks for a factory method that returns a bean with the same name as the property and injects it.
11. **By Type with Factory Bean:** The container looks for a factory bean that returns a bean of the same type and injects it.

???+ example

    === "XML Configuration"

        ```xml
        <bean id="processor" class="com.example.Processor">
            <property name="brand" value="Intel"/>
            <property name="model" value="Core i7"/>
        </bean>
        <bean id="memory" class="com.example.Memory">
            <property name="type" value="DDR4"/>
            <property name="size" value="16"/>
        </bean>
        <bean id="computer" class="com.example.Computer" autowire="byName"/>
        ```

    === "Java-based Configuration"

        ```java
        package com.example;

        @Configuration
        public class AppConfig {
            @Bean
            public Processor processor() {
                return new Processor("Intel", "Core i7");
            }

            @Bean
            public Memory memory() {
                return new Memory("DDR4", 16);
            }

            @Bean
            public Computer computer() {
                return new Computer(processor(), memory());
            }
        }
        ```

    === "Annotation-based Configuration"

        ```java
        package com.example;

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

## Using `Primary` to Resolve Bean Ambiguity

When multiple beans of the same type are eligible for injection you can explicitly use the bean name, in java based config Use `Qualifier` or use the `@Primary` annotation or the `primary` attribute in XML can be used to mark one bean as the default choice.

???+ example

    === "Class Definition"

        === "Processor.java"

            ```java
            package com.example;

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
            package com.example;

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
        <bean id="intelProcessor" class="com.example.Processor">
            <constructor-arg value="Intel"/>
            <constructor-arg value="Core i7"/>
        </bean>

        <bean id="amdProcessor" class="com.example.Processor" primary="true">
            <constructor-arg value="AMD"/>
            <constructor-arg value="Ryzen 7"/>
        </bean>
        ```

    === "Java-based Configuration"

        ```java
        package com.example;

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
    
    === "Annotation-based Configuration"

        ```java
        package com.example;

        @Component
        public class Computer {
            @Autowired
            @Qualifier("amdProcessor") // specify the bean name to be injected
            private Processor processor;

            @Autowired
            @Qualifier("memory") // specify the bean name to be injected
            private Memory memory;

            public void displaySpecs() {
                System.out.println("Processor: " + processor.getDetails());
            }
        }
        ```

## Inner Beans

Inner beans are beans defined within the `<property>` or `<constructor-arg>` tags of another bean. They are used when a bean is only relevant as a dependency of another bean and does not need to be reused elsewhere.

???+ example

    === "XML Configuration"

        ```xml
        <bean id="computer" class="com.example.Computer">
            <constructor-arg>
                <bean class="com.example.Processor">
                    <constructor-arg value="Intel"/>
                    <constructor-arg value="Core i7"/>
                </bean>
            </constructor-arg>
            <constructor-arg>
                <bean class="com.example.Memory">
                    <constructor-arg value="DDR4"/>
                    <constructor-arg value="16"/>
                </bean>
            </constructor-arg>
        </bean>
        ```

    === "Java-based Configuration"

        ```java
        @Configuration
        public class AppConfig {
            @Bean
            public Computer computer() {
                return new Computer(
                    new Processor("Intel", "Core i7"),
                    new Memory("DDR4", 16)
                );
            }
        }
        ```

    === "Annotation-based Configuration"

        ```java
        package com.example;

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
                System.out.println("Computer Specs:");
                System.out.println("Processor: " + processor.getDetails());
                System.out.println("Memory: " + memory.getDetails());
            }
        }
        ```
