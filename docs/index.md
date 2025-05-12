# Introduction

## Overview

Spring Framework is a powerful framework for building enterprise-grade Java applications, supporting dependency injection, aspect-oriented programming, and more.

???+ info "Key Features"

    - **Lightweight:** Minimal overhead, easy integration.
    - **Modular:** Flexible architecture for selective component use.
    - **Open Source:** Free under the Apache License 2.0.
    - **Enterprise Ready:** Robust features for large-scale applications.
    - **Testable:** Built-in support for testing.
    - **IoC Container:** Manages object creation and configuration.
    - **AOP:** Modularizes cross-cutting concerns.
    - **Microservices Ready:** Supports microservices with Spring Boot.

???+ info "Spring Framework Principles"

    - **Choice at Every Level:** Offers flexibility in architectural decisions.
    - **Support for Diversity:** Accommodates various application architectures.
    - **Strong Backward Compatibility:** Ensures smooth upgrades across versions.
    - **Careful API Design:** Provides intuitive and consistent APIs.
    - **High Code Quality:** Maintains a clean structure with no circular dependencies.



## Getting Started

1. **Install Prerequisites:**
    - Java Development Kit (JDK)
    - Maven or Gradle

2. **Set Up a Project:**
    - Use [Spring Initializr](https://start.spring.io/) to bootstrap a project.

3. **Learn and Explore:**
    - Refer to official documentation and guides.

---

## Core Concepts

### IoC (Inversion of Control)
- **Principle:** Transfers control of object creation and dependency management to a framework.
- **Purpose:** Decouples tasks from implementation, improving modularity and testability.
- **Example:** Spring manages object creation and lifecycle.

### IoC Container
- **Definition:** A framework component implementing IoC, managing object (bean) lifecycles and dependencies.
- **Responsibilities:**
    - Instantiate and configure beans.
    - Manage bean lifecycles.
- **Examples:** 
    - `BeanFactory`: Basic IoC container providing simple functionality.
    - `ApplicationContext`: Advanced container with additional features like AOP, event propagation and internationalization.

### DI (Dependency Injection)
- **Definition:** A design pattern to achieve IoC by injecting dependencies into objects.
- **Types:**
    1. **Constructor Injection:** Dependencies via constructor.
    2. **Setter Injection:** Dependencies via setter methods.
    3. **Field Injection:** Direct injection using `@Autowired` (less recommended).
- **Purpose:** Reduces tight coupling, improving testability and maintainability.

### Relationship Between IoC, IoC Container, and DI
- **IoC** is the overarching principle.
- The **IoC Container** is the implementation of IoC in Spring.
- **DI** is the design pattern used by the IoC Container to achieve IoC.

---

## Resources

- [Official Spring Documentation](https://docs.spring.io/spring-framework/reference/spring-projects.html)
- [Spring Guides](https://spring.io/guides)