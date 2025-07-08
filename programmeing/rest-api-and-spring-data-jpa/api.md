# API

Outline:



1. What is a Web API?
2. Web basics & HTTP
3. JSON
4. REST concepts&#x20;
5. REST API design
6. REST API's in Spring Boot

NOTES:

* Use **real-world analogies**: resources = "things", verbs = "actions".
* Keep examples **fun and relatable** (e.g. API for pets, movies, students).
* Show **both sides**: what the client sends and what the server replies.

## What is a web API?

A **Web API** is a way for applications to communicate with each other over a **network**. It exposes certain functionality or data that other applications can use.

When using a web API, communication happens over  **HTTP (HyperText Transfer Protocol)**, which is the same protocol used by web browsers to retrieve web pages. That’s where the “**web**” in “web API” comes from.

There exists different types of web APIs (like SOAP, REST etc.). **We will focus on REST APIs**

### How does a Web API work?

The consumer of a web API sends a request, which contains the action that they want to perform on some data (resource). The server receives this request, performs the required action (like fetching, creating, updating, or deleting data), and then returns a **response** that contains information about the result.

<figure><img src="../../.gitbook/assets/image (2).png" alt="" width="563"><figcaption></figcaption></figure>

This request–response cycle typically follows the structure of an HTTP communication, where:

* The **CLIENT** initiates a request using HTTP methods like `GET`, `POST`, `PUT`, or `DELETE` - ie. what action they want to perform - and a URL that indicates which data they want it to be performed on.
* The **server** processes the request, interacts with data or business logic, and sends back a structured response, often in **JSON** format.
* The **response** includes an HTTP **status code** (like `200`), headers, and optionally a response **body** with the requested data or error message.

This approach allows applications running on different systems, written in different languages, or running on different devices to talk to each other in a standardized way.

#### HTTP verbs

For reference here are the most common HTTP verbs:

| Verb     | Purpose                      | Example           |
| -------- | ---------------------------- | ----------------- |
| `GET`    | Retrieve data (read)         | `GET /users/1`    |
| `POST`   | Create a new resource        | `POST /users`     |
| `PUT`    | Update a resource completely | `PUT /users/1`    |
| `PATCH`  | Update part of a resource    | `PATCH /users/1`  |
| `DELETE` | Delete a resource            | `DELETE /users/1` |

{% hint style="info" %}
See [https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods) for a full list.
{% endhint %}

#### Common status codes

Status codes `2XX` indicate a **successful response**.

Status codes `4XX` indicates **client error**.

Status codes `5XX` indicates **server error**.

Below are some common statuscodes:

| Code      | Category           | Meaning               | Typical Use Case                          |
| --------- | ------------------ | --------------------- | ----------------------------------------- |
| **`200`** | Success (2xx)      | OK                    | Successful `GET`, `PUT`, `DELETE` request |
| `201`     | Success (2xx)      | Created               | Resource created (e.g., after `POST`)     |
| `204`     | Success (2xx)      | No Content            | Success with no body (e.g., `DELETE`)     |
| `400`     | Client Error (4xx) | Bad Request           | Invalid syntax, missing fields            |
| `401`     | Client Error (4xx) | Unauthorized          | Authentication required or failed         |
| `403`     | Client Error (4xx) | Forbidden             | Authenticated but access is denied        |
| `404`     | Client Error (4xx) | Not Found             | Resource not found                        |
| `500`     | Server Error (5xx) | Internal Server Error | General server failure                    |
| `502`     | Server Error (5xx) | Bad Gateway           | Upstream server error                     |
| `503`     | Server Error (5xx) | Service Unavailable   | Server is overloaded or under maintenance |

{% hint style="info" %}
See [https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status) for a full list.
{% endhint %}

### JSON

**JSON** stands for **JavaScript Object Notation**. It's a human-readable **data format** used to represent structured data. It is the **most commonly used format** for sending and receiving data in web APIs.

JSON is made up of two basic structures:

*   **Objects** – collections of key-value pairs

    ```json
    {
      "id": 1,
      "name": "user 1",
      "email": "user1@mail.dk",
    }
    ```
*   **Arrays** – ordered lists of values or objects

    ```json
    [
      {
        "id": 1,
        "name": "user 1",
        "email": "user1@mail.dk"
      },
      {
        "id": 2,
        "name": "user 2",
        "email": "user2@mail.dk"
      },
    ]
    ```



JSON is the **payload (data)** that is exchanged between the client and server, and it offers a simple and consistent way to represent data structures. Its format is both **easy for humans to read** and **easy for machines to parse and generate**, which makes it ideal for communication between applications.

In a REST API, JSON is typically used in:

* **Request bodies** (e.g. when creating or updating data)
* **Response bodies** (e.g. when retrieving data)



## REST and RESTful APIs

**REST** stands for **Representational State Transfer**. It is an **architectural style** for designing networked applications, especially web services. When an API is built following the principles of REST, it's called a **RESTful API**.

> _**"The ideas in REST aim to be simple and to decouple the API from the underlying services that serve the API. It uses a request-response model and is stateless, as all the information necessary to do something is contained within the request."**&#x20;_&#x20;(Ponelat & Rosenstock, _Designing APIs with Swagger and OpenAPI_, 2022).

For the full
