# Spring JDBC

Spring JDBC is a module of the Spring Framework that simplifies database access using JDBC (Java Database Connectivity). It abstracts away much of the boilerplate code (connection management, exception handling, etc.) and provides a consistent API for interacting with any JDBC-compliant database.

## Key Components

???+ info "Spring JDBC Core Components"

    - **JdbcTemplate:** Core class for executing SQL queries, updates, and batch operations.
    - **RowMapper:** Maps rows of a `ResultSet` to Java objects.
    - **ResultSetExtractor:** Extracts data from a `ResultSet` (for custom mapping).
    - **NamedParameterJdbcTemplate:** Like `JdbcTemplate`, but supports named parameters in SQL.
    - **SimpleJdbcCall:** Simplifies calling stored procedures/functions.
    - **SimpleJdbcInsert:** Simplifies insert operations.
    - **DataSource:** Factory for database connections (e.g., `DriverManagerDataSource`, HikariCP).
    - **Transaction Management:** Integrates with Spring's transaction management.
    - **Exception Translation:** Converts JDBC exceptions to Spring's `DataAccessException` hierarchy.
    - **Batch Processing:** Efficiently executes multiple statements in a batch.
    - **Connection Management:** Handles opening/closing connections and supports pooling.
    - **Integration:** Works seamlessly with other Spring modules (MVC, Security, etc.).
    - **Database Agnostic:** Consistent API for any JDBC-compliant database.

## Basic Usage

???+ info "How to Use Spring JDBC"

    === "Dependencies"

        ```groovy
        // https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-jdbc
        implementation 'org.springframework.boot:spring-boot-starter-jdbc:3.4.5'
        // https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-jpa
        runtimeOnly 'com.h2database:h2:2.3.232'
        // https://mvnrepository.com/artifact/org.springframework/spring-jdbc
        implementation 'org.postgresql:postgresql:42.7.5'
        // https://mvnrepository.com/artifact/com.mysql/mysql-connector-j
        implementation 'com.mysql:mysql-connector-j:9.3.0'
        ```

    === "application.properties"

        ```properties
        spring.datasource.url=jdbc:postgresql://localhost:5432/testdb
        spring.datasource.username=user
        spring.datasource.password=password
        spring.datasource.driver-class-name=org.postgresql.Driver

        # H2 database configuration (for development/testing)
        spring.datasource.url=jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
        spring.h2.console.enabled=true

        # The following are defaults in Spring Boot and can be omitted unless you want to override them:
        # spring.h2.console.path=/h2-console
        # spring.datasource.initialization-mode=always
        # spring.datasource.schema=classpath:schema.sql
        # spring.datasource.data=classpath:data.sql
        ```

    === "Java-based Configuration"

        ```java
        import org.springframework.context.annotation.Bean;
        import org.springframework.context.annotation.Configuration;
        import org.springframework.jdbc.datasource.DriverManagerDataSource;
        import javax.sql.DataSource;

        @Configuration
        public class DataSourceConfig {
            @Bean
            public DataSource dataSource() {
                DriverManagerDataSource ds = new DriverManagerDataSource();
                ds.setDriverClassName("org.postgresql.Driver");
                ds.setUrl("jdbc:postgresql://localhost:5432/testdb");
                ds.setUsername("user");
                ds.setPassword("password");
                return ds;
            }
        }
        ```

    === "Repository.java"

        ```java
        public class Repository {
            private JdbcTemplate jdbcTemplate;

            @Autowired
            public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
                this.jdbcTemplate = jdbcTemplate;
            }

            public void createTable() {
                String sql = "CREATE TABLE users (id INT PRIMARY KEY, name VARCHAR(50))";
                jdbcTemplate.execute(sql);
            }

            public void insertUser(int id, String name) {
                String sql = "INSERT INTO users (id, name) VALUES (?, ?)";
                jdbcTemplate.update(sql, id, name);
            }

            public User getUser(int id) {
                String sql = "SELECT * FROM users WHERE id = ?";
                return jdbcTemplate.queryForObject(
                    sql,
                    new Object[]{id},
                    (rs, rowNum) -> new User(rs.getInt("id"), rs.getString("name"))
                );
            }
        }
        ```

## Best Practices & Tips

???+ tip "Recommendations"

    - Prefer `JdbcTemplate` for most use cases; it handles resource management and exception translation.
    - Always use parameterized queries (`?` or named parameters) to prevent SQL injection.
    - Use `RowMapper` for mapping result sets to domain objects; avoid manual mapping in business logic.
    - For batch operations, use `batchUpdate` for better performance.
    - Use connection pooling (`HikariCP`, `BasicDataSource`, etc.) for production environments.
    - Handle transactions declaratively with `@Transactional` where possible.
    - Catch and handle only `DataAccessException` (not raw JDBC exceptions).
    - Keep SQL in a separate file or as constants for maintainability in larger projects.
    - Use `NamedParameterJdbcTemplate` for complex queries with many parameters for better readability.
    - Test your SQL queries independently to ensure correctness and performance.

## Additional Notes

???+ info "Other Considerations"

    - **Testing:** Use in-memory databases (like H2) for integration testing with Spring JDBC.
    - **Error Handling:** Spring JDBC translates SQLExceptions into unchecked exceptions, so you don't need to catch checked exceptions.
    - **Performance:** For large result sets, consider setting fetch size and using streaming.
    - **Migration:** If you need more advanced ORM features later, you can migrate to Spring Data JPA or Hibernate with minimal changes to your configuration.
    - **Documentation:** Refer to the [Spring JDBC documentation](https://docs.spring.io/spring-framework/reference/data-access/jdbc.html) for more advanced usage and best practices.