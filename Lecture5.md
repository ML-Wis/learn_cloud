# REST --- Lecture Notes

## 1. What is REST?

**REST** stands for **Representational State Transfer**.

REST is a **programming / architectural style** used for designing
communication between components of distributed systems.

It is **not a standard or protocol**.

A useful distinction is:

-   **HTTP** → communication protocol
-   **REST** → style/principles for designing how resources are exposed
    and manipulated using HTTP

HTTP can also be used in a **non-RESTful** way. For example, SOAP
services can use HTTP simply as a transport mechanism.

------------------------------------------------------------------------

# 2. Why Study REST?

Distributed systems usually contain multiple components that need to
communicate.

For example:

``` text
        Client / Frontend
                |
               REST
                |
        Backend / Server
                |
               REST
                |
            Database
```

REST is useful because it helps us build systems that are:

1.  **Client-friendly** --- clients can communicate with distributed
    components in a consistent way.
2.  **Simple to understand** --- it uses familiar web concepts such as
    URLs, HTTP methods, representations, and status codes.
3.  **Easy to scale** --- especially because of the stateless nature of
    REST.

------------------------------------------------------------------------

# 3. The 5 Major Concepts of REST

The lecture identifies five major concepts:

1.  **Resources**
2.  **Representations**
3.  **Operations**
4.  **Hypertext**
5.  **Statelessness**

A simple way to remember them:

``` text
RESOURCE
   ↓
What thing are we talking about?

REPRESENTATION
   ↓
In what format is that thing sent?

OPERATION
   ↓
What do we want to do with it?

HYPERTEXT
   ↓
What related resources can we follow?

STATELESSNESS
   ↓
Does the server need to remember previous communication?
```

------------------------------------------------------------------------

# 4. Resources

## What is a Resource?

A **resource** is a "thing" that our application cares about and that
can be identified.

For a BookShop application, resources might be:

``` text
Books
Users
Shopping Cart
Recommendations
Friends
```

For example:

``` text
/books/1
```

can identify book number 1.

The important idea is:

> If something is worth identifying, give it an identifier.

------------------------------------------------------------------------

## Example: Light Resource

The lecture gives an example involving a light in a seminar hall.

A resource might be identified using a path such as:

``` text
/vidhansoudha/groundfloor/cmoffice/light/1
```

This identifies the first light.

Once the light has an identifier, the application can perform operations
on that specific light.

For example:

``` text
/light/1
/light/2
/light/3
```

refer to different lights.

------------------------------------------------------------------------

# 5. URI and URL

## URI --- Uniform Resource Identifier

A **URI** identifies a resource.

It can identify a resource using:

-   its name
-   its location
-   or both

Think:

``` text
URI = identifies a resource
```

------------------------------------------------------------------------

## URL --- Uniform Resource Locator

A **URL** is a subset/type of URI that specifies where a resource can be
found and how it can be retrieved.

Think:

``` text
URL = tells you where the resource is
```

Example:

``` text
https://example.com/books/123
```

------------------------------------------------------------------------

## Easy Difference

``` text
URI → Identity
URL → Location
```

The REST design principle from the lecture is:

> **Identify everything that is worth being identified.**

------------------------------------------------------------------------

# 6. Representations

A resource itself is not necessarily sent directly to the client.

Instead, the server sends a **representation** of that resource.

For example, suppose:

``` text
/books/123
```

represents a book.

The server could send the book as JSON:

``` json
{
  "id": 123,
  "title": "The Hobbit",
  "price": 500
}
```

Or it could send a representation in XML:

``` xml
<book>
    <id>123</id>
    <title>The Hobbit</title>
    <price>500</price>
</book>
```

The resource is still the same:

``` text
Book #123
```

but its representation can be different:

``` text
Book #123
   |
   +---- JSON
   |
   +---- XML
   |
   +---- HTML
```

------------------------------------------------------------------------

# 7. MIME Types and Content Negotiation

A client can indicate what representation it wants.

For example:

``` http
GET /books/123 HTTP/1.1
Host: example.com
Accept: application/json
```

The important part is:

``` text
Accept: application/json
```

This tells the server:

> "I would like the response in JSON format."

Another example:

``` text
Accept: text/html
```

means the client wants HTML.

The lecture refers to these data formats using **MIME types**.

------------------------------------------------------------------------

## Same Resource, Different Representations

We do not need to create completely different resources just because we
want different formats.

For example, the same resource:

``` text
/books/123
```

can be represented as:

``` text
JSON
XML
HTML
```

The client requests the representation it understands.

If the server does not support the requested MIME type, the lecture
gives **HTTP 406 (Not Acceptable)** as the standard error response.

------------------------------------------------------------------------

# 8. Operations

When building an application, we naturally think about actions such as:

``` text
GetBooks()
AddBookToShoppingCart()
DeleteRecommendation()
```

REST maps these kinds of operations to HTTP methods.

The main methods discussed are:

``` text
GET
POST
PUT
DELETE
PATCH
```

------------------------------------------------------------------------

# 9. GET

## Meaning

**GET** is primarily used to retrieve a representation of a resource.

Example:

``` http
GET /books/123
```

Meaning:

> "Give me the details of book 123."

GET normally does not modify the resource.

Example response:

``` json
{
  "id": 123,
  "title": "The Hobbit",
  "price": 500
}
```

------------------------------------------------------------------------

# 10. PUT

## Meaning

**PUT** is used to create or update a resource by replacement.

Example:

``` http
PUT /books/123
```

with:

``` json
{
  "id": 123,
  "title": "The Hobbit",
  "price": 600
}
```

Think:

> "Make resource 123 have this representation."

PUT is **idempotent**.

------------------------------------------------------------------------

# 11. POST

## Meaning

**POST** is used for creating or partially updating a resource, and
generally for operations where repeated requests can create additional
effects.

Example:

``` http
POST /cart
```

Body:

``` json
{
  "book_id": 123
}
```

This could mean:

> "Add book 123 to the shopping cart."

If the same request is made multiple times, another copy may be added
each time.

Therefore POST is generally **not idempotent**.

------------------------------------------------------------------------

# 12. DELETE

## Meaning

**DELETE** removes a resource.

Example:

``` http
DELETE /recommendations/123
```

Meaning:

> "Delete recommendation 123."

DELETE is idempotent.

If the resource has already been removed, repeating the delete does not
create another removal effect.

------------------------------------------------------------------------

# 13. PATCH

## Meaning

**PATCH** is used for a partial update.

Suppose a user resource is:

``` json
{
  "name": "Divya",
  "age": 21,
  "city": "Bangalore"
}
```

We only want to change the city.

We can use:

``` http
PATCH /users/10
```

with:

``` json
{
  "city": "Hyderabad"
}
```

Only the specified part is updated.

The lecture notes that the idempotency of PATCH **depends on its
implementation**.

------------------------------------------------------------------------

# 14. Safe vs Idempotent

These two concepts are very important.

## Safe

An operation is **safe** when it does not modify the resource.

Example:

``` text
GET
```

You can call:

``` text
GET
GET
GET
GET
```

and retrieving the resource does not change it.

So:

``` text
GET → Safe
```

------------------------------------------------------------------------

# 15. Idempotent

An operation is **idempotent** when calling it multiple times with the
same input does not create an additional effect compared with calling it
once.

In simple words:

> Repeating the same request should leave the resource in the same final
> state.

------------------------------------------------------------------------

## Example: PUT

Suppose:

``` http
PUT /users/10
```

with:

``` json
{
  "name": "Divya"
}
```

After one request:

``` text
name = Divya
```

After two requests:

``` text
name = Divya
```

After ten requests:

``` text
name = Divya
```

The final state is still the same.

Therefore:

``` text
PUT → Idempotent
```

------------------------------------------------------------------------

## Example: POST

Suppose:

``` http
POST /cart
```

with:

``` json
{
  "book_id": 123
}
```

One request:

``` text
Cart:
Book 123
```

Two requests:

``` text
Cart:
Book 123
Book 123
```

Three requests:

``` text
Cart:
Book 123
Book 123
Book 123
```

Each request creates an additional effect.

Therefore:

``` text
POST → Not idempotent
```

------------------------------------------------------------------------

# 16. Safe vs Idempotent --- Do Not Confuse Them

### Safe

Means:

> The operation does not modify the resource.

### Idempotent

Means:

> Repeating the same operation does not create an additional effect.

Therefore:

``` text
GET
Safe       = YES
Idempotent = YES
```

But:

``` text
PUT
Safe       = NO
Idempotent = YES
```

Why?

Because PUT changes the resource, so it is not safe.

But repeating the same PUT produces the same final state, so it is
idempotent.

------------------------------------------------------------------------

# 17. REST Operation Table

  Operation   Main Purpose                                  Safe   Idempotent
  ----------- ------------------------------------------- ------ ------------
  GET         Retrieve a resource                            Yes          Yes
  PUT         Create/replace a resource                       No          Yes
  POST        Create / operation with additional effect       No           No
  DELETE      Remove a resource                               No          Yes
  PATCH       Partially update a resource                     No      Depends

### Easy memory trick

``` text
GET    → Read
POST   → Create / additional effect
PUT    → Replace
PATCH  → Partial update
DELETE → Remove
```

------------------------------------------------------------------------

# 18. BookShop Example

Suppose we are designing a BookShop application.

The application supports:

-   retrieving book details
-   adding a book to a shopping cart
-   liking a book
-   sending a recommendation to a friend
-   deleting a recommendation

Possible resources:

``` text
Books
Recommendations
Shopping Cart
Friend
```

If we want JSON:

``` text
Accept: application/json
```

------------------------------------------------------------------------

## Retrieve Book Details

``` http
GET /books/123
```

Why GET?

Because we only want to retrieve information.

``` text
GET
Safe
Idempotent
```

------------------------------------------------------------------------

## Add a Book to Shopping Cart

``` http
POST /cart
```

Body:

``` json
{
  "book_id": 123
}
```

Why POST?

Because repeated requests can result in additional copies/effects.

``` text
POST
Not safe
Not idempotent
```

------------------------------------------------------------------------

## Like a Book

The lecture uses:

``` http
PUT /books/123/like
```

Why PUT?

The idea is that the operation sets the like state.

Calling it repeatedly does not keep adding another "like" effect.

``` text
PUT
Not safe
Idempotent
```

------------------------------------------------------------------------

## Delete a Recommendation

``` http
DELETE /recommendations/123
```

Why DELETE?

Because we want to remove a resource.

------------------------------------------------------------------------

# 19. Hypertext

Hypertext means that the representation returned for a resource can
contain **links to other resources**.

For example:

``` json
{
  "id": 123,
  "title": "The Hobbit",
  "price": 500,
  "reviews": "/books/123/reviews",
  "author": "/authors/456"
}
```

The response gives the client links to related resources.

The client can follow these links.

Think:

``` text
Book
 |
 +---- Reviews
 |
 +---- Author
 |
 +---- Shopping Cart
```

The important idea is:

> The server can transfer information about the next possible
> resources/actions through hyperlinks.

The client is responsible for following the links.

------------------------------------------------------------------------

# 20. Application State

Before understanding statelessness, understand **application state**.

Consider:

1.  User logs into BookShop.
2.  User buys a book.

The application needs to know:

``` text
Who is the user?
Is the user authenticated?
Who is trying to buy the book?
```

Something has to keep track of this information.

The question is:

``` text
Should the client maintain it?
OR
Should the server maintain it?
```

This leads to the REST principle of statelessness.

------------------------------------------------------------------------

# 21. Statelessness --- Detailed Explanation

## What does "stateless" mean?

REST requires that the server **does not maintain communication state
for a particular client between requests**.

Each request should be treated independently.

In simple words:

> **The server should not have to remember what happened in the previous
> request in order to understand the current request.**

------------------------------------------------------------------------

## Stateful Approach

Imagine this:

``` text
Client
  |
  | "I logged in."
  ↓
Server
  |
  | remembers:
  | "This client is logged in."
  |
  ↓
Client
  |
  | "Buy this book."
  ↓
Server
  |
  | uses previously stored login/session state
```

Here, the server remembers information about the client's previous
communication.

This is a **stateful** approach.

------------------------------------------------------------------------

# 22. Stateless REST Approach

In a stateless approach, every request contains the information needed
to process it.

Conceptually:

``` text
Request 1
   ↓
Server
   ↓
Response

Request 2
   ↓
Server
   ↓
Response

Request 3
   ↓
Server
   ↓
Response
```

The server does not depend on remembering:

``` text
"What did this client tell me earlier?"
```

Each request is handled independently.

For example, a request might contain authentication information:

``` http
GET /profile
Authorization: ...
```

The important REST idea is not the particular authentication mechanism,
but that the server can understand the request without relying on stored
communication state from earlier requests.

------------------------------------------------------------------------

# 23. Statelessness Does NOT Mean "No State Exists"

This is a very important distinction.

REST does **not** mean:

> "The application cannot have any state."

That would be incorrect.

Applications obviously have data/state such as:

``` text
User information
Book information
Shopping cart
Orders
Recommendations
Likes
```

What REST says is that the **server should not maintain
communication/session state for the client between requests**.

The state can instead be:

-   represented as resource state, or
-   managed by the client.

So:

``` text
Application/resource state
        ↓
       CAN exist
```

But:

``` text
Server-side communication state
        ↓
Should NOT be required between requests
```

------------------------------------------------------------------------

# 24. Why Statelessness Is Useful

Statelessness provides several important benefits.

## 24.1 Easier Scaling

Suppose we have:

``` text
             Load Balancer
              /    |    \
             /     |     \
        Server 1 Server 2 Server 3
```

A client sends:

``` text
Request 1 → Server 1
Request 2 → Server 3
Request 3 → Server 2
```

This works more easily when the servers do not depend on a particular
server remembering the client's communication state.

This promotes **redundancy and performance**.

------------------------------------------------------------------------

## 24.2 No Need to Know Which Server Was Used

With stateless communication, the client does not need to keep returning
to exactly the same server just because that server remembers its
session.

Conceptually:

``` text
Request 1 → Server A
Request 2 → Server B
Request 3 → Server C
```

Each server can process the request independently.

------------------------------------------------------------------------

## 24.3 Less Synchronization Overhead

If servers are not maintaining communication state for individual
clients, they don't need to constantly synchronize that communication
state with one another.

This reduces synchronization overhead.

------------------------------------------------------------------------

## 24.4 Better Failure Handling

Imagine:

``` text
Client
   |
   ↓
Server 1  ❌
```

If all necessary information is available to make another request, the
client can communicate with another server:

``` text
Client
   |
   ↓
Server 2  ✅
```

The client is less dependent on the failed server's stored communication
state.

The lecture specifically highlights that when no state is saved on the
server, a client can connect to another server and continue.

------------------------------------------------------------------------

# 25. Statelessness --- One-Line Definition

For exams:

> **REST statelessness means that the server does not maintain
> communication/session state for a client between requests; each
> request is treated independently.**

Remember:

``` text
Stateless ≠ No data/state exists

Stateless = Server does not depend on
previous client communication state.
```

------------------------------------------------------------------------

# 26. HTTP Error Handling

REST uses HTTP response status codes.

Status codes are grouped into categories:

``` text
1xx → Informational
2xx → Success
3xx → Redirection
4xx → Client errors
5xx → Server errors
```

------------------------------------------------------------------------

## Important Status Codes

### 200 --- OK

Request succeeded.

``` http
GET /books/123
```

``` text
200 OK
```

------------------------------------------------------------------------

### 201 --- Created

A resource was successfully created.

------------------------------------------------------------------------

### 400 --- Bad Request

The request sent by the client is invalid.

------------------------------------------------------------------------

### 401 --- Unauthorized

Authentication is required or invalid.

------------------------------------------------------------------------

### 403 --- Forbidden

The client is not allowed to perform the operation.

------------------------------------------------------------------------

### 404 --- Not Found

The requested resource does not exist.

Example:

``` http
GET /books/999999
```

if that book doesn't exist.

------------------------------------------------------------------------

### 406 --- Not Acceptable

The server cannot provide the representation requested by the client.

For example, the client requests a format that the server does not
support.

------------------------------------------------------------------------

### 500 --- Internal Server Error

A server-side error occurred.

------------------------------------------------------------------------

# 27. The Reality of REST

REST is useful, but it is **not perfect for every situation**.

The lecture points out several limitations and practical concerns.

------------------------------------------------------------------------

## 27.1 Resource State Implementation Is Up to the Programmer

REST gives principles for representing resources and communication, but
the exact implementation of resource state is largely the programmer's
responsibility.

For example, you have to decide how your application represents:

``` text
User
Book
Cart
Recommendation
Order
```

and how their state is stored and updated.

REST itself does not magically solve all application-state design
problems.

------------------------------------------------------------------------

## 27.2 Too Many Resources Can Become a Problem

The lecture notes that if a system requires a very large number of
resources, REST may not be a good choice.

The basic idea is that designing and managing a huge resource-oriented
system can become complicated.

Instead of having a clean set of meaningful resources, the system may
become overloaded with resource definitions and interactions.

------------------------------------------------------------------------

## 27.3 Real-Time / Bandwidth-Constrained Systems

The lecture also says REST may not be a good choice for certain:

-   real-time systems
-   bandwidth-constrained scenarios

Why?

Because REST-style communication can involve a **large number of
messages being exchanged**.

If an application needs extremely continuous, low-latency communication,
repeatedly sending independent HTTP requests may not be the most
suitable approach.

The important point from the lecture is:

``` text
More messages
     ↓
More communication overhead
     ↓
Can be unsuitable for some
real-time or bandwidth-constrained systems
```

------------------------------------------------------------------------

# 28. Limitations of REST Listed in the Lecture

The lecture specifically lists:

1.  Built on HTTP 1.1
2.  Lack of multiplexing
3.  Head-of-line blocking
4.  No streaming support
5.  API versioning
6.  JSON performance problems
7.  Lack of typing

Let's understand each clearly.

------------------------------------------------------------------------

## 28.1 Built on HTTP 1.1

The lecture discusses REST in the context of HTTP 1.1.

This creates limitations related to the capabilities of HTTP 1.1.

------------------------------------------------------------------------

## 28.2 Lack of Multiplexing

**Multiplexing** means being able to efficiently handle multiple streams
of communication over a connection.

The lecture lists the lack of multiplexing as a limitation of REST in
the HTTP 1.1 context.

This can make communication less efficient when many requests need to be
handled.

------------------------------------------------------------------------

## 28.3 Head-of-Line Blocking

Head-of-line blocking means that an earlier request can delay later
requests.

Conceptually:

``` text
Request 1 → slow
Request 2 → waiting
Request 3 → waiting
```

Even if Request 2 and Request 3 could otherwise be handled quickly, they
may have to wait because of the earlier request.

The lecture connects this issue to HTTP 1.1 and notes that even
pipelining still has to wait for older requests.

------------------------------------------------------------------------

## 28.4 No Streaming Support

The lecture lists lack of streaming support as another limitation.

This matters for applications that need continuous streams of data
rather than ordinary request/response exchanges.

Examples of scenarios that may require streaming-like communication
include continuously changing data or long-running data flows.

------------------------------------------------------------------------

## 28.5 API Versioning

APIs change over time.

For example:

``` text
/api/v1/books
/api/v2/books
```

When an API changes, older clients may still depend on the old behavior.

Therefore, maintaining multiple versions can become complicated.

The lecture identifies API versioning as a limitation/challenge.

------------------------------------------------------------------------

## 28.6 JSON Performance Problems

JSON is very popular because it is easy for humans and programs to
understand.

Example:

``` json
{
  "name": "Book",
  "price": 500
}
```

However, the lecture points out that using JSON can have performance
problems.

For some systems, JSON may not be the most efficient representation in
terms of communication or processing.

------------------------------------------------------------------------

## 28.7 Lack of Typing

JSON itself does not provide the kind of strong typing/schema guarantees
that some other data formats or interface systems can provide.

For example:

``` json
{
  "price": 500
}
```

does not by itself enforce all of the rules that an application might
want for the `price` field.

The lecture therefore lists **lack of typing** as a limitation.

------------------------------------------------------------------------

# 29. Complete REST Example

Let's put everything together.

Suppose we have:

``` text
BookShop
```

A client wants information about book 123.

### Step 1 --- Resource

``` text
/books/123
```

This identifies the book.

### Step 2 --- Operation

``` http
GET /books/123
```

We want to retrieve it.

### Step 3 --- Representation

``` http
Accept: application/json
```

We want JSON.

### Step 4 --- Server Response

``` http
200 OK
Content-Type: application/json
```

``` json
{
  "id": 123,
  "title": "The Hobbit",
  "price": 500,
  "reviews": "/books/123/reviews"
}
```

### Step 5 --- Hypertext

The response contains:

``` text
/books/123/reviews
```

The client can follow that link to retrieve reviews.

### Step 6 --- Statelessness

The request is processed independently; the server should not need to
depend on remembering previous communication with this client.

So one simple REST interaction contains:

``` text
Resource
   ↓
/books/123

Operation
   ↓
GET

Representation
   ↓
JSON

Hypertext
   ↓
/books/123/reviews

Status
   ↓
200 OK

Statelessness
   ↓
Request is independently understandable
```

------------------------------------------------------------------------

# 30. REST API Design --- Good Structure

A resource-oriented API might look like:

``` text
GET     /books
GET     /books/123
POST    /books
PUT     /books/123
PATCH   /books/123
DELETE  /books/123
```

Notice that we keep the resource name:

``` text
/books
```

and use HTTP methods to indicate what we want to do.

------------------------------------------------------------------------

# 31. Avoid Thinking of REST URLs as Function Names

A common way to think about APIs is:

``` text
/getBook
/addBook
/deleteBook
/updateBook
```

REST instead focuses on **resources**.

A more REST-oriented design is:

``` text
GET    /books/123
POST   /books
PUT    /books/123
PATCH  /books/123
DELETE /books/123
```

The URL identifies the resource.

The HTTP method tells us the operation.

So:

``` text
URL / URI
   ↓
WHAT resource?

HTTP Method
   ↓
WHAT operation?
```

------------------------------------------------------------------------

# 32. REST in One Big Picture

``` text
                     REST
                      |
       +--------------+--------------+
       |              |              |
   RESOURCE      REPRESENTATION   OPERATION
       |              |              |
   /books/123      JSON/XML       GET/POST
                                  PUT/PATCH
                                  DELETE
       |
       +---------------------------+
       |
    HYPERTEXT
       |
    Links to related
      resources
       |
       |
  STATELESSNESS
       |
Each request is independent
and server does not maintain
client communication state
```

------------------------------------------------------------------------

# 33. Exam-Oriented Quick Revision

## REST

``` text
REST = Representational State Transfer
```

REST is:

``` text
A programming / architectural style
```

It is not:

``` text
A protocol
```

------------------------------------------------------------------------

## Five Major Concepts

``` text
1. Resources
2. Representations
3. Operations
4. Hypertext
5. Statelessness
```

------------------------------------------------------------------------

## Resource

A thing that can be identified.

Example:

``` text
/books/123
```

------------------------------------------------------------------------

## URI

``` text
Uniform Resource Identifier
```

Identifies a resource.

------------------------------------------------------------------------

## URL

``` text
Uniform Resource Locator
```

Specifies where/how to retrieve a resource.

------------------------------------------------------------------------

## Representation

The format used to transfer a resource.

Examples:

``` text
JSON
XML
HTML
```

------------------------------------------------------------------------

## HTTP Methods

``` text
GET     → Retrieve
POST    → Create / additional effect
PUT     → Create or replace
PATCH   → Partial update
DELETE  → Remove
```

------------------------------------------------------------------------

## Safe

``` text
Does not modify the resource.
```

Example:

``` text
GET
```

------------------------------------------------------------------------

## Idempotent

``` text
Repeating the same request
does not create an additional effect.
```

Usually:

``` text
GET       → Yes
PUT       → Yes
POST      → No
DELETE    → Yes
PATCH     → Depends
```

------------------------------------------------------------------------

## Hypertext

Returned representations can contain links to other resources.

------------------------------------------------------------------------

## Statelessness

``` text
Server does not maintain
communication/session state
for the client between requests.

Each request is treated independently.
```

Benefits:

``` text
✓ Easier scaling
✓ Better redundancy
✓ Less synchronization overhead
✓ Client is less dependent on a particular server
✓ Easier handling of server failures
```

------------------------------------------------------------------------

## HTTP Status Codes

``` text
1xx → Informational
2xx → Success
3xx → Redirection
4xx → Client Error
5xx → Server Error
```

Important:

``` text
200 → OK
201 → Created
400 → Bad Request
401 → Unauthorized
403 → Forbidden
404 → Not Found
406 → Not Acceptable
500 → Internal Server Error
```

------------------------------------------------------------------------

# 34. Final Memory Trick

Whenever you see a REST API, ask these questions:

``` text
1. WHAT?
   ↓
   Resource

2. HOW?
   ↓
   Representation

3. DO WHAT?
   ↓
   Operation

4. WHERE NEXT?
   ↓
   Hypertext

5. DOES SERVER REMEMBER ME?
   ↓
   Statelessness
```

And remember this single example:

``` http
GET /books/123
Accept: application/json
```

means:

``` text
GET
 ↓
"I want to retrieve"

 /books/123
 ↓
"This is the resource"

 application/json
 ↓
"I want its representation as JSON"

Response
 ↓
200 OK

Response may contain links
 ↓
Hypertext

Request is independently understandable
 ↓
Statelessness
```

That is the core idea behind the entire REST lecture.
