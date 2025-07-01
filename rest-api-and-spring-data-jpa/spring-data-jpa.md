# Spring Data JPA

## Objectives

* WRITE SOMETHING HERE



Literature used:

* Spring boot in action, 2024

## Intro to Spring Data JPA

Spring Data JPA is part of the Spring Data framework ([https://spring.io/projects/spring-data](https://spring.io/projects/spring-data)) an umbrella project designed to simplify data access in Spring-based applications. Its main goal is to reduce boilerplate code and provide a consistent programming model across different types of data stores, such as relational databases (e.g., MySQL, PostgreSQL).

**Spring Data JPA** focuses specifically on integrating the **Jakarta Persistence API (JPA)** with the Spring ecosystem. While JPA itself is just a set of interfaces for object-relational mapping (ORM), Spring Data JPA builds on top of a JPA provider like **Hibernate**, which supplies the actual implementation

## Why use Spring Data JPA?

Spring Data JPA streamlines working with relational databases in Java by:

* **Reducing boilerplate**: Eliminates the need to write common CRUD operations.
* **Providing a unified API**: Offers the same repository-based interface regardless of the underlying database.
* **Encouraging best practices**: Promotes separation of concerns and a clean architecture.
* **Enabling rapid development**: Developers can focus on business logic rather than repetitive data access code.

In essence, Spring Data JPA acts as a bridge between Java objects and the underlying relational database, enabling developers to interact with their database using standard Java code and minimal configuration.

## Configuring Spring Data JPA

In the following you are shown configurations for connecting to two different relational databases:

* H2 database: An in memory database, with&#x20;
* MySQL database:

In many projects we are going to use both databases, the H2 for testing purposes, and the MySQL database for the actual production database.

### Using an in-memory database (H2)

To configure a relational database, you need to add two additional dependencies in

In order to use Spring Data JPA with H2 in an existing project, you need to add two additional dependencies to the `pom.xml` file:

{% code title="pom.xml" %}
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```
{% endcode %}

Every spring boot project contains a `application.properties` file, in which you can configure variuous properties for your application. To configure the H2 database, add the following:

{% code title="application.properties" %}
```properties
# H2 in-memory database configuration
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password= 
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
```
{% endcode %}

This is everything that is needed for the connection to be established.

#### Verify the database connection

To verify the database connection, add the following to the existing test class:

```java
@SpringBootTest
class JpaApplicationTests {

    @Autowired
    private DataSource dataSource;

    @Test
    public void whenConnectingToDatabase_thenDatabaseIsAccessible() throws SQLException {
        try (Connection connection = dataSource.getConnection()) {
            String dbName = connection.getMetaData().getDatabaseProductName();
            assertThat(dbName).isNotNull();
            assertThat(dbName).isEqualTo("H2");
            System.out.println("Connected to: " + dbName);
        }
    }
}
```

{% hint style="info" %}
The `@SpringBootTest` annotation tells Spring Boot to start the **full application context**, similar to how it would run in production. This is necessary to autowire beans like `DataSource`.

Without `@SpringBootTest`, Spring wouldn't initialize the context, and the `@Autowired` `dataSource` would be `null`.
{% endhint %}

**Run the test!** If configured properly it should pass the test, and print the message:

```bash
Connected to: H2
```



### Configuring a relational database (MySQL)

#### Prerequisites

To follow along, make sure you have **MySQL** installed (in addition MySQL Workbench or DataGrip).

You can verify that you have MySQL, by running the following command in a terminal:

```bash
mysql --version
```

This should output the MySQL version (if installed).

#### Create a new database

Create a new database called `jpatest` in your preffered way. It can be done by using the terminal:

```bash
mysql -u {{your username}} -p
```

It prompts you for your password - type it, and press enter.

Create the database by running (remember the trailing semicolon):

```sql
CREATE DATABASE jpatest;
```

#### Configuration

With this out of the way, we need to add the MySQL driver dependency to our `pom.xml`:

{% code title="pom.xml" %}
```xml
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <scope>runtime</scope>
</dependency>
```
{% endcode %}

In our application.properties file replace the content, with the following:&#x20;

```properties
# H2 in-memory database configuration
#spring.datasource.url=jdbc:h2:mem:testdb
#spring.datasource.driverClassName=org.h2.Driver
#spring.datasource.username=sa
#spring.datasource.password=
#spring.h2.console.enabled=true
#spring.h2.console.path=/h2-console

# MySQL configuration
spring.datasource.url=jdbc:mysql://localhost:3306/jpatest
spring.datasource.username= #Enter your username
spring.datasource.password= #Enter your password
```

#### Verify the database connection

Run the same test as before. The test should fail, and the reason is that it still expects the `dbName` to be `H2` - fix it by replacing it with `MySQL`.

If the test still fails, make sure that your username and password are correct!



## Creating a database
