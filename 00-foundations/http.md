# HTTP

## 1. What Is HTTP?

**HTTP (Hypertext Transfer Protocol)** is the protocol used for
communication between a web client and a web server.

In the website-visit process from Chapter 1, HTTP comes after:

``` text
URL
 ↓
Domain
 ↓
DNS
 ↓
IP address
 ↓
TCP connection
 ↓
HTTP
```

Once the TCP connection has been established, the browser can send an
HTTP request to the server.

------------------------------------------------------------------------

## 2. The HTTP Request

A browser sends an HTTP request to ask a server to perform an action or
provide information.

The book gives this example:

``` http
GET / HTTP/1.1
Host: www.google.com
Connection: keep-alive
Accept: application/html, */*
User-Agent: Mozilla/5.0 ...
```

A simplified model is:

$$
\boxed{\text{Client}}
\xrightarrow{\text{HTTP Request}}
\boxed{\text{Server}}
$$

------------------------------------------------------------------------

## 3. Understanding a Request

### Request Line

The first line contains:

``` http
GET / HTTP/1.1
```

It tells the server:

-   **Method:** `GET`
-   **Path:** `/`
-   **HTTP version:** `HTTP/1.1`

The `/` represents the **root path** of the website.

For example:

``` text
/
├── about
├── login
└── users
```

would conceptually represent different paths within a website.

### Host Header

``` http
Host: www.google.com
```

The `Host` header identifies which domain the request is intended for.

This matters because a single IP address can host multiple domains.

Conceptually:

$$
\text{One IP}
\rightarrow
\begin{cases}
\text{domain A}\\
\text{domain B}\\
\text{domain C}
\end{cases}
$$

The `Host` header helps the server determine which domain should handle
the request.

### Connection Header

``` http
Connection: keep-alive
```

This indicates that the client wants to keep the connection open rather
than repeatedly opening and closing connections.

### Accept Header

``` http
Accept: application/html, */*
```

The `Accept` header indicates which response formats the client is
willing to receive.

The book highlights several content types you will commonly encounter:

``` text
application/html
application/json
application/octet-stream
text/plain
```

### User-Agent

``` http
User-Agent: Mozilla/5.0 ...
```

The `User-Agent` identifies the software responsible for sending the
request, such as a web browser.

------------------------------------------------------------------------

## 4. HTTP Methods

HTTP requests use **methods** to describe what the client wants to do.

The book introduces the idea using `GET`:

``` http
GET / HTTP/1.1
```

A `GET` request retrieves information.

For example:

``` http
GET /profile HTTP/1.1
Host: example.com
```

The server may respond with the requested profile page.

> **Important:** The security significance of HTTP methods becomes much
> clearer later in the book. For example, using `GET` for state-changing
> actions can create security problems such as CSRF.

------------------------------------------------------------------------

## 5. The HTTP Response

After receiving a request, the server sends an HTTP response.

The book gives an example like:

``` http
HTTP/1.1 200 OK
Content-Type: text/html

<html>
<head>
<title>Google.com</title>
</head>
<body>
...
</body>
</html>
```

The basic flow is:

$$
\boxed{\text{Client}}
\xrightarrow{\text{Request}}
\boxed{\text{Server}}
\xrightarrow{\text{Response}}
\boxed{\text{Client}}
$$

------------------------------------------------------------------------

## 6. Status Codes

The response begins with a status code.

Example:

``` http
HTTP/1.1 200 OK
```

The book explains that HTTP status codes are generally three-digit
numbers beginning with:

-   `2` --- typically successful
-   `3` --- typically redirects
-   `4` --- typically client-side errors
-   `5` --- typically server-side errors

### Common examples

     Code Meaning
  ------- ---------------------
    `200` Successful response
    `301` Permanent redirect
    `302` Temporary redirect
    `404` Resource not found
    `500` Server error

The book also makes an important point:

> There is no strict enforcement requiring applications to use status
> codes perfectly.

Therefore, an application might return `200` even when the response body
actually describes an application error.

For bug bounty, **don't rely only on the status code**. Inspect the
response body and other response details too.

------------------------------------------------------------------------

## 7. Response Headers

A response can contain headers that provide additional information about
the response.

For example:

``` http
Content-Type: text/html
```

The `Content-Type` header tells the browser what kind of data is
contained in the response body.

Common examples include:

``` text
text/html
application/json
text/plain
application/octet-stream
```

------------------------------------------------------------------------

## 8. The HTTP Message Body

The **message body** contains the actual content associated with a
request or response.

For a normal web page, the response body is often HTML:

``` html
<html>
    <body>
        <h1>Hello</h1>
    </body>
</html>
```

But the body could also contain:

-   JSON from an API
-   File contents
-   Plain text
-   Other data formats

So:

$$
\text{HTTP Response}
=
\text{Status Line}
+
\text{Headers}
+
\text{Body}
$$

------------------------------------------------------------------------

## 9. Content-Type and MIME Sniffing

The `Content-Type` header tells the browser the media type of the
response body.

For example:

``` http
Content-Type: text/html
```

However, the book explains that browsers may perform **MIME sniffing**,
examining the beginning of the response body themselves to determine its
type.

The book mentions that applications can disable this browser behavior
with:

``` http
X-Content-Type-Options: nosniff
```

This is an important security-related HTTP header to recognize.

------------------------------------------------------------------------

## 10. Redirects

Responses beginning with `3` generally indicate a redirect.

For example:

``` http
HTTP/1.1 301 Moved Permanently
```

or:

``` http
HTTP/1.1 302 Found
```

A redirect instructs the browser to make another request.

Conceptually:

$$
\text{Request A}
\rightarrow
\text{301/302 Response}
\rightarrow
\text{Request B}
$$

This becomes particularly relevant later in the book when studying
**Open Redirect vulnerabilities**.

------------------------------------------------------------------------

## 11. HTTP Is Stateless

HTTP itself is **stateless**.

This means that each HTTP request is treated independently; HTTP does
not inherently remember what happened in previous requests.

Conceptually:

``` text
Request 1 → Response 1

Request 2 → Response 2

Request 3 → Response 3
```

HTTP does not inherently know that all three requests came from the same
user.

Web applications therefore use additional mechanisms to maintain state,
such as cookies and sessions.

This concept becomes important when studying authentication,
authorization, CSRF, and other web vulnerabilities.

------------------------------------------------------------------------

## 12. Why HTTP Matters for Bug Bounty

This is one of the most important foundations for web hacking.

A bug hunter will frequently inspect HTTP requests and responses and
ask:

> **"What happens if I change something?"**

For example:

``` http
GET /profile?id=123 HTTP/1.1
Host: example.com
```

You might investigate what happens when an input, header, path, method,
or other request component is modified.

This is why tools such as **Burp Suite** are so useful: they allow you
to observe and manipulate HTTP traffic between the client and server.

Conceptually:

$$
\text{Normal Request}
\xrightarrow{\text{Modify}}
\text{Modified Request}
\xrightarrow{\text{Server}}
\text{Unexpected Behavior?}
$$

Many web vulnerabilities ultimately involve the application handling
attacker-controlled HTTP input in an unsafe way.

------------------------------------------------------------------------

## 13. HTTP Mental Model

Keep this complete flow in your head:

``` text
                 DNS
                  ↓
Domain ───────→ IP Address
                  ↓
             TCP Connection
                  ↓
           HTTP Request
                  ↓
              Web Server
                  ↓
           HTTP Response
                  ↓
              Browser
                  ↓
              Rendering
```

Or:

$$
\text{URL}
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

This is the foundation on which the rest of the book builds.

------------------------------------------------------------------------

## 14. Key Takeaways

-   HTTP is the protocol used for web communication.
-   The browser sends an **HTTP request** to a server.
-   The server returns an **HTTP response**.
-   A request contains a request line and headers, and may contain a
    body.
-   The request line contains the **method, path, and HTTP version**.
-   The `Host` header identifies the intended domain.
-   `GET` retrieves information.
-   A response contains a status line, headers, and usually a body.
-   Status codes generally fall into `2xx`, `3xx`, `4xx`, and `5xx`
    groups.
-   `Content-Type` describes the response body's media type.
-   `3xx` responses generally indicate redirects.
-   HTTP is stateless.
-   Understanding HTTP requests and responses is fundamental to web
    vulnerability testing.

------------------------------------------------------------------------

## Source

*Real-World Bug Hunting: A Field Guide to Web Hacking* --- Chapter 1,
**Bug Bounty Basics**, "What Happens When You Visit a Website --- Step
4: Sending an HTTP Request," "Step 5: Server Response," "Step 6:
Rendering the Response," "HTTP Requests," "Request Methods," and "HTTP
Is Stateless."
