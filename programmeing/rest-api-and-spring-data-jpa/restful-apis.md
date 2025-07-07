# RESTful API's

## What is an API?

API stands for **Application Programming Interface**. It’s a way for different parts of software to talk to each other — just like **a contract** or **a waiter** in a restaurant.



## What is REST?

**REST** stands for **Representational State Transfer**.

It’s a set of rules (an **architectural style**) for building web services that allow systems to **communicate over the internet** using **standard HTTP methods** — like `GET`, `POST`, `PUT`, and `DELETE`.

### An API is RESTfull if:

* **Stateless**: Each request from a client to a server must contain all the information the server needs to fulfill the request. No session state is stored on the server.
* **Client-Server Architecture**: RESTful APIs are based on a client-server model, where the client and server operate independently, allowing scalability.



* **Cacheable**: Responses from the server can be explicitly marked as cacheable or non-cacheable to improve performance.
* **Uniform Interface**: REST APIs follow a set of conventions and constraints, such as consistent URL paths, standardized HTTP methods, and status codes, to ensure smooth communication.
* **Layered System**: REST APIs can be deployed on multiple layers, which helps with scalability and security.

## What have you done so far?

Up until now, you have only created server-side rendered applications using Spring Boot with Thymeleaf and MySQL database. This means that the HTML served from the backend to the browser is full hydrated from the server, and no additional happening in the browser. &#x20;

Let’s assume that you wanted to create a mobile app, or maybe a JavaScript frontend later using something like React or Vue. In that case, you wouldn't want your backend to send fully rendered HTML anymore. Instead, you’d want your backend to expose **data**, not pages — and let the frontend decide how to display that data.

This is where **REST APIs** come in.

A REST API lets your backend application expose structured data (usually in JSON format) over the web. Frontend applications (including mobile apps, JavaScript apps, or even other servers) can then **consume** this data by making HTTP requests.

Instead of returning an HTML page, your backend returns just the raw data. For example, instead of rendering a page for user details, your API might return:

```json
{
  "id": 1,
  "name": "Osman",
  "email": "osnb@kea.dk"
}
```

This response would typically come from calling `https://somedomain.com/api/users/1` .



## Creating a simple API in Spring Boot:

To create an API with Spring Boot, the only dependency we need is `Spring Web` :

<pre class="language-xml" data-title="pom.xml"><code class="lang-xml">&#x3C;dependency>
    &#x3C;groupId>org.springframework.boot&#x3C;/groupId>
<strong>    &#x3C;artifactId>spring-boot-starter-web&#x3C;/artifactId>
</strong>&#x3C;/dependency>
</code></pre>

Furthermore we will create a simple class that mimics the JSON response from above:

{% code title="User.java" %}
```java
public class User {
    private Long id;
    private String name;
    private String email;
    
    // Constructors, Getters & Setters
}
```
{% endcode %}

All that is left is to create a controller that that receives the incoming HTTP request:

{% code title="UserController.java" %}
```java
@RestController
public class UserController {
    @GetMapping
    public User getUser() {
        return new User(1L, "Osman", "osnb@kea.dk");
    }
}
```
{% endcode %}

{% hint style="info" %}
Marking a class with `@RestController` tells **Spring boot** that this class returns data and **not views.**

The return value is **written directly to the HTTP response body** as JSON.
{% endhint %}

Running the application and opening it in the browser (e.g. [http://localhost:8080](http://localhost:8080/)) will display the following JSON response:

```json
{
  "id": 1,
  "name": "Osman",
  "email": "osnb@kea.dk"
}
```

This means your Spring Boot application has successfully handled an HTTP `GET` request and returned a JSON response instead of an HTML page.

Unlike a Thymeleaf controller, where we would return a view name (e.g. `"index"`), this REST controller returns **data** — in this case, a `User` object. Spring Boot automatically converts (or **serializes**) the Java object into JSON using a library called **Jackson**.

### Making the API better

At the moment, the API works — but the endpoint [http://localhost:8080](http://localhost:8080/) doesn’t clearly indicate what kind of resource is being requested. It’s too generic, and that can be confusing for the consumers of the API.

Instead, we want our endpoints to **describe the resource** they return. This is a key principle in REST: URLs should be **resource-oriented** and **semantic**.

We want the endpoint to be defined at [http://localhost:8080/api/users](http://localhost:8080/) to return a list of all users, and [http://localhost:8080/api/users/1](http://localhost:8080/) to return the specific user with `id=1`.

#### Updating the controller

We simulate a list of users directly in the controller (usally this would come from some database). And add an additional method

<pre class="language-java" data-title="UserController.java"><code class="lang-java">@RestController
<strong>@RequestMapping("/api/users")
</strong>public class UserController {

<strong>    private List&#x3C;User> users = Arrays.asList(
</strong><strong>        new User(1L, "Osman", "osnb@kea.dk"),
</strong><strong>        new User(2L, "User 2", "user2@kea.dk"),
</strong><strong>        new User(3L, "User 3", "user3@kea.dk")
</strong><strong>    );
</strong>
    @GetMapping
<strong>    public List&#x3C;User> getAllUsers() {
</strong><strong>        return users;
</strong><strong>    }
</strong>
<strong>    @GetMapping("/{id}")
</strong><strong>    public User getUserById(@PathVariable Long id) {
</strong><strong>        for (var user : users) {
</strong><strong>            if (user.getId().equals(id)) {
</strong><strong>                return user;
</strong><strong>            }
</strong><strong>        }
</strong><strong>        return null;
</strong><strong>    }
</strong>}
</code></pre>

{% hint style="info" %}
The `{id}` in `@GetMapping("/{id}")` is a **placeholder** in the URL.

The `@PathVariable` annotation tells Spring to **extract the value** from the URL and pass it to the method.

For example, if the user accesses `/api/users/3`, then `id` will be `3`.
{% endhint %}

This makes the API much more **descriptive**, **structured**, and **RESTful**.

### Using `ResponseEntity`&#x20;

Up until now, our controller method returned a `User` object directly. Spring Boot automatically converted that into JSON and sent it back with a `200 OK` status.

But what if:

* The user is not found?
* You want to set a different status code (like `404 Not Found`)?
* You want to include extra headers or customize the response?

That’s where `ResponseEntity<T>` comes in.

{% hint style="info" %}
`ResponseEntity` is a Spring class that lets you:

* Return both the **data** (`body`)
* And control the **HTTP status code**, headers, etc.
{% endhint %}

#### Updating the controller with `ResponseEntity`

<pre class="language-java" data-title="UserController.java"><code class="lang-java">@RestController
@RequestMapping("/api/users")
public class UserController {

    private List&#x3C;User> users = Arrays.asList(
        new User(1L, "Osman", "osnb@kea.dk"),
        new User(2L, "User 2", "user2@kea.dk"),
        new User(3L, "User 3", "user3@kea.dk")
    );

    @GetMapping
<strong>    public ResponseEntity&#x3C;List&#x3C;User>> getAllUsers() {
</strong><strong>        return ResponseEntity.ok(users);
</strong>    }

    @GetMapping("/{id}")
<strong>    public ResponseEntity&#x3C;User> getUserById(@PathVariable Long id) {
</strong>        for (var user : users) {
            if (user.getId().equals(id)) {
<strong>                return ResponseEntity.ok(user);
</strong>            }
        }
<strong>        return ResponseEntity.notFound().build();
</strong>    }
}
</code></pre>

Instead of always returning a `200 OK` response — even when a user isn't found — the controller now correctly returns a **`404 Not Found`** when the requested ID doesn’t exist.

### Adding full CRUD

We want to support all CRUD operations done to the User resource, in other words we want to have the following endpoints:

* **GET** `http://localhost:8080/api/users` → _Get all users_
* **GET** `http://localhost:8080/api/users/{id}` → _Get a specific user_
* **POST** `http://localhost:8080/api/users` → _Create a user_
* **PUT** `http://localhost:8080/api/users/{id}` → _Update an existing user_
* **DELETE** `http://localhost:8080/api/users/{id}` → _Delete a user_

{% hint style="info" %}
Notice that we **never write URLs like** <mark style="color:red;">`/api/users/create`</mark> or <mark style="color:red;">`/api/users/delete`</mark> in REST APIs.

That’s because in REST, the **HTTP method** (like `POST`, `GET`, `PUT`, or `DELETE`) already describes the action being taken. <mark style="color:green;">**The URL should only describe the resource, not the operation.**</mark>
{% endhint %}

To mark a method as a handler for a specific HTTP method in Spring Boot, we use method-level annotations like:

* `@GetMapping` for GET requests
* `@PostMapping` for POST requests
* `@PutMapping` for PUT requests
* `@DeleteMapping` for DELETE requests

Each of these corresponds to one of the CRUD operations.

Below is an updated version of the `UserController` class with **full CRUD support** using an in-memory list:

<pre class="language-java"><code class="lang-java">@RestController
@RequestMapping("/api/users")
public class UserController {

    private List&#x3C;User> users = new ArrayList&#x3C;>(List.of(
        new User(1L, "Osman", "osnb@kea.dk"),
        new User(2L, "User 2", "user2@kea.dk"),
        new User(3L, "User 3", "user3@kea.dk")
    ));

    @GetMapping
    public ResponseEntity&#x3C;List&#x3C;User>> getAllUsers() {
        return ResponseEntity.ok(users);
    }

    @GetMapping("/{id}")
    public ResponseEntity&#x3C;User> getUserById(@PathVariable Long id) {
        for (User user : users) {
            if (user.getId().equals(id)) {
                return ResponseEntity.ok(user);
            }
        }
        return ResponseEntity.notFound().build();
    }

<strong>    @PostMapping
</strong><strong>    public ResponseEntity&#x3C;User> createUser(@RequestBody User newUser) {
</strong><strong>        users.add(newUser);
</strong><strong>        return ResponseEntity.ok(newUser);
</strong><strong>    }
</strong>
<strong>    @PutMapping("/{id}")
</strong><strong>    public ResponseEntity&#x3C;User> updateUser(@PathVariable Long id, @RequestBody User updatedUser) {
</strong><strong>        for (User user : users) {
</strong><strong>            if (user.getId().equals(id)) {
</strong><strong>                user.setName(updatedUser.getName());
</strong><strong>                user.setEmail(updatedUser.getEmail());
</strong><strong>                return ResponseEntity.ok(user);
</strong><strong>            }
</strong><strong>        }
</strong><strong>        return ResponseEntity.notFound().build();
</strong><strong>    }
</strong>
<strong>    @DeleteMapping("/{id}")
</strong><strong>    public ResponseEntity&#x3C;Void> deleteUser(@PathVariable Long id) {
</strong><strong>        for (int i = 0; i &#x3C; users.size(); i++) {
</strong><strong>            if (users.get(i).getId().equals(id)) {
</strong><strong>                users.remove(i);
</strong><strong>                return ResponseEntity.noContent().build();
</strong><strong>            }
</strong><strong>        }
</strong><strong>        return ResponseEntity.notFound().build();
</strong><strong>    }
</strong>}

</code></pre>

This gives a full CRUD for the user resource.

#### Testing the API in PostMan

TODO
