# HTTP

> **Source:** *Real-World Bug Hunting: A Field Guide to Web Hacking*,
> Chapter 1 --- Bug Bounty Basics.
>
> This note expands the HTTP material from the chapter rather than
> treating HTTP as a generic networking textbook. Additional
> explanations are marked where useful.

------------------------------------------------------------------------

# 1. HTTP in the Website-Visit Process

When you enter a website into a browser, several things happen before
the page appears.

The simplified sequence from Chapter 1 is:

``` text
URL
 ↓
Extract domain
 ↓
DNS resolution
 ↓
IP address
 ↓
TCP connection
 ↓
HTTP request
 ↓
HTTP response
 ↓
Browser rendering
```

The relationship can be represented as:

$$
\text{URL}
\rightarrow
\text{Domain}
\rightarrow
\text{DNS}
\rightarrow
\text{IP}
\rightarrow
\text{TCP}
\rightarrow
\text{HTTP Request}
\rightarrow
\text{HTTP Response}
\rightarrow
\text{Rendering}
$$

HTTP is therefore the layer through which the browser and web server
exchange web messages.

------------------------------------------------------------------------

# 2. What Is an HTTP Request?

An **HTTP request** is a message sent by the client to a server.

Conceptually:

$$
\boxed{\text{Client}}
\xrightarrow{\text{HTTP Request}}
\boxed{\text{Server}}
$$

The browser constructs the request after the TCP connection has been
established.

A request can contain:

1.  A request line
2.  Headers
3.  A message body, when applicable

Example:

``` http
GET / HTTP/1.1
Host: www.google.com
Connection: keep-alive
Accept: application/html, */*
User-Agent: Mozilla/5.0 ...
```

------------------------------------------------------------------------

# 3. The Request Line

The first line of an HTTP request contains important information.

``` http
GET / HTTP/1.1
```

It contains:

``` text
GET      → method
/        → path
HTTP/1.1 → HTTP version
```

### Method

The method indicates the purpose of the request.

Here:

``` text
GET
```

means the client is requesting information.

### Path

``` text
/
```

is the root path of the website.

Other examples:

``` text
/
 /login
 /profile
 /users/123
 /api/users
```

A website's content can therefore be accessed through different paths.

### HTTP Version

``` text
HTTP/1.1
```

indicates the HTTP version being used for the request.

------------------------------------------------------------------------

# 4. HTTP Headers

Headers provide additional information about an HTTP message.

Example:

``` http
Host: www.google.com
Connection: keep-alive
Accept: application/html, */*
User-Agent: Mozilla/5.0 ...
```

A useful mental model is:

``` text
HTTP message
│
├── Start line
├── Headers
└── Body (when present)
```

Headers are extremely important in web security because applications
frequently make security decisions based on information contained in
requests and responses.

------------------------------------------------------------------------

# 5. The Host Header

Example:

``` http
Host: www.google.com
```

The `Host` header identifies which domain the request is intended for.

This is important because multiple domains can be hosted on the same IP
address.

Conceptually:

$$
\text{One IP Address}
\rightarrow
\begin{cases}
\text{example.com}\\
\text{example.org}\\
\text{another-site.com}
\end{cases}
$$

The `Host` header helps the server determine which site should handle
the request.

### Bug-bounty relevance

When examining requests in Burp Suite, the `Host` header is one of the
first pieces of information worth understanding.

It can also become relevant to vulnerabilities involving:

-   host-based routing
-   virtual hosts
-   URL generation
-   password-reset links
-   access-control assumptions

Those vulnerabilities are beyond Chapter 1, so don't try to learn them
yet. The important thing now is understanding what the header does.

------------------------------------------------------------------------

# 6. The Connection Header

Example:

``` http
Connection: keep-alive
```

The book explains that this indicates a request to keep the connection
open.

Keeping a connection open can avoid the overhead of repeatedly opening
and closing connections.

Conceptually:

``` text
Without keeping connection open:

Request → connection
Response → close
Request → new connection
Response → close
...

With persistent connection:

Request ─┐
Response ├── same connection
Request ─┤
Response ┘
```

The exact connection behavior depends on the HTTP version and
implementation, but the chapter's important point is understanding why
this header appears.

------------------------------------------------------------------------

# 7. The Accept Header

Example:

``` http
Accept: application/html, */*
```

The `Accept` header tells the server which response formats the client
is willing to receive.

The book mentions several content types that you will commonly
encounter:

``` text
application/html
application/json
application/octet-stream
text/plain
```

The wildcard:

``` text
*/*
```

means that the client is willing to accept any media type.

------------------------------------------------------------------------

# 8. The User-Agent Header

Example:

``` http
User-Agent: Mozilla/5.0 ...
```

The `User-Agent` identifies the software responsible for sending the
request.

For example, the request may have been generated by:

-   Chrome
-   Firefox
-   Safari
-   another browser
-   a script
-   a security tool

### Bug-bounty relevance

A User-Agent is just another HTTP header.

Therefore, when testing an application, it is important to recognize
that **headers are client-controlled input** unless the server has some
independent way to verify their contents.

That does not automatically mean changing a User-Agent creates a
vulnerability. It simply means you should understand what information
the application is receiving.

------------------------------------------------------------------------

# 9. HTTP Request Body

Not every HTTP request has a body.

For example, a simple GET request commonly looks like:

``` http
GET /profile HTTP/1.1
Host: example.com
```

A POST request may contain data in its body:

``` http
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

username=alice&password=example
```

The exact format of the body depends on the request's `Content-Type`.

The book later demonstrates POST requests and explains how the
`Content-Type` header describes how the request body is encoded.

------------------------------------------------------------------------

# 10. HTTP Methods

HTTP defines request methods that indicate the purpose of a request.

The book lists:

``` text
GET
HEAD
POST
PUT
DELETE
TRACE
CONNECT
OPTIONS
```

It also discusses `PATCH`, noting its status in the HTTP standards at
the time the book was written.

The important point is:

> A request method communicates what kind of action the client is asking
> the server to perform.

------------------------------------------------------------------------

# 11. GET

The `GET` method retrieves information identified by the request URI.

Example:

``` http
GET /profile HTTP/1.1
Host: example.com
```

Conceptually:

$$
\text{GET}
\rightarrow
\text{Retrieve resource}
$$

The book emphasizes that GET requests **should not alter data**.

For example:

``` text
GET /profile
```

should retrieve the profile rather than modifying it.

This distinction becomes important later in the book when discussing
**CSRF**.

The book also notes that visiting a URL or clicking a normal website
link causes the browser to send a GET request, which is relevant to
**Open Redirect** vulnerabilities.

------------------------------------------------------------------------

# 12. HEAD

`HEAD` is similar to `GET`, except the server should not return a
message body.

Conceptually:

``` text
GET
 ├── Response headers
 └── Response body

HEAD
 └── Response headers
     (no response body)
```

It can therefore be useful when you need information about a resource
without receiving the resource itself.

------------------------------------------------------------------------

# 13. POST

`POST` invokes some function on the receiving server.

For example, it might:

-   create a comment
-   register a user
-   perform some backend action
-   submit information

Example:

``` http
POST /comment HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

comment=Hello
```

The exact action depends on how the server implements the endpoint.

The book also emphasizes that a POST does not guarantee that the server
successfully performed the intended action; the request could result in
an error.

------------------------------------------------------------------------

# 14. PUT

`PUT` invokes a function referring to an existing record/resource.

For example, it might be used to update:

``` text
an account
a blog post
a resource
```

Conceptually:

$$
\text{PUT}
\rightarrow
\text{Update/replace an existing resource}
$$

The exact behavior depends on the application's implementation.

------------------------------------------------------------------------

# 15. DELETE

`DELETE` requests that the server delete a remote resource identified by
a URI.

Example:

``` http
DELETE /users/123 HTTP/1.1
Host: example.com
```

Conceptually:

$$
\text{DELETE}
\rightarrow
\text{Request deletion of a resource}
$$

Again, the server's implementation determines what actually happens.

------------------------------------------------------------------------

# 16. TRACE

`TRACE` is an uncommon HTTP method.

The book describes it as reflecting the request message back to the
requester.

This can allow the requester to see what the server receives and can be
useful for testing and diagnostic purposes.

Conceptually:

$$
\text{TRACE Request}
\xrightarrow{\text{Server}}
\text{Reflected Request}
$$

You don't need to memorize exploitation techniques involving TRACE yet.

Just know what the method is intended to do.

------------------------------------------------------------------------

# 17. CONNECT

`CONNECT` is associated with proxies.

The book describes it as starting two-way communication with a requested
resource through a proxy.

For example, it can be used by a proxy to access websites using HTTPS.

Conceptually:

``` text
Client
  ↓
Proxy
  ↓
Target
```

The proxy forwards the connection toward the requested resource.

------------------------------------------------------------------------

# 18. OPTIONS

`OPTIONS` asks a server about the communication options available.

For example, a server may indicate that it accepts methods such as:

``` text
GET
POST
PUT
DELETE
OPTIONS
```

Example:

``` http
OPTIONS / HTTP/1.1
Host: example.com
```

The book also discusses **preflight OPTIONS requests**, particularly in
the context of requests involving specific content types such as
`application/json`.

This becomes important later when studying **CSRF protections**.

------------------------------------------------------------------------

# 19. URI vs URL

The book makes a small but useful distinction.

A **URI** identifies a resource.

A **URL** is a type of URI that also specifies how to locate that
resource through its network location.

For example:

``` text
/website/file.txt
```

can be a URI.

While:

``` text
http://www.example.com/website/file.txt
```

is a URL.

The book uses the term **URL** throughout for simplicity.

------------------------------------------------------------------------

# 20. The HTTP Response

After receiving the request, the server sends an HTTP response.

Example:

``` http
HTTP/1.1 200 OK
Content-Type: text/html

<html>
<head>
<title>Example</title>
</head>
<body>
Hello
</body>
</html>
```

Conceptually:

$$
\boxed{\text{Client}}
\xrightarrow{\text{Request}}
\boxed{\text{Server}}
\xrightarrow{\text{Response}}
\boxed{\text{Client}}
$$

A response can be thought of as:

``` text
HTTP Response
│
├── Status line
├── Headers
└── Body
```

------------------------------------------------------------------------

# 21. Status Codes

The first part of a response contains a status code.

Example:

``` http
HTTP/1.1 200 OK
```

The book explains that status codes generally fall into these groups:

  Range   General meaning
  ------- -------------------
  `2xx`   Successful
  `3xx`   Redirection
  `4xx`   User/client error
  `5xx`   Server error

### 2xx

Usually indicates success.

Example:

``` text
200 OK
```

### 3xx

Usually indicates a redirect.

Examples:

``` text
301
302
```

### 4xx

Usually indicates an error associated with the request.

Example:

``` text
403
```

The book gives `403` as an example where a request lacks proper
identification to authorize access to content.

### 5xx

Usually indicates a server-side error.

Example:

``` text
503
```

indicating that the server is unavailable to handle the request.

------------------------------------------------------------------------

# 22. Do Not Trust the Status Code Alone

This is a particularly useful bug-bounty lesson from the chapter.

The book explains that applications do not strictly have to implement
status codes perfectly.

Therefore, an application might return:

``` http
HTTP/1.1 200 OK
```

while the response body actually contains an application error.

So when testing an application:

``` text
Status code
      +
Response headers
      +
Response body
      +
Behavior
```

should be considered together.

A `200` does not automatically mean:

> "Everything worked."

------------------------------------------------------------------------

# 23. Response Headers

A response can contain headers that tell the browser how to interpret
the response.

Example:

``` http
Content-Type: text/html
```

The book specifically discusses `Content-Type`.

------------------------------------------------------------------------

# 24. Content-Type

`Content-Type` tells the browser the media type of the response body.

Examples from the book include:

``` text
text/html
application/json
application/octet-stream
text/plain
```

For example:

``` http
Content-Type: text/html
```

indicates that the response body is HTML.

Conceptually:

$$
\text{Content-Type}
\rightarrow
\text{How the body should be interpreted}
$$

------------------------------------------------------------------------

# 25. MIME Sniffing

The book explains that browsers do not always blindly trust the returned
`Content-Type`.

A browser can perform **MIME sniffing** by examining the beginning of
the response body and determining what type of content it appears to
contain.

The book mentions:

``` http
X-Content-Type-Options: nosniff
```

as a header applications can use to disable this behavior.

This is an example of why HTTP headers matter for web security.

------------------------------------------------------------------------

# 26. Response Body

The response body contains the content associated with the response.

For a normal webpage it may contain HTML:

``` html
<html>
    <body>
        <h1>Hello</h1>
    </body>
</html>
```

But the body could also contain:

``` text
JSON
Files
Plain text
Other data
```

For example, an API might return:

``` json
{
  "username": "alice"
}
```

So:

$$
\text{Response Body}
\neq
\text{HTML only}
$$

------------------------------------------------------------------------

# 27. Redirects

A `3xx` response generally tells the browser to make another request.

For example:

``` http
HTTP/1.1 301 Found
Location: https://www.google.com/
```

The `Location` header tells the browser where to make the next request.

Conceptually:

$$
\text{Request A}
\rightarrow
\text{3xx Response}
\rightarrow
\text{Location}
\rightarrow
\text{Request B}
$$

The book distinguishes:

``` text
301 → permanent redirect
302 → temporary redirect
```

Redirects become directly relevant in **Chapter 2: Open Redirect**.

------------------------------------------------------------------------

# 28. Rendering the Response

After receiving a successful HTML response, the browser begins rendering
the content.

The response body can contain:

### HTML

Defines the page structure.

``` html
<h1>Hello</h1>
```

### CSS

Controls styling and layout.

### JavaScript

Adds dynamic behavior.

The simplified relationship is:

$$
\text{HTML}
+
\text{CSS}
+
\text{JavaScript}
\rightarrow
\text{Rendered Web Page}
$$

------------------------------------------------------------------------

# 29. Additional HTTP Requests During Rendering

A webpage can reference external resources.

For example:

``` text
HTML
 │
 ├── CSS file
 ├── JavaScript file
 ├── image
 └── video
```

The browser may therefore make additional HTTP requests to retrieve
those resources.

Conceptually:

$$
\text{Initial HTML}
\rightarrow
\begin{cases}
\text{Request CSS}\\
\text{Request JavaScript}\\
\text{Request Images}\\
\text{Request Other Media}
\end{cases}
$$

This is an important security concept because a single page can cause
many HTTP requests.

------------------------------------------------------------------------

# 30. JavaScript and the Browser

The book introduces JavaScript as a scripting language supported by
major browsers.

JavaScript can:

-   update page content without reloading
-   respond to events
-   store values in variables
-   execute functions
-   interact with browser APIs

This means the browser is not simply displaying static HTML.

It is executing code that can interact with the page and the browser
environment.

------------------------------------------------------------------------

# 31. The DOM

One of the most important browser concepts introduced in Chapter 1 is
the **Document Object Model (DOM)**.

The DOM represents the webpage's HTML structure in a form that
JavaScript can access and manipulate.

Conceptually:

``` text
HTML
 ↓
DOM
 ↓
JavaScript
 ↓
Modify page
```

JavaScript can use the DOM to manipulate:

-   HTML
-   CSS
-   page content

The book points out why this matters for security:

> If an attacker can execute their own JavaScript on a site, they can
> access the DOM and perform actions on the site on behalf of the
> targeted user.

This concept becomes important later in the book's **Cross-Site
Scripting (XSS)** chapter.

------------------------------------------------------------------------

# 32. HTTP Is Stateless

HTTP requests are **stateless**.

That means each request is treated as a new request.

Conceptually:

``` text
Request 1 → Response 1

Request 2 → Response 2

Request 3 → Response 3
```

The server does not inherently remember the previous communication when
receiving the next HTTP request.

Formally:

$$
\text{Request}_{n}
\not\Rightarrow
\text{Automatic knowledge of Request}_{n-1}
$$

------------------------------------------------------------------------

# 33. Why Statelessness Is a Problem for Websites

Imagine logging into a website.

Without some mechanism for maintaining state, you would have to provide
your username and password again for every request.

For example:

``` text
Request 1 → "I am Alice"
Request 2 → "I am Alice"
Request 3 → "I am Alice"
Request 4 → "I am Alice"
```

That would be impractical.

Websites therefore use additional mechanisms to maintain state.

The book specifically mentions:

-   Cookies
-   Basic authentication

Cookies are discussed in more detail later in the book.

------------------------------------------------------------------------

# 34. Cookies and HTTP State

The important conceptual relationship is:

``` text
HTTP itself
    ↓
Stateless

Website needs to remember user
    ↓
Use additional mechanism
    ↓
Cookies / authentication
```

So:

$$
\text{Stateless HTTP}
+
\text{State mechanism}
\rightarrow
\text{Practical web session}
$$

This becomes important when you later study:

-   authentication
-   CSRF
-   session handling
-   access control
-   other web vulnerabilities

------------------------------------------------------------------------

# 35. Base64 Note

At the end of Chapter 1, the book briefly mentions **Base64-encoded
content**.

The book says the specifics of Base64 encoding are beyond the chapter's
scope, but you will likely encounter Base64-encoded content while
hacking.

Therefore:

> If you encounter something that appears to be Base64 encoded, decode
> it rather than assuming it is encrypted.

This is a practical bug-hunting habit introduced by the chapter.

------------------------------------------------------------------------

# 36. Complete HTTP Mental Model

Put everything together:

``` text
                    Browser
                       │
                       │
                 HTTP Request
                       │
                       ▼
                  Web Server
                       │
                       │
                 HTTP Response
                       │
                       ▼
                    Browser
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
         HTML         CSS       JavaScript
          │            │            │
          └────────────┼────────────┘
                       ▼
                      DOM
                       │
                       ▼
                 Rendered Page
```

And the full network process:

``` text
URL
 ↓
Domain
 ↓
DNS
 ↓
IP Address
 ↓
TCP Connection
 ↓
HTTP Request
 ↓
Server
 ↓
HTTP Response
 ↓
Browser
 ↓
Rendering
 ↓
Additional HTTP Requests
```

------------------------------------------------------------------------

# 37. HTTP and Bug Hunting

This is where the foundation becomes directly useful.

A bug hunter spends a lot of time asking:

> **What is the application receiving from me?**

and:

> **What is the application sending back?**

For example:

``` http
GET /profile?id=123 HTTP/1.1
Host: example.com
Cookie: session=...
```

You can conceptually break this into:

``` text
Method     → GET
Path       → /profile
Parameter  → id=123
Host       → example.com
Cookie     → session=...
```

A security tester may then investigate how changing these inputs affects
the application's behavior.

For example:

``` text
id=123
 ↓
id=124
```

or:

``` text
GET
 ↓
POST
```

or:

``` text
Header A
 ↓
Modified Header A
```

The point is **not** that every modification is a vulnerability.

The point is to understand that HTTP gives you a structured interface
through which the application receives input.

------------------------------------------------------------------------

# 38. Why Burp Suite Will Become Important

Later, when you use Burp Suite, you will essentially be looking at this
communication:

``` text
Browser
   │
   ▼
Burp Suite
   │
   ▼
Server
```

Instead of treating the browser as a black box, you can inspect the HTTP
requests and responses.

That lets you ask:

``` text
What request was sent?
        ↓
What can I change?
        ↓
How does the server respond?
        ↓
Did the application's behavior change?
        ↓
Is the new behavior unauthorized or unintended?
```

This is one of the fundamental transitions from **using a website** to
**testing a website**.

------------------------------------------------------------------------

# 39. Security-Relevant HTTP Concepts to Remember

From this chapter, pay particular attention to:

``` text
HTTP methods
     ↓
GET / POST / PUT / DELETE / etc.

Headers
     ↓
Host
User-Agent
Accept
Content-Type
Connection
Location
X-Content-Type-Options

Status codes
     ↓
2xx / 3xx / 4xx / 5xx

Request body
     ↓
Data sent to the server

Response body
     ↓
Data returned by the server

Statelessness
     ↓
Cookies / authentication maintain state

Rendering
     ↓
HTML + CSS + JavaScript + DOM
```

These aren't just definitions. They become building blocks for
understanding the vulnerabilities in the rest of the book.

------------------------------------------------------------------------

# 40. Key Takeaways

-   HTTP is the protocol through which web clients and servers exchange
    messages.
-   An HTTP request contains a request line, headers, and potentially a
    body.
-   The request line contains a method, path, and HTTP version.
-   The `Host` header identifies the intended domain.
-   `Accept` describes response formats the client can accept.
-   `User-Agent` identifies the software sending the request.
-   HTTP defines methods including `GET`, `HEAD`, `POST`, `PUT`,
    `DELETE`, `TRACE`, `CONNECT`, and `OPTIONS`.
-   `GET` retrieves information and should not modify data.
-   `POST` typically invokes a server-side action.
-   `PUT` can refer to updating an existing resource.
-   `DELETE` requests deletion of a resource.
-   `HEAD` is like GET without a response body.
-   `TRACE` reflects the request.
-   `CONNECT` is associated with proxy communication.
-   `OPTIONS` asks about supported communication options.
-   A response contains a status line, headers, and potentially a body.
-   `2xx`, `3xx`, `4xx`, and `5xx` represent broad classes of response
    status.
-   A `200` response does not necessarily mean the application operation
    succeeded.
-   `Content-Type` describes the response body's media type.
-   Browsers may perform MIME sniffing.
-   `Location` is used with redirects.
-   A webpage can trigger additional HTTP requests for CSS, JavaScript,
    images, and other resources.
-   JavaScript can interact with the DOM.
-   HTTP is stateless.
-   Cookies and authentication mechanisms can provide state across
    otherwise independent HTTP requests.
-   Understanding HTTP requests and responses is one of the most
    important foundations for web bug hunting.

---

## Source

*Real-World Bug Hunting: A Field Guide to Web Hacking* — Chapter 1, **Bug Bounty Basics**, "What Happens When You Visit a Website — Step 4: Sending an HTTP Request," "Step 5: Server Response," "Step 6: Rendering the Response," "HTTP Requests," "Request Methods," and "HTTP Is Stateless."
