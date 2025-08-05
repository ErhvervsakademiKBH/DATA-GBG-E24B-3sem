# Entity Relationships in JPA

**Objective:** Understand how to model and implement relationships between domain entities in Spring Data JPA.

#### Topics:

* The four main JPA relationship types:
  * `@OneToOne`
  * `@OneToMany` / `@ManyToOne`
  * `@ManyToMany`
* Ownership and bidirectionality
* `mappedBy`, `JoinColumn`, and `JoinTable`
* Cascade operations (`PERSIST`, `REMOVE`, etc.)
* Fetch strategies: `EAGER` vs `LAZY`
* Creating and testing relationships
* JSON serialization issues and how to solve them (`@JsonIgnore`, `@JsonManagedReference`, etc.)



## Overview

In most applications, entities are related to each other. For example, a `User` might have many `Posts`, or a `Student` might enroll in many `Courses`.

JPA provides annotations to define these relationships clearly, both in the object model (Java classes) and in the database (tables and foreign keys).



