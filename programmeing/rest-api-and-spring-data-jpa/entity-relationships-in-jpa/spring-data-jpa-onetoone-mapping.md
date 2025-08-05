# Spring Data JPA - OneToOne mapping

## Objectives

By the end of this module the student would be able to:

* Set up `@OneToOne` mappings between JPA entities
* Configure and understand unidirectional and bidirectional `@OneToOne` relationships
* Persist and retrieve related entities using Spring Data JPA
* Understand how ownership (`mappedBy`) affects table structure and foreign key placement
* Use cascading options (`CascadeType`) to control persistence behavior between linked entities
* Test and verify entity relationships using integration tests
* Expose `@OneToOne` relationships through RESTful endpoints
* Handle serialization issues (e.g., infinite recursion) using annotations like `@JsonManagedReference` and `@JsonBackReference`



## Overview

In Spring Data JPA, a `@OneToOne` relationship represents a **one-to-one association** between two entities. This means that one entity instance is associated with exactly one instance of another entity.

Example: A `Student` entity has one `ProfilePhoto`.

This kind of relationship is useful when you want to split a large object into two tables for modularity.

## Creating a OneToOne mapping

Suppose we want to create a one-to-one mapping. When using MySQL it could be written using SQL:

```sql
CREATE TABLE student (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255),
    profile_photo_id BIGINT,
    FOREIGN KEY (profile_photo_id) REFERENCES profile_photo(id)
);

CREATE TABLE profile_photo (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    file_name VARCHAR(255),
    updated_at DATE
);
```

Here:

* The `student` table contains a **foreign key** pointing to the `profile_photo` table.
* This models that **each student can have one profile photo**, and each profile photo belongs to exactly one student.

We can represent this relationship in Java using the `@OneToOne` annotation.

{% code title="Student.java" %}
```java
@Entity
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    @OneToOne
    private ProfilePhoto profilePhoto;

    // Getters and setters
}
```
{% endcode %}

{% code title="ProfilePhoto.java" %}
```java
@Entity
public class ProfilePhoto {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String fileName;
    private LocalDate updatedAt;

    // Getters and setters
}
```
{% endcode %}

