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
